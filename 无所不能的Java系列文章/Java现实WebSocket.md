Java现实WebSocket
============

[无所不能的Java系列文章](http://blog.fens.me/series-java/)，涵盖了Java的思想，应用开发，设计模式，程序架构等，通过我的经验去诠释Java的强大。

说起Java，真的有点不知道从何说起。Java是一门全领域发展的语言，从基础的来讲有4大块，Java语法，JDK，JVM，第三方类库。官方又以面向不同应用的角度，又把JDK分为JavaME，JavaSE，JavaEE三个部分。Java可以做客户端界面，可以做中间件，可以做手机系统，可以做应用，可以做工具，可以做游戏，可以做算法…，Java几乎无所不能。

在Java的世界里，Java就是一切。

#### 关于作者

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/java-websocket-intro/

![Java现实WebSocket](http://blog.fens.me/wp-content/uploads/2013/08/java-websocket.png)

#### 前言

伴随着HTML5技术的新起，WebSocket 作为一种浏览器与服务器的核心通信技术，被嵌入到了浏览器的内核中。WebSocket 的出现使得浏览器提供对 Socket 的支持成为可能，从而在浏览器和服务器之间提供了一个基于 TCP 连接的双向通道。

所有新的技术都会第一时间在Java社区，出现对应的开源项目！WebSocket也被实现在多种Java的开源库中。WebSocket实现列表：https://java.net/projects/websocket-spec/pages/WebSocketAPIs/text。

今天就让我们用Java来解密一下WebSocket的服务器端和客户端 实现。

#### 目录

1. 服务器端实现(Tomcat)
2. 客户端实现(Java-WebSocket)
3. 客户端实现(Javascript原生API)

## 1. 服务器端实现(Tomcat)

用Java实现的websocket，在Server端是通过Tomcat内嵌支持的，我们需要开发一个继承WebSocketServlet 的servlet就可以了，与普通的HttpServlet没有太大区别。

### 1). JAVA环境：

* Java: jdk 1.6.0_45, Server VM 64bit
* Maven: 3.0.5
* Tomcat: 7.0.39.0

```{bash}

~ D:\workspace\java>java -version
java version "1.6.0_45"
Java(TM) SE Runtime Environment (build 1.6.0_45-b06)
Java HotSpot(TM) 64-Bit Server VM (build 20.45-b01, mixed mode)

~ D:\workspace\java>mvn -version
Apache Maven 3.0.5 (r01de14724cdef164cd33c7c8c2fe155faf9602da; 2013-02-19 21:51:28+0800)
Maven home: D:\toolkit\maven3\bin\..
Java version: 1.6.0_45, vendor: Sun Microsystems Inc.
Java home: D:\toolkit\java\jdk6\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 7", version: "6.1", arch: "amd64", family: "windows"

~ D:\toolkit\tomcat7\bin>catalina.bat version
Using CATALINA_BASE:   "D:\toolkit\tomcat7"
Using CATALINA_HOME:   "D:\toolkit\tomcat7"
Using CATALINA_TMPDIR: "D:\toolkit\tomcat7\temp"
Using JRE_HOME:        "D:\toolkit\java\jdk6"
Using CLASSPATH:       "D:\toolkit\tomcat7\bin\bootstrap.jar;D:\toolkit\tomcat7\bin\tomcat-juli.jar"
Server version: Apache Tomcat/7.0.39
Server built:   Mar 22 2013 12:37:24
Server number:  7.0.39.0
OS Name:        Windows 7
OS Version:     6.1
Architecture:   amd64
JVM Version:    1.6.0_45-b06
JVM Vendor:     Sun Microsystems Inc.
```

### 2). maven构建一个简单的webapp项目。

```{bash}
~ D:\workspace\java>mvn archetype:generate -DgroupId=org.conan.websocket -DartifactId=websocketServer -DarchetypeArtifactId=maven-archetype-webapp

[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Old (1.x) Archetype: maven-archetype-webapp:1.0
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: org.conan.websocket
[INFO] Parameter: packageName, Value: org.conan.websocket
[INFO] Parameter: package, Value: org.conan.websocket
[INFO] Parameter: artifactId, Value: websocketServer
[INFO] Parameter: basedir, Value: D:\workspace\java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] project created from Old (1.x) Archetype in dir: D:\workspace\java\websocketServer
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1:42.200s
[INFO] Finished at: Tue Aug 20 13:57:05 CST 2013
[INFO] Final Memory: 9M/179M
[INFO] ------------------------------------------------------------------------
```

### 3). 配置项目目录

```{bash}
~ D:\workspace\java>cd websocketServer
~ D:\workspace\java\websocketServer>mkdir src\main\java
~ D:\workspace\java\websocketServer>rm src\main\webapp\index.jsp
```

导入到Eclipse的项目截图

![](http://blog.fens.me/wp-content/uploads/2013/08/ws1.png)

### 4). 编辑pom.xml配置文件，增加tomcat的依赖

```{bash}
~ vi pom.xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>org.conan.websocket</groupId>
<artifactId>websocketServer</artifactId>
<packaging>war</packaging>
<version>1.0-SNAPSHOT</version>
<name>websocketServer Maven Webapp</name>
<url>http://maven.apache.org</url>
<dependencies>
<dependency>
<groupId>org.apache.tomcat</groupId>
<artifactId>tomcat-catalina</artifactId>
<version>7.0.27</version>
<scope>provided</scope>
</dependency>
<dependency>
<groupId>org.apache.tomcat</groupId>
<artifactId>tomcat-coyote</artifactId>
<version>7.0.39</version>
<scope>provided</scope>
</dependency>
</dependencies>
<build>
<finalName>websocketServer</finalName>
</build>
</project>
```

### 下载并安装类库

```{bash}
~ D:\workspace\java\websocketServer>mvn clean install
```

### 5). 创建DemoServlet，服务器端运行类

```{bash}
~ vi src/main/java/org/conan/websocket/DemoServlet.java

package org.conan.websocket;

import java.io.IOException;
import java.nio.ByteBuffer;
import java.nio.CharBuffer;
import java.util.ArrayList;

import javax.servlet.http.HttpServletRequest;

import org.apache.catalina.websocket.MessageInbound;
import org.apache.catalina.websocket.StreamInbound;
import org.apache.catalina.websocket.WebSocketServlet;
import org.apache.catalina.websocket.WsOutbound;

public class DemoServlet extends WebSocketServlet {

    private static final long serialVersionUID = -4853540828121130946L;
    private static ArrayList mmiList = new ArrayList();

    @Override
    protected StreamInbound createWebSocketInbound(String str, HttpServletRequest request) {
        return new MyMessageInbound();
    }

    private class MyMessageInbound extends MessageInbound {
        WsOutbound myoutbound;

        @Override
        public void onOpen(WsOutbound outbound) {
            try {
                System.out.println("Open Client.");
                this.myoutbound = outbound;
                mmiList.add(this);
                outbound.writeTextMessage(CharBuffer.wrap("Hello!"));
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        @Override
        public void onClose(int status) {
            System.out.println("Close Client.");
            mmiList.remove(this);
        }

        @Override
        public void onTextMessage(CharBuffer cb) throws IOException {
            System.out.println("Accept Message : " + cb);
            for (MyMessageInbound mmib : mmiList) {
                CharBuffer buffer = CharBuffer.wrap(cb);
                mmib.myoutbound.writeTextMessage(buffer);
                mmib.myoutbound.flush();
            }
        }

        @Override
        public void onBinaryMessage(ByteBuffer bb) throws IOException {
        }
    }

}
```

### 6). 修改web.xml文件

```{bash}
~ vi src/main/webapp/WEB-INF/web.xml

<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">
<display-name>Archetype Created Web Application</display-name>
<servlet>
<servlet-name>wsServlet</servlet-name>
<servlet-class>org.conan.websocket.DemoServlet</servlet-class>
</servlet>
<servlet-mapping>
<servlet-name>wsServlet</servlet-name>
<url-pattern>/wsServlet</url-pattern>
</servlet-mapping>
</web-app>
```

### 7). 编译，打包，部署到tomcat

```{bash}
~ D:\workspace\java\websocketServer>mvn clean install
~ D:\workspace\java\websocketServer>cp target\websocketServer.war D:\toolkit\tomcat7\webapps
```

### 启动tomcat

```{bash}
~ D:\toolkit\tomcat7>bin\catalina.bat run
Using CATALINA_BASE:   "D:\toolkit\tomcat7"
Using CATALINA_HOME:   "D:\toolkit\tomcat7"
Using CATALINA_TMPDIR: "D:\toolkit\tomcat7\temp"
Using JRE_HOME:        "D:\toolkit\java\jdk6"
Using CLASSPATH:       "D:\toolkit\tomcat7\bin\bootstrap.jar;D:\toolkit\tomcat7\bin\tomcat-juli.jar"
2013-8-20 14:43:29 org.apache.catalina.core.AprLifecycleListener init
信息: The APR based Apache Tomcat Native library which allows optimal performance in production environments was not fou
nd on the java.library.path: D:\toolkit\java\jdk6\bin;C:\Windows\Sun\Java\bin;C:\Windows\system32;C:\Windows;D:\toolkit\
Rtools\bin;D:\toolkit\Rtools\gcc-4.6.3\bin;C:\Program Files (x86)\Common Files\NetSarang;C:\Windows\system32;C:\Windows;
C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;D:\toolkit\Git\cmd;D:\toolkit\Git\bin;C:\Program Fi
les (x86)\Microsoft SQL Server\100\Tools\Binn\;C:\Program Files\Microsoft SQL Server\100\Tools\Binn\;C:\Program Files\Mi
crosoft SQL Server\100\DTS\Binn\;c:\Program Files (x86)\Common Files\Ulead Systems\MPEG;C:\Program Files (x86)\QuickTime
\QTSystem\;D:\toolkit\MiKTex\miktex\bin\x64\;D:\toolkit\sshclient;D:\toolkit\ant19\bin;D:\toolkit\eclipse;D:\toolkit\gra
dle15\bin;D:\toolkit\java\jdk6\bin;D:\toolkit\maven3\bin;D:\toolkit\mysql56\bin;D:\toolkit\python27;D:\toolkit\putty;C:\
Program Files\R\R-3.0.1\bin\x64;D:\toolkit\mongodb243\bin;D:\toolkit\php54;D:\toolkit\nginx140;D:\toolkit\nodejs;D:\tool
kit\npm12\bin;D:\toolkit\java\jdk6\jre\bin\server;.
2013-8-20 14:43:30 org.apache.coyote.AbstractProtocol init
信息: Initializing ProtocolHandler ["http-bio-8080"]
2013-8-20 14:43:30 org.apache.coyote.AbstractProtocol init
信息: Initializing ProtocolHandler ["ajp-bio-8009"]
2013-8-20 14:43:30 org.apache.catalina.startup.Catalina load
信息: Initialization processed in 1409 ms
2013-8-20 14:43:30 org.apache.catalina.core.StandardService startInternal
信息: Starting service Catalina
2013-8-20 14:43:30 org.apache.catalina.core.StandardEngine startInternal
信息: Starting Servlet Engine: Apache Tomcat/7.0.39
2013-8-20 14:43:30 org.apache.catalina.startup.HostConfig deployWAR
信息: Deploying web application archive D:\toolkit\tomcat7\webapps\websocketServer.war
2013-8-20 14:43:30 org.apache.catalina.startup.HostConfig deployDirectory
信息: Deploying web application directory D:\toolkit\tomcat7\webapps\docs
2013-8-20 14:43:30 org.apache.catalina.startup.HostConfig deployDirectory
信息: Deploying web application directory D:\toolkit\tomcat7\webapps\examples
2013-8-20 14:43:31 org.apache.catalina.startup.HostConfig deployDirectory
信息: Deploying web application directory D:\toolkit\tomcat7\webapps\host-manager
2013-8-20 14:43:31 org.apache.catalina.startup.HostConfig deployDirectory
信息: Deploying web application directory D:\toolkit\tomcat7\webapps\manager
2013-8-20 14:43:31 org.apache.catalina.startup.HostConfig deployDirectory
信息: Deploying web application directory D:\toolkit\tomcat7\webapps\ROOT
2013-8-20 14:43:31 org.apache.coyote.AbstractProtocol start
信息: Starting ProtocolHandler ["http-bio-8080"]
2013-8-20 14:43:31 org.apache.coyote.AbstractProtocol start
信息: Starting ProtocolHandler ["ajp-bio-8009"]
2013-8-20 14:43:31 org.apache.catalina.startup.Catalina start
信息: Server startup in 996 ms
```

### websocket的服务地址：

ws://localhost:8080/websocketServer/wsServlet

## 2. 客户端实现(Java-WebSocket)

通过Java实现websocket的客户端，这里将介绍的是”Java-WebSocket”。另外，我发现Java7已经原生支持了websocket, “[JSR 365, Java API for WebSocket](http://www.oracle.com/technetwork/articles/java/jsr356-1937161.html)” (看来要开始学学java7和java8了，我在java6的时代停滞3-4年了。)

现在我们使用“Java-WebSocket”

### 1). 修改pom.xml文件，增加jetty websocket依赖库

```{bash}
~ vi pom.xml
<dependency>
<groupId>org.java-websocket</groupId>
<artifactId>Java-WebSocket</artifactId>
<version>1.3.0</version>
</dependency>
```

### 下载依赖库

```{bash}
~ D:\workspace\java\websocketServer>mvn clean install
```

### 2). 新建文件，ChatClient.java

```{bash}
~ vi src/main/java/org/conan/websocket/ChatClient.java

package org.conan.websocket;

import java.awt.Container;
import java.awt.GridLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.WindowEvent;
import java.net.URI;
import java.net.URISyntaxException;

import javax.swing.JButton;
import javax.swing.JComboBox;
import javax.swing.JFrame;
import javax.swing.JScrollPane;
import javax.swing.JTextArea;
import javax.swing.JTextField;

import org.java_websocket.WebSocketImpl;
import org.java_websocket.client.WebSocketClient;
import org.java_websocket.drafts.Draft;
import org.java_websocket.drafts.Draft_10;
import org.java_websocket.drafts.Draft_17;
import org.java_websocket.drafts.Draft_75;
import org.java_websocket.drafts.Draft_76;
import org.java_websocket.handshake.ServerHandshake;

public class ChatClient extends JFrame implements ActionListener {
    private static final long serialVersionUID = -6056260699202978657L;

    private final JTextField uriField;
    private final JButton connect;
    private final JButton close;
    private final JTextArea ta;
    private final JTextField chatField;
    private final JComboBox draft;
    private WebSocketClient cc;

    public ChatClient( String defaultlocation ) {
        super( "WebSocket Chat Client" );
        Container c = getContentPane();
        GridLayout layout = new GridLayout();
        layout.setColumns( 1 );
        layout.setRows( 6 );
        c.setLayout( layout );

        Draft[] drafts = { new Draft_17(), new Draft_10(), new Draft_76(), new Draft_75() };
        draft = new JComboBox( drafts );
        c.add( draft );

        uriField = new JTextField();
        uriField.setText( defaultlocation );
        c.add( uriField );

        connect = new JButton( "Connect" );
        connect.addActionListener( this );
        c.add( connect );

        close = new JButton( "Close" );
        close.addActionListener( this );
        close.setEnabled( false );
        c.add( close );

        JScrollPane scroll = new JScrollPane();
        ta = new JTextArea();
        scroll.setViewportView( ta );
        c.add( scroll );

        chatField = new JTextField();
        chatField.setText( "" );
        chatField.addActionListener( this );
        c.add( chatField );

        java.awt.Dimension d = new java.awt.Dimension( 300, 400 );
        setPreferredSize( d );
        setSize( d );

        addWindowListener( new java.awt.event.WindowAdapter() {
            @Override
            public void windowClosing( WindowEvent e ) {
                if( cc != null ) {
                    cc.close();
                }
                dispose();
            }
        } );

        setLocationRelativeTo( null );
        setVisible( true );
    }

    public void actionPerformed( ActionEvent e ) {

        if( e.getSource() == chatField ) {
            if( cc != null ) {
                cc.send( chatField.getText() );
                chatField.setText( "" );
                chatField.requestFocus();
            }

        } else if( e.getSource() == connect ) {
            try {
                // cc = new ChatClient(new URI(uriField.getText()), area, ( Draft ) draft.getSelectedItem() );
                cc = new WebSocketClient( new URI( uriField.getText() ), (Draft) draft.getSelectedItem() ) {

                    @Override
                    public void onMessage( String message ) {
                        ta.append( "got: " + message + "\n" );
                        ta.setCaretPosition( ta.getDocument().getLength() );
                    }

                    @Override
                    public void onOpen( ServerHandshake handshake ) {
                        ta.append( "You are connected to ChatServer: " + getURI() + "\n" );
                        ta.setCaretPosition( ta.getDocument().getLength() );
                    }

                    @Override
                    public void onClose( int code, String reason, boolean remote ) {
                        ta.append( "You have been disconnected from: " + getURI() + "; Code: " + code + " " + reason + "\n" );
                        ta.setCaretPosition( ta.getDocument().getLength() );
                        connect.setEnabled( true );
                        uriField.setEditable( true );
                        draft.setEditable( true );
                        close.setEnabled( false );
                    }

                    @Override
                    public void onError( Exception ex ) {
                        ta.append( "Exception occured ...\n" + ex + "\n" );
                        ta.setCaretPosition( ta.getDocument().getLength() );
                        ex.printStackTrace();
                        connect.setEnabled( true );
                        uriField.setEditable( true );
                        draft.setEditable( true );
                        close.setEnabled( false );
                    }
                };

                close.setEnabled( true );
                connect.setEnabled( false );
                uriField.setEditable( false );
                draft.setEditable( false );
                cc.connect();
            } catch ( URISyntaxException ex ) {
                ta.append( uriField.getText() + " is not a valid WebSocket URI\n" );
            }
        } else if( e.getSource() == close ) {
            cc.close();
        }
    }

    public static void main( String[] args ) {
        WebSocketImpl.DEBUG = true;
        String location;
        if( args.length != 0 ) {
            location = args[ 0 ];
            System.out.println( "Default server url specified: \'" + location + "\'" );
        } else {
            location = "ws://localhost:8887";
            System.out.println( "Default server url not specified: defaulting to \'" + location + "\'" );
        }
        new ChatClient( location );
    }
}
```

### 运行程序：

这里会启动一个Java的GUI界面。输入websocket服务的地址：ws://localhost:8080/websocketServer/wsServlet

![](http://blog.fens.me/wp-content/uploads/2013/08/ws6.png)

查看Java客户端和HTML客户端的对话，在Java客户端中，输入”你好，小朋友”。

![](http://blog.fens.me/wp-content/uploads/2013/08/ws4.png)

我们发现在html的客户端中，同样出现的”你好，小朋友”的消息记录。

![](http://blog.fens.me/wp-content/uploads/2013/08/ws5.png)

这样，我们就在Java6的环境中，实现了Java WebSocket的客户端程序。

## 3. 客户端实现(Javascript原生API)

编写一个纯HTML的网页，通过浏览器原生的websocketAPI实现对websocket的服务的调用。

```{bash}
~ vi D:\workspace\javascript\tomcatClient.html

<!DOCTYPE html>
<html>
<head>
<meta charset=UTF-8>
<title>Tomcat WebSocket Chat</title>
<script>
var ws = new WebSocket("ws://localhost:8080/websocketServer/wsServlet");
ws.onopen = function(){
};
ws.onmessage = function(message){
document.getElementById("chatlog").textContent += message.data + "\n";
};
function postToServer(){
ws.send(document.getElementById("msg").value);
document.getElementById("msg").value = "";
}
function closeConnect(){
ws.close();
}
</script>
</head>
<body>
<textarea id="chatlog" readonly></textarea><br/>
<input id="msg" type="text" />
<button type="submit" id="sendButton" onClick="postToServer()">Send!</button>
<button type="submit" id="sendButton" onClick="closeConnect()">End</button>
</body>
</html>
```

通过浏览器刚刚编写的文件：file:///D:/workspace/javascript/tomcatClient.html

打开两个窗口：

![](http://blog.fens.me/wp-content/uploads/2013/08/ws2.png)

在右边窗口输入”我是BBB”，然后点击send。左边，右这，和后台日志，同时增加了”我是BBB”。

![](http://blog.fens.me/wp-content/uploads/2013/08/ws3.png)

原来在浏览器上面，实现聊天功能是如此地简单！！

#### 转载请注明出处：http://blog.fens.me/nodejs-websocket-intro/
