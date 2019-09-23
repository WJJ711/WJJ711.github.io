---
title: 7.UDP程序若干Demo
date: 2019-04-29 09:42:28
tags:
	- JavaSE
	- Java
categories: JavaSE
---

# 基本Demo

## 客户端

```java
package udp;

import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetSocketAddress;
import java.net.SocketException;

/**
 * 发送端
 * 1、使用DatagramSocket指定端口，创建发送端
 * 2、准备数据 一定转成字节数组
 * 3、封装成DatagramPacket包裹，需要指定目的地
 * 4.发送包裹 send(DatagramPacket p)
 *  5.释放资源
 */
public class UdpClient {
    public static void main(String[] args) throws IOException {
        System.out.println("发送方启动中。。。。");
        //1、使用DatagramSocket指定端口，创建发送端
        DatagramSocket client=new DatagramSocket(8888);
        //2、准备数据，转成字节数组
        byte[] datas="talk is cheap,show me the code".getBytes();
        //3.封装成DatagramPacket包裹，需要指定目的地
        DatagramPacket packet=new DatagramPacket(datas,0,datas.length,
                new InetSocketAddress("localhost",9999));
        //4.发送包裹send(DatagramPacket p)
        client.send(packet);
        client.close();

    }
}
```

## 服务器

```java
package udp;

import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.SocketException;

/**
 * 接收端
 * 1、使用DatagramSocket指定端口，创建接收端
 * 2、准备容器 封装成DatagramPacket包裹
 * 3、阻塞式接收包裹receive（DatagramPacket p)
 * 4.分析数据
 *       byte[] getData()
 *      int getLength()
 * 5.释放资源
 */

public class UdpServer {
    public static void main(String[] args) throws IOException {
        System.out.println("接收端启动中...");
        //1、使用DatagramSocket指定端口，创建接收端
        DatagramSocket server=new DatagramSocket(9999);
        //2、准备容器 封装成DatagramPacket包裹
        byte[] container=new byte[1024*60];
        //3阻塞式接收包裹receive（DatagramPacket p)
        DatagramPacket packet=new DatagramPacket(container,0,container.length);
        server.receive(packet);
        //4.分析数据
        // *       byte[] getData()
        // *      int getLength()
        byte[] datas=packet.getData();
        int len=packet.getLength();
        System.out.println(new String(datas,0,len));
        server.close();
    }
}
```

# 传输基本数据类型

## 客户端

```java
package udp;

import java.io.BufferedOutputStream;
import java.io.ByteArrayOutputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetSocketAddress;
import java.net.SocketException;

/**
 * 基本类型：发送端
 * 1、使用DatagramSocket指定端口 创建发送端
 * 2、将基本类型转成字节数组
 * 3、封装成DatagramPacket包裹，需要指定目的地
 * 4、发送包裹 send(DatagramPacket p)
 * 5、释放资源
 */

public class UdpTypeClient {
    public static void main(String[] args) throws IOException {
        System.out.println("发送端启动中...");
        //1.使用DatagramSocket指定端口，创建发送端
        DatagramSocket client=new DatagramSocket(8888);
        //2准备数据，一定转成字节数组
        ByteArrayOutputStream baos=new ByteArrayOutputStream();
        DataOutputStream dos=new DataOutputStream(new BufferedOutputStream(baos));
        //操作数据类型+数据
        dos.writeUTF("编码辛酸泪");
        dos.writeInt(18);
        dos.writeBoolean(false);
        dos.writeChar('c');
        dos.flush();
        byte[] datas=baos.toByteArray();
        //3封装成DatagramPacket包裹，需要指定目的地
        DatagramPacket packet=new DatagramPacket(datas,0,datas.length,
                new InetSocketAddress("localhost",9999));
        //4发送包裹send(DatagramPacket p)
        client.send(packet);
        //5释放资源
        client.close();
    }
}
```

##　服务器

```java
package udp;

import java.io.BufferedInputStream;
import java.io.ByteArrayInputStream;
import java.io.DataInputStream;
import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.SocketException;

/**
 * 基本类型：接收端
 *1、使用DatagramSocket指定端口，创建接收端
 * 2、准备容器 封装成DatagramPacket包裹
 * 3.阻塞式接收包裹，receive(DatagramPacket p)
 * 4.分析数据，将字节数组还原成对应的类型
 * byte[] getData()
 *  int   getLength()
 * */
public class UdpTypeServer {
    public static void main(String[] args) throws IOException {
        System.out.println("接收方启动中....");
        //1使用DatagramSocket 指定端口 创建接收端
        DatagramSocket server=new DatagramSocket(9999);
        //2准备容器封装成DatagramPacket包裹
        byte[] container=new byte[1024*60];
        DatagramPacket packet=new DatagramPacket(container,0,container.length);
        //3.阻塞式接收包裹，receive(DatagramPacket p)
        server.receive(packet);
        //4分析数据
        byte[] datas=packet.getData();
        int len=packet.getLength();
        DataInputStream dis=new DataInputStream(new BufferedInputStream(new ByteArrayInputStream(datas)));
        String msg=dis.readUTF();
        int age=dis.readInt();
        boolean flag=dis.readBoolean();
        char ch=dis.readChar();
        System.out.println(msg+"-->"+ch);
        //5.释放资源
        server.close();

    }
}
```

# 传输对象

