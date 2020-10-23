# Golang
## 实际应用
1. [Golang连接mysql数据库 - 简书](https://www.jianshu.com/p/ee87e989f149)
2. [使用Kafka(附Golang代码)](https://juejin.im/post/6844903903113248776) [golang 消费 kafka 的坑 | Wolfogre’s Blog](https://blog.wolfogre.com/posts/golang-consume-kafka/)
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
	fmt.Println(*ptr) （*指针获取a变量的值，为2）
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
![2B4E155F-5BE8-48D7-9332-C9C3FA315FE4](/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.B1Jj6j/2B4E155F-5BE8-48D7-9332-C9C3FA315FE4.png)
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