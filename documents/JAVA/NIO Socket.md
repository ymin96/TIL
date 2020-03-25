# NIO Socket

> 블로킹,넌블로킹, 비동기 통신을 지원하는 소켓에 대해 알아본다.

<br>

### TCP 블로킹 채널

NIO를 이용해서 TCP 서버/클라이언트 애플리케이션을 개발하려면 블로킹, 넌블로킹, 비동기 구현 방식 중에서 하나를 결정해야 한다. 먼저 블로킹 방식부터 알아보자.

<br>

#### 서버소켓 채널과 소켓 채널의 용도

NIO에서 TCP 네트워크 통신을 위해 사용되는 채널은 java.nio.channels.ServerSocketChannel과 java.nio.channels.SocketChannel이다. 사용 방법은 IO와 큰 차이점이 없다.

<br>

#### 서버소켓 채널 생성과 연결 수락

서버를 개바하려면 우선 ServerSocketChannel 객체를 얻어야 한다. ServerSocketChannel은 정적 메소드인 open()으로 생성하고, 블로킹 방식으로 동작시키기 위해 configureBlocking(true) 메소드를 호출한다. 기본적으로 블로킹 방식으로 동작하지만 넌블로킹과 구분하기 위해서 해주는 작업이다. 포트에 바인딩하기 위해서는 bind() 메소드가 호출되어야 하는데, 포트 정보를 가진 InetSocketAddress 객체를 매개값으로 주면 된다.

```java
ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
serverSocketChannel.configureBlocking(true);
serverSocketChannel.bind(new InetSocketAddress(5001));
```

포트 바인딩까지 끝났다면 ServerSocketChannel은 클라이언트 연결 수락을 위해 accept() 메소드를 실행해야 한다.

```java
SocketChannel socketChannel = serverSocketChannel.accept();
```

연결된 클라이언트의 IP와 포트 정보를 알고 싶다면 SocketChannel의 getRemoteAddress() 메소드를 호출해서 SocketAddress를 얻으면 된다. 실제 리턴되는 것은 InetSocketAddress 인스턴스이므로 다음과 같이 타입 변환할 수 있다.

```java
InetSocketAddress socketAddress = (InetSocketAddress) socketChannel.getRemoteAddress();
```

InetSocketAddress에는 다음과 같이 IP와 포트 정보를 리턴하는 메소드들이 있다.

| 리턴 타입 | 메소드명(매개 변수) | 설명                             |
| --------- | ------------------- | -------------------------------- |
| String    | getHostName()       | 클라이언트 IP리턴                |
| int       | getPort()           | 클라이언트 포트 번호 리턴        |
| String    | toString()          | "IP:포트번호" 형태의 문자열 리턴 |

더 이상 클라이언트를 위해 연결 수락이 필요 없다면 ServerSocketChannel의 close() 메소드를 호출해서 포트를 언바이딩시켜야 한다. 그래야 다른 프로그램에서 해당 포트를 재사용할 수 있다.

```java
serverSocketChannel.close();
```

<br>

#### 소켓 채널 생성과 연결 요청

클라이언트가 서버에 연결 요청을 할 때에는 java.nio.channels.SocketChannel을 이용한다. SocketChannel은 정적 메소드인 open()으로 생성하고, 블로킹 방식으로 동작한다. 서버 연결 요청은 connect() 메소드를 호출하면 되는데, 서버 IP와 포트 정보를 가진 InetSocketAddress 객체를 매개값으로 주면 된다. connect() 메소드는 연결이 완료될 때까지 블로킹되고, 연결이 완료되면 리턴된다. 다음은 로컬 PC의 5001 포트에 바인딩된 서버에 연결을 요청하는 코드이다.

```java
SocketChannel socketChannel = SocketChannel.open();
socketChannel.configureBlocking(true);
socketChannel.connect(new InetSocketAddress("localhost",5001));
```

연결된 후, 클라이언트 프로그램을 종료하거나, 필요에 따라서 연결을 끊고 싶다면 다음과 같이  SocketChannel의 close() 메소드를 호출하면 된다.

```java
socketChannel.close();
```

<br>

#### 소켓 채널 데이터 통신

클라이언트가 연결 요청하고 서버가 연결 수락했다면, 양쪽 SocketChannel 객체의 read(), write() 메소드를 호출해서 데이터 통신을 할 수 있다. 이 메소드들은 모두 버퍼를 이용하기 때문에 버퍼로 읽고, 쓰는 작업을 해야 한다.

다음은 SocketChannel의 write() 메소드를 이용해서 문자열을 보내는 코드이다.

```java
Charset charset = Charset.forName("UTF-8");
ByteBufferbyteBuffer = charset.encode("Hello Server");
socketChannel.write(byteBuffer);
```

다음은 SocketChannel의 read() 메소드를 이용해서 문자열을 받는 코드이다.

```java
ByteBuffer byteBuffer = ByteBuffer.allocate(100);
int byteCount = socketChannel.read(byteBuffer);
byteBuffer.flip();
Charset charset = Charset.forName("UTF-8");
String message = charset.decode(byteBuffer).toString();
```

