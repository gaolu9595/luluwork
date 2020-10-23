# RPC & gRPC
1. RPC：远程过程调用，在Object-Oriented语言中也可称为RMI（远程方法调用）
	RPC用来屏蔽封装一系列细节处理（如TCP/UDP/HTTP通信细节、序列化机制等），保证Client A可以简单透明地调用Server B上的远程方法并收到返回结果
![46CAA2B0-C536-407A-91C3-90C6910387FD](/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.jxmnfY/46CAA2B0-C536-407A-91C3-90C6910387FD.png)

2. gRPC：是Google牵头的高性能、开源、支持多语言的一种RPC框架，是RPC理念的一种实现
3. gRPC面向HTTP2传输协议，使用ProtoBuf作为内容交换格式，客户端和服务端都可以使用gRPC支持的不同语言

   