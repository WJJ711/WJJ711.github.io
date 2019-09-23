---
title: 6.UDP通信程序
date: 2019-04-29 09:42:27
tags:
	- JavaSE
	- Java
categories: JavaSE
---

# 第一章 Socket

Socket套接字：

›网络上具有唯一标识的IP地址和端口号组合在一起才能构成唯一能识别的标识符套接字。Socket屏蔽了协议的具体细节（不同类型的socket使用不同的协议）

Socket原理机制：

›通信的两端都有Socket。

›网络通信其实就是Socket间的通信。

›数据在两个Socket间通过IO传输。![Socket机制图解](D:\blog\WJJ711\source\_posts\6-UDP通信程序\Socket机制图解.jpg)

{%asset_img Socket机制图解.jpg%}

# 第二章 UDP传输

DatagramSocket与DatagramPacket

1. 建立发送端，接收端。

2. 建立数据包。

3. 调用Socket的发送接收方法。

4. 关闭Socket。

发送端与接收端是两个独立的运行程序。

# 第三章 UDP传输-发送端思路

1. 使用DatagramSocket 指定端口，创建发送端

2. **将要发送内容转成字节数组**（不同的传输类型只有该步不同）

3. 封装成DatagramPacket包裹，需要指定目的地

4.  发送包裹send
5. 释放资源

# 第四章 UDP传输-接收端思路

1. 指定端口
2. 创建容器，封装成包裹
3. 阻塞式接受
4. **解析数据**（不同的传输类型只有该步不同）
5. 释放资源



