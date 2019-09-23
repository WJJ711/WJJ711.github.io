---
title: 9.TCP通信程序
date: 2019-04-29 11:06:52
tags:	
	- JavaSE
	- Java
categories: JavaSE
---

# 第一章  概述

TCP通信能实现两台计算机之间的数据交互，通信的两端，要严格区分为客户端（Client）与服务端（Server）。

**两端通信时步骤：**

1. 服务端程序，需要事先启动，等待客户端的连接。
2. 客户端主动连接服务器端，连接成功才能通信。服务端不可以主动连接客户端。

**在Java中，提供了两个类用于实现TCP通信程序：**

1. 客户端：`java.net.Socket` 类表示。创建`Socket`对象，向服务端发出连接请求，服务端响应请求，两者建立连接开始通信。
2. 服务端：`java.net.ServerSocket` 类表示。创建`ServerSocket`对象，相当于开启一个服务，并等待客户端的连接。

# 第二章 Socket类  

`Socket` 类：该类实现客户端套接字，套接字指的是两台设备之间通讯的端点。

### 构造方法

- `public Socket(String host, int port)` :创建套接字对象并将其连接到指定主机上的指定端口号。如果指定的host是null ，则相当于指定地址为回送地址。  

  > 小贴士：回送地址(127.x.x.x) 是本机回送地址（Loopback Address），主要用于网络软件测试以及本地机进程间通信，无论什么程序，一旦使用回送地址发送数据，立即返回，不进行任何网络传输。

构造举例，代码如下：

```java
Socket client = new Socket("127.0.0.1", 6666);
```

### 成员方法

- `public InputStream getInputStream()` ： 返回此套接字的输入流。
  - 如果此Scoket具有相关联的通道，则生成的InputStream 的所有操作也关联该通道。
  - 关闭生成的InputStream也将关闭相关的Socket。
- `public OutputStream getOutputStream()` ： 返回此套接字的输出流。
  - 如果此Scoket具有相关联的通道，则生成的OutputStream 的所有操作也关联该通道。
  - 关闭生成的OutputStream也将关闭相关的Socket。
- `public void close()` ：关闭此套接字。
  - 一旦一个socket被关闭，它不可再使用。
  - 关闭此socket也将关闭相关的InputStream和OutputStream 。 
- `public void shutdownOutput()` ： 禁用此套接字的输出流。   
  - 任何先前写出的数据将被发送，随后终止输出流。 

# 第三章ServerSocket类

`ServerSocket`类：这个类实现了服务器套接字，该对象等待通过网络的请求。

### 构造方法

- `public ServerSocket(int port)` ：使用该构造方法在创建ServerSocket对象时，就可以将其绑定到一个指定的端口号上，参数port就是端口号。

构造举例，代码如下：

```java
ServerSocket server = new ServerSocket(6666);
```

### 成员方法

- `public Socket accept()` ：侦听并接受连接，返回一个新的Socket对象，用于和客户端实现通信。该方法会一直阻塞直到建立连接。 

# 第三章 简单的TCP网络程序

## TCP传输-客户端思路

```
*创建客户端
* 1、建立连接：使用Socket创建客户端+服务器的地址和端口
* 2、操作：输入输出流操作
* 3、释放资源
```

# TCP传输-服务器端思路

``` 
* 创建服务器
* 1.指定端口：使用ServerSocket创建服务器
* 2.阻塞式等待连接accept
* 3.操作：输入输出流操作
* 4.关闭资源
```

### TCP通信分析图解

> 到此，客户端向服务端发送数据成功。

![TCP简单通信](D:\blog\WJJ711\source\_posts\7-TCP通信程序\TCP简单通信.jpg)

{%asset_img TCP简单通信.jpg%}

> 自此，服务端向客户端回写数据。

6. 【服务端】Socket对象，获取OutputStream，向客户端回写数据。
7. 【客户端】Scoket对象，获取InputStream，解析回写数据。
8. 【客户端】释放资源，断开连接。

### 客户端向服务器发送数据

**服务端实现：**

