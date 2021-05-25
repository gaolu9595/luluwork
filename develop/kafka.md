1. topic是逻辑概念，topic下的partitions才是实际的物理概念
2. partitions之间的消息是无序的，partition内部的消息是有序的
3. 同一consumer group中的consumers不能消费同一个partition的信息，因此consumer数目要小于partition数目
4. producer负责将消息分配给topic的某个partition，consumer负责按规则从partition中获取指定topic的消息
5. consumer group可以保证某条消息只会被其中某一个consumer所消费
6. 每个partitiin中包含若干个LogSegment，是一个逻辑概念，实际对应着一个具体的.log日志文件和.index索引文件
7. offset是partition内的全局偏移量，用于在partition内唯一标识消息
8. mesaage是kafka中存储的最小单位，即为一个commit log，由一个固定长度的消息头和一个可变长度的消息体组成



## 常用命令

```shell
kafka-topics.sh --describe --zookeeper zookeeper:2181 --topic ExternalAdService

kafka-console-producer.sh --broker-list kafka:9092 --topic ExternalAdService

kafka-consumer-groups.sh --bootstrap-server kafka:9092 --list

kafka-consumer-groups.sh --bootstrap-server kafka:9092 --describe --group test

kafka-console-consumer.sh --bootstrap-server ac03a8b2bbbf:9092 --topic mytest --from-beginning （使用默认的consumer-group进行消费）

kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list ac03a8b2bbbf:9092 -topic mytest --time -1

kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list ac03a8b2bbbf:9092 -topic mytest --time -2
```



## 一些框架

**golang-sarama** consumer源码分析https://zhuanlan.zhihu.com/p/110114004

上篇笔记过了一遍 ConsumerGroup 的 Rebalance 协议，这里过一下消费的流程。

consumer 的结构体很简单，里面主要是 partitionConsumer 和 brokerConsumer 两个表：

```text
type consumer struct {
	conf            *Config
	children        map[string]map[int32]*partitionConsumer
	brokerConsumers map[*Broker]*brokerConsumer
	client          Client
	lock            sync.Mutex
}
```

从上篇笔记中记得 ConsumePartition() 是消费的入口，看代码大约是初始化一个 partitionConsumer，并启动 partitionConsumer 的 dispatcher 和 responseFeeder 两个 goroutine，将 brokerConsumer 的引用交给 partitionConsumer。

注释中提到除非调用 AsyncClose() 或者 Close() 方法，ConsumePartition() 启动的消费过程会永不停止，遇到错误，就不停重试，如果开启 Consumer.Return.Errors，则可以将收到的错误交给 Errors 这个 channel 允许用户处理。

brokerConsumer 是什么呢？当 Consumer 需要从 Broker 订阅多个 Topic 时，会使用单独的一个连接来消费数据，再将数据按 partition 分给不同的 partitionConsumer。Consumer、partitionConsumer、brokerConsumer 乃至 broker 之间大致上似乎是这样的关系：