## 对象

```java
package udp;

import java.io.Serializable;

public class Employee implements Serializable {
    private transient String name;
    private int salary;

    public Employee(String name, int salary) {
        this.name = name;
        this.salary = salary;
    }

    @Override
    public String toString() {
        return "Employee{" +
                "name='" + name + '\'' +
                ", salary=" + salary +
                '}';
    }
}

```



## 客户端

```java
package udp;

import java.io.BufferedOutputStream;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.ObjectOutputStream;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetSocketAddress;
import java.net.SocketException;

/**
 * 引用类型：发送端
 * 1.使用DatagramSocket 指定端口，创建发送端
 * 2.讲引用数据类型转成字节数组
 * 3、封装成DatagramPacket包裹，需要指定目的地
 * 4.发送包裹send(DatagramPacket p)
 * 5.释放资源
 */

public class UdpObjClient {
    public static void main(String[] args) throws IOException {
        System.out.println("发送端启动中....");
        //1.使用DatagramSocket 指定端口，创建发送端
        DatagramSocket client=new DatagramSocket(8888);
        //2.讲引用数据类型转成字节数组
        ByteArrayOutputStream baos=new ByteArrayOutputStream();
        ObjectOutputStream os=new ObjectOutputStream(new BufferedOutputStream(baos));
        os.writeUTF("show me the code");
        os.writeInt(18);
        os.writeObject(new Employee("小马",400));
        os.flush();
        byte[] datas=baos.toByteArray();
        //3封装成DatagramPackage包裹，需要指定目的地
        DatagramPacket packet=new DatagramPacket(datas,0,datas.length,
                new InetSocketAddress("localhost",9999));
        //4发送包裹
        client.send(packet);
        //5释放资源
        client.close();
    }
}
```

## 服务器

```java
package udp;

import java.io.BufferedInputStream;
import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetSocketAddress;
import java.net.SocketException;

/**
 * 引用类型：接收端
 * 1、使用DatagramSocket指定端口，创建接收端
 * 2、准备容器，封装成DatagramPacket包裹
 * 3、阻塞式接受包裹
 * 4.分析数据，将字节数组还原为对应的类型
 * 5、释放资源
 */

public class UdpObjServer {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        System.out.println("接收端启动中.....");
        //1,指定端口创建接收端
        DatagramSocket server=new DatagramSocket(9999);
        //2创建容器，封装成包裹
        byte[] contains=new byte[1024*60];
        DatagramPacket packet=new DatagramPacket(contains,0,contains.length);
        //3阻塞式接受
        server.receive(packet);
        //4分析数据
        byte[] datas=packet.getData();
        int len=packet.getLength();
        ByteArrayInputStream bis=new ByteArrayInputStream(datas);
        ObjectInputStream is=new ObjectInputStream(new BufferedInputStream(bis));
        String msg = is.readUTF();
        int age = is.readInt();
        Object emp = is.readObject();
        System.out.println(msg);
        System.out.println(age);
        System.out.println(emp);
    }
}
```

# 传输文件

## 客户端

```java
package udp;

import java.io.*;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetSocketAddress;
import java.net.SocketException;

/**
 * 文件传输：发送端
 * 1.创建端口
 * 2.将文件转成字节数组
 * 3.封装成包裹，需要指定目的地
 * 4.发送包裹
 * 5.释放资源
 * */
public class UdpFileCilent {
    public static void main(String[] args) throws IOException {
        System.out.println("发送端启动...");
        //1创建端口
        DatagramSocket client=new DatagramSocket(8888);
        //2将文件转成字节数组
        ByteArrayOutputStream baos=new ByteArrayOutputStream();
        InputStream is=new FileInputStream("D:/picture/朝阳李敏镐.jpg");
        byte[] flush=new byte[1024];
        int len=-1;
        while ((len=is.read(flush))!=-1){
            baos.write(flush,0,len);
        }
        baos.flush();

        byte[] datas=baos.toByteArray();
        //3封装成包裹，需要指定目的地
        DatagramPacket packet=new DatagramPacket(datas,0,datas.length,
                new InetSocketAddress("localhost",9999));
        //4发送数据
        client.send(packet);
        //5关闭资源
        client.close();
        is.close();
    }
}

```

## 服务器

```java
package udp;

import java.io.*;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.SocketException;

/**
 * 文件传输：接收端
 * 1、指定端口
 * 2、创建容器，封装成包裹
 * 3、阻塞式接受
 * 4.解析数据
 * 5.释放资源
 */

public class UdpFileServer {
    public static void main(String[] args) throws IOException {
        System.out.println("接收端启动");
        //1指定端口
        DatagramSocket server=new DatagramSocket(9999);
        //2创建容器，封装成包裹
        byte[] contains=new byte[1024*60];
        DatagramPacket packet=new DatagramPacket(contains,0,contains.length);
        //3阻塞式接受包裹
        server.receive(packet);
        //4分析数据
        byte[] datas=packet.getData();
        ByteArrayInputStream bis=new ByteArrayInputStream(datas);
        OutputStream os=new FileOutputStream("upload.jpg");
        byte[] flush=new byte[1024];
        int len=-1;
        while ((len=bis.read(flush))!=-1){
            os.write(flush,0,len);
        }
        os.flush();

        //5释放资源
        server.close();
        os.close();

    }
}
```

