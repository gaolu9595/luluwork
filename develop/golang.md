# Golang
[TOC]



## 实际应用

1. [Golang连接mysql数据库 - 简书](https://www.jianshu.com/p/ee87e989f149)
2. [使用Kafka(附Golang代码)](https://juejin.im/post/6844903903113248776) [golang 消费 kafka 的坑 | Wolfogre’s Blog](https://blog.wolfogre.com/posts/golang-consume-kafka/)
3. http://cngolib.com/index.html （go标准库中文文档）
## 基础语法
1. 句尾没有;
2. package为main的main()函数才能执行
3. fmt是输入输出库
4. 变量和方法定义时，类型放在变量名后面
	1. var name string = “LuGao”
	2. var age, height, weight int = 25, 162, 51
	3. nickname := “Lulu” (自动推断类型。⚠️ 左侧的变量不能是已经声明过的，否则会导致编译错误)
	4. const AREA = “China” (基本数据类型的常量定义时，可以不指定类型，系统可自行判断)
		特殊常量 iota 
		第一个 iota 等于 0，每当 iota 在新的一行被使用时，它的值都会自动加 1；所以 a=0, b=1, c=2 可以简写为如下形式：
		const (
   
    			a = iota
    			b
	 			c	
	 	)
	4. func doSomething (name string, age int) (string, string) {
		XXXXXXXXXXX
		}
	5. map集合的定义：
		1. var country map[int]string
		2. country := map[int]string
	6. 指针类型：
	var ptr *int
	var a int = 2
	ptr = &a	     (若不对ptr赋值，则ptr为nil)
	fmt.Println(\*ptr) （*指针获取a变量的值，为2）
	6. 未初始化的变量值：
		1. 数值类型（包括complex64/128）为 **0**
		2. 布尔类型为 **false**
		3. 字符串为 **””**（空字符串）
		4. 以下几种类型为 **nil**（空指针/null）：
		![68FB3DD7-41C7-4AB3-AA74-F0876F9D7404](/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.yimrvf/68FB3DD7-41C7-4AB3-AA74-F0876F9D7404.png)
5. 数组长度不可改变，切片是一种动态数组的抽象
	1. 数组定义时需要指定长度，或者使用[…]
	2. 切片定义时使用[]
		1. 不用make时，不赋值则切片为nil
		2. 用make时，不赋值则切片不为nil、元素值为default值
		3. append追加元素
		![C9A11B06-FE3A-4F79-AC91-DB0C6544B561](/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.eLKEDx/C9A11B06-FE3A-4F79-AC91-DB0C6544B561.png)
6. range范围的使用
<img src="/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.k4QzGh/2B4E155F-5BE8-48D7-9332-C9C3FA315FE4.png" alt="2B4E155F-5BE8-48D7-9332-C9C3FA315FE4" style="zoom:33%;" />
7. 面向对象编程（类、接口）
  1. type parentclass struct{

  	变量声明……
  }
  // 继承该类的子类
  type childclass stuct{
  	parentclass
  	变量声明……
  }
  // 该struct的相关方法定义
  func (c classname) funcname() {
      		XXXXXXXXXX
  }
  2. type interfacename interface{

  	方法声明……
  }
  实现一个接口，需要实现其中的所有方法
  4.  [与Go同行：Golang面向对象编程](https://code.tutsplus.com/zh-hans/tutorials/lets-go-object-oriented-programming-in-golang--cms-26540)
8. 错误处理，通过实现内置的Error接口来处理，使用errors.New 可返回一个错误信息
    type error interface {
   Error() string
    }
9. 并发编程：用go来开启一个goroutine线程，同一个程序中的所有goroutine共享一个地址空间[Go 并发 | 菜鸟教程](https://www.runoob.com/go/go-concurrent.html)

## Golang 项目开发

1. ![GO目录结构](https://static.bookstack.cn/projects/topgoer/801543302bbdbb6ba9efd76ff978fc65.png)
2. GOROOT：Golang的安装路径
3. GOPATH：GO大项目的路径（将从该path下的src中寻找source code），go get的依赖包存放的地方

## Go 包管理

1. GoPath：简单粗暴
2. GoVendor：在项目目录下，添加自己的vendor目录，从GOPATH中拷贝项目引用的依赖包到vendor目录下来进行管理
3. GoMod：golang1.12后支持，不依赖GoPATH和vendor目录，方便进行版本管理
4. Go get在GOPATH下不支持version管理，因此一定要注意！大坑（go: cannot use path@version syntax in GOPATH mode）
5. 

## Go常用package库

1. fmt：标准输入输出库
2. ioutil：io相关库
3. log：日志库
4. os：与平台无关的操作系统库（例：os.Stdin和os.Stdout）
5. strings
6. strconv
7. sql
8. golang控制并发的三种方法： https://www.flysnow.org/2017/05/12/go-in-action-go-context.html
9. 





## Golang的多线程

### channel支持线程间通信

channel本质是数据流动，分为阻塞和非阻塞channel两种

阻塞channel的读写不能在同一个线程中，否则会死锁

非阻塞channel的读写可以在同一个线程中，但是如果没有数据pull的话也会阻塞

### channel是线程安全的，并发首选

使用通信来共享内存，而不是使用共享内存来通信

在channel和mutex之间，golang推荐首选channel

channel是线程安全的，可以被多个goroutine同时读写，我们不需要考虑其数据冲突

### 优雅的检查channel的关闭

https://www.cnblogs.com/-wenli/p/12350181.html

使用ok检查channel是否关闭且是否清空了所有数据，然后break或者将channel置为nil（select不会在nil的channel上等待）

### select-case的随机监听以及优先级监听

```go
func worker2(ch1, ch2 <-chan int, stopCh chan struct{}) {
	for {
		select {
		case <-stopCh:
			return
		case job1 := <-ch1:
			fmt.Println(job1)
		case job2 := <-ch2:
		priority:
			for {
				select {
				case job1 := <-ch1:
					fmt.Println(job1)
				default:
					break priority
				}
			}
			fmt.Println(job2)
		}
	}
}
```

### 优雅的执行和检查goroutine的关闭

1. signals
2. sync.WaitGroup
3. context.Cancel
