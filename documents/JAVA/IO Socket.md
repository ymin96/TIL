# IO Socket

> 자바에서 동기식 네트워크 전송방법인 IO Socket을 알아본다.

<br>

### InetAddress로 IP 주소 얻기

자바는 IP 주소를 java.net.InetAddress 객체로 표현한다. InetAddress는 로컬 컴퓨터의 IP 주소뿐만 아니라 도메인 이름을 DNS에서 검색한 후 IP주소를 가져오는 기능을 제공한다. 로컬 컴퓨터의 InetAddress를 얻고 싶다면 `InetAddress.getLocalHost()` 메소드를 다음과 같이 호출하면 된다.

```java
InetAddress ia = InetAddress.getLocalHost();
```

외부 컴퓨터의 도메인 이름을 알고 있다면 다음 두 개의 메소드를 사용하여 InetAddress 객체를 얻으면 된다.

```java
InetAddress ia = InetAddress.getByName(String host);
InetAddress[] iaArr = InetAddress.getAllByName(String host);
```

InetAddress 객체에서 IP 주소를 얻기 위해서는 `getHostAddress()` 메소드를 다음과 같이 호출하면 된다. 리턴값은 문자열로 된 IP 주소이다.

```java
String ip = InetAddress.getHostAddress();
```

<br>

<br>

### TCP 네트워킹

자바는 TCP 네트워킹을 위해 java.net.ServerSocket과 java.net.Socket 클래스를 제공하고 있다. 이 두 클래스의 사용 방법을 알아보자.

<br>

#### ServerSocket과 Socket의 용도

서버의 역할은 두 가지로 볼 수 있다. 하나는 클라이언트가 연결 요청을 해오면 연결을 수락하는 것이고, 다른 하나는 연결된 클라이언트와 통신하는 것이다. 자바에서는 이 두 역할별로 별도의 클래스를 제공하고 있다. 클라이언트의 연결 요청을 기다리면서 연결 수락을 담당하는 것이 java.net.ServerSock 클래스이고, 연결된 클라이언트와 통신을 담당하는 것이 java.net.Socket 클래스이다. 클라이언트가 연결 요청을 해오면 ServerSocket은 연결을 수락하고 통신용 Socket을 만든다.

<br>

#### ServerSocket 생성과 연결 수락

ServerSocket을 얻는 가장 간단한 방법은 생성자에 바인딩 포트를 대입하고 객체를 생성하는 것이다. 다음은 5001번 포트에 바인딩하는 ServerSocket을 생성한다.

```java
ServerSocket serverSocket = new ServerSocket(5001);
```

ServerSocket을 얻는 다른 방법은 디폴트 생성자로 객체를 생성하고 포트 바인딩을 위해 bind( )메소드를 호출하는 것이다. bind( ) 메소드의 매개값은 포트 정보를 가진 InetSocketAddress 이다.

```java
ServerSocket serverSocket = new ServerSocket();
serverSocket.bind( new InetAddress(5001));
```

포트 바인딩이 끝났다면 ServerSocket은 클라이언트 연결 수락을 위해 accept() 메소드를 실행해야 한다. accept()메소드는 클라이언트가 연결 요청하기 전까지 블로킹된다. 클라이언트가 연결 요청을 하면 accept()는 클라이언트와 통신할 Socket을 만들고 리턴한다.

연결된 클라이언트의 IP와 포트 정보를 알고 싶다면 Socket의 getRemoteSocketAddress() 메소드를 호ㅜㄹ해서 SocketAddress를 얻으면 된다. 실제 리턴되는 것은 InetSocketAddress 객체이므로 다음과 같이 타입 변환할 수 있다.

```java
InetSocketAddress socketAddress = (InetSocketAddress) socket.getRemoteSocketAddress();
```

InetSocketAddress 에는 IP와 포트 정보를 리턴하는 다음과 같은 메소드들이 있다.

| 리턴 타입 | 메소드명(매개 변수) | 설명                             |
| --------- | ------------------- | -------------------------------- |
| String    | getHostName( )      | 클라이언트 IP 리턴               |
| int       | getPort( )          | 클라이언트 포트 번호 리턴        |
| String    | toString( )         | "IP:포트번호" 형태의 문자열 리턴 |

더 이상 클라이언트 연결 수락이 필요 없으면 ServerSocket의 close( ) 메소드를 호출해서 포트를 언바인딩시켜야 한다. 그래야 다른 프로그램에서 해당 포트를 재사용할 수 있다.

```java
serverSocket.close();	
```

<br>

#### Socket 생성과 연결 요청

클라이언트가 서버에 연결 요청을 하려면 java.net.Socket을 이용해야 한다. Socket 객체를 생성함과 동시에 연결 요청을 하려면 생성자의 매개값으로 서버의 IP 주소와 바인딩 포트 번호를 제공하면 된다. 다음은 로컬 PC의 5001 포트에 연결 요청하는 코드이다.

```java
try{
	Socket socket = new Socket("localhost", 5001);	//방법1
	Socket socket = new Socket( new InetSocketAddress("localhost", 5001) );	//방법2
}catch (UnknownHostException e){
	//IP표기 방법이 잘못되었을 경우
} catch (IOException e){
	//해당 포트의 서버에 연결할 수 없는 경우
}
```

Socket  생성과 동시에 연결 요청을 하지 않고, 다음과 같이 기본 생성자로 Socket을 생성한 후, connect( ) 메소드로 연결 요청을 할 수도 있다.

```java
socket = new Socket();
socket.connect( new InetSocketAddress("localhost",5001) );
```

연결된 후, 클라이언트 프로그램을 종료하거나, 강제적으로 연결을 끊고 싶다면 Socket의 close() 메소드를 다음과 같이 호출하면 된다. close() 메소드도 IOException이 발생할 수 있기 때문에 예외 처리가 필요하다.

```java
try{
	socket.close();
} catch (IOExceptione){ }
```

<br>

#### Socket 데이터 통신

클라이언트가 연결 요청(connect( ))하고 서버가 연결 수락(accept())했다면, 양쪽의 Socket객체로부터 각각 입력 스트림(InputStream)과 출력 스트림(OutputStream)을 얻을 수 있다.

다음은 Socket으로부터 InputStream과 OutputStream을 얻는 코드이다.

```java
//입력 스트림 얻기
InputStream is = socket.getInputStream();

//출력 스트림 얻기
OutputStream os = socket.getOutputStream();
```

상대방에게 데이터를 보내기 위해서는 보낼 데이터를 byte[] 배열로 생성하고, 이것을 매개값으로 해서 OutputStream의 write() 메소드를 호출하면 된다. 다음은 문자열을 UTF-8로 인코딩한 바이트 배열을 얻어내고, write() 메소드로 전송한다.

```java
String data = "보낼 데이터";
byte[] byteArr = data.getBytes("UTF-8");
OutputStream outputStream = socket.getOutputStream();
outputStream.write(byteArr);
outputStream.flush();
```

상대방이 보낸 데이터를 받기 위해서는 받은 데이터를 저장할 byte[]배열을 하나 생성하고, 이것을 매개값으로 해서 InputStream의 read() 메소드를 호출하면 된다. read() 메소드는 읽은 데이터를 byte[] 배열에 저장하고 읽은 바이트 수를 리턴한다. 다음은 데이터를 읽고 UTF-8로 디코딩 한 문자열을 얻는 코드이다.

```java
byte[] byteArr = new byte[100];
InputStream inputStream = socket.getInputStream();
int readByteCount = inputStream.read(byteArr);
String data = new String(byteArr, 0, readByteCount, "UTF-8");
```

