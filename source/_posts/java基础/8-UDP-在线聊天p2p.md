---
title: 8.UDP-在线聊天p2p
date: 2019-04-29 09:42:29
tags:
	- JavaSE
	- Java
categories: JavaSE
---

# 客户端：

```java
package udponlinetalkmulti;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetSocketAddress;
import java.net.SocketException;

/**
 * 多线程模拟网络聊天：发送端
 * 1、指定端口
 * 2、转成字节数组
 * 3、封装成包裹，要指定发送的地方
 * 4、发送
 * 5、关闭资源
 */

public class TalkClient implements Runnable{
    DatagramSocket client;
    private int toport;
    private String toIP;

    public TalkClient(int port,int toport, String toIP) {
        try {
            client=new DatagramSocket(port);
        } catch (SocketException e) {
            e.printStackTrace();
        }
        this.toport = toport;
        this.toIP = toIP;
    }

    @Override
    public void run() {
        System.out.println("发送端启动.....");
        //1指定端口

        try {
            //2转换成字节数组
            BufferedReader reader=new BufferedReader(new InputStreamReader(System.in));
            while (true){
                String s = reader.readLine();
                byte[] datas=s.getBytes();
                //3封装成包裹
                DatagramPacket packet=new DatagramPacket(datas,0,datas.length,
                new InetSocketAddress(toIP,toport));
                //4发送
                client.send(packet);
                if(s.equals("bye")){
                    break;
                }
            }
        } catch (SocketException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            client.close();
        }

    }
}
```

# 服务器

```java
package udponlinetalkmulti;

import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.SocketException;

/**
 * 多线程模拟网上聊天 接收端
 * 1、指定端口
 * 2、指定字节数组，打包成包裹
 * 3、接受
 * 4、解码
 * 5、关闭资源
 */

public class TalkServer implements Runnable {
    DatagramSocket server;
    private String from;

    public TalkServer(int port,String from) {
        try {
            server =new DatagramSocket(port);
        } catch (SocketException e) {
            e.printStackTrace();
        }
        this.from = from;
    }

    @Override
    public void run() {
        System.out.println("接收端运行中....");
        try {

            //2指定字节数组，打包成包裹
            byte[] container=new byte[1024*60];
            DatagramPacket packet=new DatagramPacket(container,0,container.length);
            //3接收
            while (true){
                server.receive(packet);
                //4解码
                byte[] datas=packet.getData();
                int len=packet.getLength();
                String s=new String(datas,0,len);
                System.out.println(from+": "+s);
                if(s.equals("bye")){
                    break;
                }
            }
        } catch (SocketException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

# 学生

```java
package udponlinetalkmulti;

public class Student {
    public static void main(String[] args) {
        new Thread(new TalkServer(8888,"老师")).start();
        new Thread(new TalkClient(6666,5555,"localhost")).start();
    }
}
```

# 老师

```java
package udponlinetalkmulti;

public class Teacher {
    public static void main(String[] args) {
        new Thread(new TalkClient(9999,8888,"localhost")).start();
        new Thread(new TalkServer(5555,"学生")).start();
    }
}
```

