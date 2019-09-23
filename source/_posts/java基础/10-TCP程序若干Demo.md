---
title: 10-TCP程序若干Demo
date: 2019-04-29 11:06:53
tags:
	- JavaSE
	- Java
categories: JavaSE
---

# 基本Demo

## 客户端：

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

## 服务器

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

# 模拟登陆 双向

## 客户端

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

## 服务器

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

# 模拟登陆 多个客户端登陆

多线程，面向对象封装

## 客户端

```java
package tcp;

import java.io.*;
import java.net.Socket;

/**
 * 模拟登陆 多个客户端请求
 * 创建客户端
 * 1、建立连接：使用Socket创建客户端
 * 2、操作：输入输出流操作
 * 3、释放资源
 */

public class LoginMultiClient {
    public static void main(String[] args) throws IOException {
        System.out.println("-------Client---------");
        //1建立连接
        Socket client = new Socket("localhost", 8888);
        //2操作：输入输出流操作
        new Send(client).send();
        new Receive(client).receive();
        //3释放资源
        client.close();

    }

    static class Send {
        private Socket client;
        private BufferedReader reader;
        private DataOutputStream dos;
        private String msg="";
        public Send(Socket client) {
            try {
                dos = new DataOutputStream(client.getOutputStream());
            } catch (IOException e) {
                e.printStackTrace();
            }
            this.client = client;
            reader = new BufferedReader(new InputStreamReader(System.in));
            msg=init();
        }

        private String init() {
            String uname = "";
            String upwd = "";
            try {
                System.out.println("请输入用户名:");
                uname = reader.readLine();
                System.out.println("请输入密码：");
                upwd = reader.readLine();
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                try {
                    if(reader!=null){

                        reader.close();
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
                return "uname=" + uname + "&" + "upwd=" + upwd;

            }
        }

        public void send() {

            try {
                dos.writeUTF(msg);
                dos.flush();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }

    static class Receive {
        private Socket client;
        private DataInputStream dis;
        public Receive(Socket client) {
            this.client = client;
            try {
                dis=new DataInputStream(client.getInputStream());
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        public void receive() {
            try {
                String s=dis.readUTF();
                System.out.println(s);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}

```

## 服务器

```java
package tcp;

import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.io.StringReader;
import java.net.ServerSocket;
import java.net.Socket;

/**
 * 模拟登陆：多个客户端请求
 * 创建服务器
 * 1、指定端口 使用ServerSocket创建服务器
 * 2、阻塞式等待连接accept
 * 3、操作
 * 4、释放资源
 */
public class LoginMutilServer {
    public static void main(String[] args) throws IOException {
        System.out.println("--------server-----------");
        //1指定端口
        ServerSocket server=new ServerSocket(8888);
        boolean isRunning=true;
        while (isRunning){
            //2阻塞式等待连接accept
            Socket client=server.accept();
            System.out.println("一个客户端进行了连接");
            new Thread(new Channel(client)).start();

        }
    }
    static class Channel implements Runnable{
        private Socket client;
        private DataInputStream dis;
        private DataOutputStream dos;

        public Channel(Socket client) {
            try {
                dis=new DataInputStream(client.getInputStream());
                dos=new DataOutputStream(client.getOutputStream());
            } catch (IOException e) {
                e.printStackTrace();
            }
            this.client = client;
        }
        private String receive(){
            String datas="";
            try {
                datas=dis.readUTF();
            } catch (IOException e) {
                e.printStackTrace();
            }
            return datas;
        }
        private void send(String msg){
            try {
                dos.writeUTF(msg);
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
        private void release(){
            try {
                dis.close();
                dos.close();
                client.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        @Override
        public void run() {//线程体主要执行业务逻辑
            String datas=receive();
            String uname="";
            String upwd="";
            String[] dataArray=datas.split("&");
            for(String info:dataArray){
                String[] user=info.split("=");
                if(user[0].equals("uname")){
                    System.out.println("用户名为--->"+user[1]);
                    uname=user[1];
                }else {
                    System.out.println("密码为--->"+user[1]);
                    upwd=user[1];
                }
            }
            if(uname.equals("wjj")&&upwd.equals("123456")){
                send("登陆成功");
            }else{
                send("用户名或密码错误");
            }
            release();
        }
    }
}

```