![img](https://pic2.zhimg.com/80/v2-ac9542728e1d644db702d7435f1bcc5d_1440w.jpg)



整理一下 partitionConsumer 与 brokerConsumer 之间的交互还是有点复杂的，大概是：

1. partitionConsumer 通过 broker.input 这个 chan 加入 brokerConsumer 的订阅；
2. 加入订阅后，brokerConsumer 每次 fetchNewMessages 得到一批消息，会发给每个 partitionConsumer 的 feeder chan；
3. 每个 partitionConsumer 解析 feeder chan 的数据得到 对应的消息列表，同时设置自身的 responseResult，设置 brokers.acks 这个 WaitGoup 执行 Done()，表示处理完毕该批次；
4. brokerConsumer 等待每个 partitonConsumer 处理完毕该批次，处理所有 partitionConsumer 的 responseResult 后循环 fetchNewMessages；

![img](https://pic2.zhimg.com/80/v2-e0e26a57653519ab3d92bff7f4b9d4d5_1440w.jpg)

这里的交互还是比较 go 特色的，直觉上 java 原版客户端该不会做这么麻烦的交互。

brokerConsumer 的 input chan 有点像服务端的 accept，会有多个 partitionConsumer 动态加入、解除对 brokerConsumer 的订阅。

brokerConsumer 会负责发起 FetchRequest 的主循环，每轮迭代拉取一批消息，发送给每个 partitionConsumer 再转给用户的 messages chan。通过 acks 这个 WaitGroup 来协调每个 partitionConsumer 对这一批次的消息的处理节奏。

如果用户消费 messages chan 有超时，parseResponse 会返回 errTimeout，brokerConsumer 会依据 errTimeout 而暂停 fetchRequest。

## partitionConsumer

partitonConsumer 会启动 dispatcher 和 responseFeeder 两个 goroutine，其中 dispatcher goroutine 用于跟踪 broker 的变化，偏元信息性质的控制侧，而 responseFeeder 用于跟踪消息的到来，偏数据侧。

```text
func (child *partitionConsumer) dispatcher() {
	for range child.trigger {
		select {
		case <-child.dying:
			close(child.trigger)
		case <-time.After(child.computeBackoff()):
			if child.broker != nil {
				child.consumer.unrefBrokerConsumer(child.broker)
				child.broker = nil
			}

			Logger.Printf("consumer/%s/%d finding new broker\n", child.topic, child.partition)
			if err := child.dispatch(); err != nil {
				child.sendError(err)
				child.trigger <- none{}
			}
		}
	}

	if child.broker != nil {
		child.consumer.unrefBrokerConsumer(child.broker)
	}
	child.consumer.removeChild(child)
	close(child.feeder)
}

// ...

  func (child *partitionConsumer) dispatch() error {
      if err := child.consumer.client.RefreshMetadata(child.topic); err != nil {
          return err
      }

      var leader *Broker
      var err error
      if leader, err = child.consumer.client.Leader(child.topic, child.partition); err != nil {
          return err
      }

      child.broker = child.consumer.refBrokerConsumer(leader)

      child.broker.input <- child

      return nil
  }
```

dispatcher 这个 goroutine 用于发现 broker 的变化。它会侦听 dispatcher.trigger 这个 channel 的通知，来发现 Partition 的 Leader 变化。而 trigger 这个 channel 的更新来自 brokerConsumer 对象。

最后 child.broker.input child 这一句，相当于使 partitionConsumer 加入 brokerConsumer 的订阅。

不过这里不是很明白 dispatcher 这个 goroutine 里为啥没有对 partitionConsumer 对象上锁。

```text
func (child *partitionConsumer) responseFeeder() {
	var msgs []*ConsumerMessage
	expiryTicker := time.NewTicker(child.conf.Consumer.MaxProcessingTime)
	firstAttempt := true

feederLoop:
	for response := range child.feeder {
		msgs, child.responseResult = child.parseResponse(response)

		if child.responseResult == nil {
			atomic.StoreInt32(&child.retries, 0)
		}

		for i, msg := range msgs {
		messageSelect:
			select {
			case <-child.dying:
				child.broker.acks.Done()
				continue feederLoop
			case child.messages <- msg:
				firstAttempt = true
			case <-expiryTicker.C:
				if !firstAttempt {
					child.responseResult = errTimedOut
					child.broker.acks.Done()
				remainingLoop:
					for _, msg = range msgs[i:] {
						select {
						case child.messages <- msg:
						case <-child.dying:
							break remainingLoop
						}
					}
					child.broker.input <- child
					continue feederLoop
				} else {
					// current message has not been sent, return to select
					// statement
					firstAttempt = false
					goto messageSelect
				}
			}
		}

		child.broker.acks.Done()
	}

	expiryTicker.Stop()
	close(child.messages)
	close(child.errors)
}
```

child.feed 这个 channel 也是来自 brokerConsumer。大约是处理来自 brokerConsumer 的消息，转发给 messages chan。

值得留意有一个配置项目 child.conf.Consumer.MaxProcessingTime，默认值为 100ms，看注释它的意思是如果朝 messages chan 写入超过 100ms 仍未成功，则停止再向 Broker 发送 fetch 请求。这一流程的实现有一些细节：

1. 通过 firstAttempt 变量判断是否第一次超时，只有当第二次超时才跑暂停 Broker 发送 fetch 请求的流程
2. 设置 child.responseResult 为 errTimeout，以通知 brokerConsumer 暂停。
3. 将当前批次的消息列表消化掉，如果中途 partitionConsumer 退出，则停止消化当前批次的消息；
4. child.broker.input child，将 partitionConsumer 重新加入 brokerConsumer 的订阅；

## brokerConsumer

brokerConsumer 的结构体如下：

```text
type brokerConsumer struct {
      consumer         *consumer
      broker           *Broker
      input            chan *partitionConsumer
      newSubscriptions chan []*partitionConsumer
      subscriptions    map[*partitionConsumer]none
      wait             chan none
      acks             sync.WaitGroup
      refs             int
  }
```

初始化 brokerConsumer 时会产生两个 goroutine：

1. bc.subscriptionManager：侦听 input chan，获取加入订阅的 partitionConsumer；
2. bc.subscriptionConsumer：循环发起 FetchRequest，向 broker 拉取一批消息，协调发给 partitionConsumer；

subscriptionConsumer 相当于 FetchRequest 的主循环流程：

```text
//subscriptionConsumer ensures we will get nil right away if no new subscriptions is available
  func (bc *brokerConsumer) subscriptionConsumer() {
      <-bc.wait // wait for our first piece of work

      for newSubscriptions := range bc.newSubscriptions {
          bc.updateSubscriptions(newSubscriptions)

          if len(bc.subscriptions) == 0 {
              // We're about to be shut down or we're about to receive more subscriptions.
              // Either way, the signal just hasn't propagated to our goroutine yet.
              <-bc.wait
              continue
          }

          response, err := bc.fetchNewMessages()

          if err != nil {
              Logger.Printf("consumer/broker/%d disconnecting due to error processing FetchRequest: %s\n", bc.broker.ID(), err)
              bc.abort(err)
              return
          }

          bc.acks.Add(len(bc.subscriptions))
          for child := range bc.subscriptions {
              child.feeder <- response
          }
          bc.acks.Wait()
          bc.handleResponses()
      }
  }
```

而 subscriptionManager 用于管理订阅的加入，并通过 bc.newSubscriptions 来通知给 subscriptionConsumer。

```text
// The subscriptionManager constantly accepts new subscriptions on `input` (even when the main subscriptionConsumer
  // goroutine is in the middle of a network request) and batches it up. The main worker goroutine picks
  // up a batch of new subscriptions between every network request by reading from `newSubscriptions`, so we give
  // it nil if no new subscriptions are available. We also write to `wait` only when new subscriptions is available,
  // so the main goroutine can block waiting for work if it has none.
  func (bc *brokerConsumer) subscriptionManager() {
      var buffer []*partitionConsumer

      for {
          if len(buffer) > 0 {
              select {
              case event, ok := <-bc.input:
                  if !ok {
                      goto done
                  }
                  buffer = append(buffer, event)
              case bc.newSubscriptions <- buffer:
                  buffer = nil
              case bc.wait <- none{}:
              }
          } else {
              select {
              case event, ok := <-bc.input:
                  if !ok {
                      goto done
                  }
                  buffer = append(buffer, event)
              case bc.newSubscriptions <- nil:
              }
          }
      }

  done:
      close(bc.wait)
      if len(buffer) > 0 {
          bc.newSubscriptions <- buffer
      }
      close(bc.newSubscriptions)
  }
```

## 总结

- consumer 与 broker 算是个多对多的关系，如果有多个 partition 位于一个 broker，那么通过单个 brokerConsumer 与之统一交互。
- 因此 partitionConsumer 与 brokerConsumer 属于一个订阅的关系，partitonConsumer 关注的点是将自己加入订阅并处理订阅的内容，由 brokerConsumer 驱动 FetchRequest 循环；
- brokerConsumer 使用一个 WaitGroup 来协调多个 partitionConsumer 的执行节奏与结果。





## Kafka 

Header

Key for hash, value is main content

segment.byte 超过这个数了才会形成文件，所以有可能流量小的partition中的rentention不生效，很早以前的message可能都还存在

一个broker就是一个kafka server进程

ack的配置，控制producer生产消息的一致性

offset commit的配置，控制consumer消费消息的一致性

rebalance in consumer group



