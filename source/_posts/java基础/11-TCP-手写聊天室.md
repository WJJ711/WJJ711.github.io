---
title: 11.TCP-手写聊天室
date: 2019-04-29 11:06:54
tags:
	- JavaSE
	- Java
categories: JavaSE
---

# 客户端

```java
package chat05;

import chat03oop.Utils;

import java.io.*;
import java.net.Socket;

/**
 * 在线聊天室：客户端
 * 有私聊，群聊功能
 */

public class Client {
    public static void main(String[] args) throws IOException {
        System.out.println("----------Client1-------------");
        //1建立连接
        Socket client=new Socket("localhost",8888);
        System.out.println("请输入用户名");
        BufferedReader reader=new BufferedReader(new InputStreamReader(System.in));
        String name = reader.readLine();
        new Thread(new Send(client,name)).start();
        new Thread(new Receive(client)).start();
    }
    static class Send implements Runnable{
        private Socket client;
        private boolean isRuning;
        private BufferedReader console;
        private DataOutputStream os;
        public Send(Socket client,String name) {
            this.client = client;
            isRuning=true;
            console=new BufferedReader(new InputStreamReader(System.in));
            try {
                os= new DataOutputStream(client.getOutputStream());
                send(name);
            } catch (IOException e) {
                e.printStackTrace();
                release();
            }
        }
        private void release(){
            Utils.close(os,console,client);
        }
        private String getFromConsole(){
            String msg="";
            try {
                msg=console.readLine();
            } catch (IOException e) {
                e.printStackTrace();
                release();
            }
            return msg;
        }
        private void send(String msg){
            try {
                if(!msg.equals("")){
                    os.writeUTF(msg);
                }
            } catch (IOException e) {
                e.printStackTrace();
                release();
            }
        }


        @Override
        public void run() {
            while (isRuning){
                //1从控制台获取数据
                String msg=getFromConsole();
                send(msg);
            }
        }
    }
    static class Receive implements Runnable{
        private Socket client;
        private boolean isRunning;
        private DataInputStream is;
        public Receive(Socket client) {
            isRunning=true;
            this.client = client;
            try {
                is=new DataInputStream(client.getInputStream());
            } catch (IOException e) {
                e.printStackTrace();
                release();
            }
        }
        private void release(){
            isRunning=false;
            Utils.close(client,is);
        }
        private String receive(){
            String msg="";
            try {
                msg=is.readUTF();
            } catch (IOException e) {
                e.printStackTrace();
            }
            return msg;
        }
        @Override
        public void run() {
            while (isRunning){
                String msg=receive();
                System.out.println(msg);
            }
        }
    }
}

```

# 服务器

```java
package chat05;

import chat03oop.Utils;

import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.CopyOnWriteArrayList;

/**
 * 在线聊天室:服务器
 * 目标：私聊，群聊
 */

public class Server {
    private static CopyOnWriteArrayList<Channel> list=new CopyOnWriteArrayList<>();
    public static void main(String[] args) throws IOException {
        System.out.println("-----Server-------");
        ServerSocket server=new ServerSocket(8888);
        while (true){
            Socket client = server.accept();
            Channel channel = new Channel(client);
            list.add(channel);
            new Thread(channel).start();
        }
    }
    static class Channel implements Runnable{
        private Socket client;
        private DataOutputStream os;
        private DataInputStream is;
        private boolean isRunning;
        private String name;
        public Channel(Socket client) {
            isRunning=true;
            this.client = client;
            try {
                os=new DataOutputStream(client.getOutputStream());
                is=new DataInputStream(client.getInputStream());
                this.name=receive();
                this.send("欢迎加入");
                sendOthers(this.name+"加入了群聊",true);
            } catch (IOException e) {
                e.printStackTrace();
                release();
            }
        }
        private String receive(){
            String msg="";
            try {
                msg=is.readUTF();
            } catch (IOException e) {
                e.printStackTrace();
                release();
            }
            return msg;
        }
        private void send(String msg){
            try {
                os.writeUTF(msg);
            } catch (IOException e) {
                e.printStackTrace();
                release();
            }

        }

        /**
         * 加入私聊，以@xxx:
         * @param msg
         */
        private void sendOthers(String msg,boolean isSys){
           if(msg.startsWith("@")){
               int index=msg.indexOf(":");
               String name=msg.substring(1,index);
               String info=msg.substring(index+1);
                for (Channel c:list){
                    if(c.name.equals(name)){
                        c.send(this.name+"悄悄地对您说:"+info);
                        break;
                    }
                }
           }else{
               for(Channel c:list){
                   if(c==this){
                       continue;
                   }else{
                       if(isSys){
                           c.send(msg);
                       }else {
                           c.send(this.name+"向所有人说："+msg);
                       }
                   }
               }
           }
        }
        private void release(){
            isRunning=false;
            sendOthers(this.name+"退出了群聊",true);
            list.remove(this);
            Utils.close(is,os,client);
        }
        @Override
        public void run() {
            while (isRunning){
                //1获取信息
                String msg=receive();
                //2发送
                if(!msg.equals("")){
                    sendOthers(msg,false);
                }
            }
        }
    }
}
```

