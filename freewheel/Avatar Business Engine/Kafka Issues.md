# Kafka Issues

[TOC]

## Kafka Send ERROR：dial tcp 172.20.0.4:9092: i/o timeout 

这是一种kafka和主机程序处于不同网络环境下会发生的问题！（比如，docker内的kafka容器 & localhost的client程序）

说明：

首先，我们需要在producer/consumer这些client中为kafka配置**代理ip**，以便client能找到相应的kafka服务。可是这里的代理ip只是让client与kafka建立**initial connection**，之后kafka会在这次connection中返回关于partition的leader broker的metadata（包括broker的**实际ip**）。而后client在实际produce或者consume时，才会再尝试去连接相应topic的metadata中的broker实际ip。所以，当实际broker ip与client ip在同一局域网下能直连的时候，不会出现任何问题，以上细节对用户是透明的；但是两者若不在同一局域网下，就无法ping通，生产或者消费就会失败。

解决：

思路是“把metadate返回的实际ip经过一层转义，暴露给client可以直连的局域网ip”，更改kafka的listener相关配置。其中，listener定义的是kafka服务监听的ip和port，advertised_listener定义的则是对应listener分别对外监听和对外返回的ip和port，kafka_inter_broker_listener_name定义的是kafka服务内部的监听配置名。

举例：

当kafka:9092收到连接请求，将会映射到kafka:9092上进行服务，且返回的broker metadata为kafka:9092；当localhost:9094收到连接请求，将会映射到kafka:9094上进行服务，且返回的broker metadata为localhost:9094。

而kafka_inter_broker_listener_name为INTERNAL，表示的是：INTERNAL定义的这一组互为映射的listener是kafka在局域网内部使用的配置（比如在启动Kafka服务，创建partition配置lead的时候）。

![image-20201127111807026](file:///Users/lugao/Library/Application%20Support/typora-user-images/image-20201127111807026.png?lastModify=1616125200)

参考：https://www.confluent.io/blog/kafka-listeners-explained/



## Kafka Topic

topic： 

- ssp-de-external-ad
- ssp-se-seat
- ssp-se-campaign



## Kafka Lag Monitor