```java
package tcp;

import java.io.DataInputStream;
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.logging.SocketHandler;

/**
 * 创建服务器
 * 1.指定端口：使用ServerSocket创建服务器
 * 2.阻塞式等待连接accept
 * 3.操作：输入输出流操作
 * 4.关闭资源
 */
public class Server {
    public static void main(String[] args) throws IOException {
        System.out.println("------Server------------");
        //1指定端口：使用ServerSocket创建服务器
        ServerSocket server=new ServerSocket(8888);
        //2.阻塞式等待连接
        Socket client = server.accept();
        System.out.println("一个客户端建立了连接");
        //3操作
        DataInputStream dis=new DataInputStream(client.getInputStream());
        System.out.println(dis.readUTF());
        //4关闭资源
        dis.close();
        client.close();
    }
}
```

**客户端实现：**

```java
package tcp;

import java.io.DataOutputStream;
import java.io.IOException;
import java.net.Socket;

/**
 * 创建客户端
 * 1、建立连接：使用Socket创建客户端+服务器的地址和端口
 * 2、操作：输入输出流操作
 * 3、释放资源
 */
public class Client {
    public static void main(String[] args) throws IOException {
        System.out.println("---------Client------------");
        //建立连接
        Socket client=new Socket("localhost",8888);
        //2操作
        DataOutputStream dos=new DataOutputStream(client.getOutputStream());
        String s="hello";
        dos.writeUTF(s);

        dos.close();
        client.close();
    }
}
```

### 服务器向客户端回写数据

**服务端实现：**

```java
package tcp;

import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;

/**
 * 模拟登陆：双向
 * 创建服务器
 * 1、指定端口
 * 2、阻塞式等待连接accept
 * 3操作
 * 4关闭资源
 */
public class LoginTwoWayServer {
    public static void main(String[] args) throws IOException {
        System.out.println("------Server-----------------");
        //1指定端口
        ServerSocket server=new ServerSocket(8888);
        //2阻塞式等待连接
        Socket client=server.accept();
        //3操作
        DataInputStream dis=new DataInputStream(client.getInputStream());
        String data = dis.readUTF();
        String uname="";
        String upwd="";
        String[] dataArray = data.split("&");
        for(String info:dataArray){
            String[] msg = info.split("=");
            if(msg[0].equals("uname")){
                uname=msg[1];
                System.out.println("用户名--->"+msg[1]);
            }else{
                upwd=msg[1];
                System.out.println("密码--->"+msg[1]);

            }
        }
        DataOutputStream dos=new DataOutputStream(client.getOutputStream());

        //反馈
        if(uname.equals("wjj")&&upwd.equals("123456")){
            dos.writeUTF("登陆成功，欢迎回来");
        }else{
            dos.writeUTF("用户名或密码错误");
        }
        dos.flush();
        //关闭资源
        dis.close();
        dos.close();
        client.close();
    }
}

```

**客户端实现：**

```java
package tcp;

import java.io.*;
import java.net.Socket;

/**
 * 模拟登陆 双向
 * 创建客户端
 * 1、建立连接：使用Socket创建客户端+服务的地址和端口
 * 2、操作：输入输出流操作
 * 3、释放资源
 */

public class LoginTwoWayClient {
    public static void main(String[] args) throws IOException {
        System.out.println("---------Client------");
        //控制台输入
        BufferedReader reader=new BufferedReader(new InputStreamReader(System.in));
        System.out.println("请输入用户名");
        String uname=reader.readLine();
        System.out.println("请输入密码");
        String upwd=reader.readLine();
        //1建立连接：使用Socket创建客户端+服务的地址和端口
        Socket client=new Socket("localhost",8888);
        //2输入输出流操作
        DataOutputStream dos=new DataOutputStream(client.getOutputStream());
        dos.writeUTF("uname="+uname+"&upwd="+upwd);
        dos.flush();
        //接受服务器消息
        DataInputStream dis=new DataInputStream(client.getInputStream());
        String s = dis.readUTF();
        System.out.println(s);
        //3释放资源
        dis.close();
        dos.close();
        client.close();
    }
}

```

# 