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

<br>

<br>

### TCP 넌블로킹 채널

넌블로킹 방식의 특징과 넌블로킹 핵심 객체인 셀렉터(Selector)를 이해하고 채널을 넌블로킹 방식으로 사용하는 방법에 대해 알아보자.

<br>

#### 넌블로킹 방식의 특징

넌블로킹 방식은 connect(), accept(), read(), write() 메소드에서 블로킹이 없다. 클라이언트의 연결 요청이 없으면 accept()는 즉시 null을 리턴한다. 그리고 클라이언트가 데이터를 보내지 않으면 read()는 즉시 0을 리턴하고, 매개값으로 전달한 ByteBuffer에는 어떤 데이터도 저장되지 않는다. 

따라서 넌블로킹은 이벤트 리스너 역할을 하는 셀렉터(Selector)를 사용한다. 넌블로킹 채널에 Selector를 등록해 놓으면 클라이언트의 연결 요청이 들어오거나 데이터가 도착할 경우, 채널은 Selector에 통보한다. Selector는 통보한 채널들을 선택해서 작업 스레드가 accept() 또는 read() 메소드를 실행해서 즉시 작업을 처리하도록 한다.

Selector는 멀티 채널의 작업을 싱글 스레드에서 처리할 수 있도록 해주는 멀티플렉서(multiplexor)역할을 한다. 

1. 채널은 Selector에 자신을 등록할 때 작업 유형을 키(SelectorKey)로 생성하고, Selector의 관심키셋(interest-set)에 저장시킨다. 
2. 클라이언트가 처리 요청을 하면
3. Selector는 관심키셋에 등록된 키 중에서 작업 처리 준비가 된 키를 선택해 키셋(selected-set)에 별도로 저장한다.
4. 그리고 작업 스레드가 선택된 키셋에 있는 키를 하나씩 꺼내어 키와 연관된 채널 작업을 처리하게 된다. 작업 스레드가 선택된 키셋에 있는 모든 키를 처리하게 되면 선택된 키셋은 비워지고, Selector는 다시 관심키셋에서 작업 처리 준비가 된 키들을 선택해서 선택된 키셋을 채운다.

<br>

#### 셀렉터 생성과 등록

Selector은 정적 메소드인 open() 메소드를 호출하여 생성한다. open() 메소드는 IOException이 발생할 수 있기 때문에 예외 처리가 필요하다.

```java
try{
	Selector selector = Selector.open();
} catch (IOException e) {}
```

Selector에 등록할 수 있는 채널은 SelectableChannel의 하위 채널만 가능한데, TCP 통신에 사용되는 ServerSocketChannel, SocketChannel과 UDP 통신에 사용되는 DatagramChannel은 모두 SelectableChannel의 하위 클래스이므로 Selector에 등록할 수 있다. 주의할 점은 넌블로킹으로 설졍된 것만 가능하다. 다음은 ServerSocketChannel을 넌블로킹으로 설정하는 코드이다.

```java
ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
serverSocketChannel.configureBlocking(false);
```

다음은 SocketChannel을 넌블로킹으로 설정하는 코드이다.

```java
SocketChannel socketChannel = SocketChannel.open();
socketChannel.configureBlocking(false);
```

각 채널은 register() 메소드를 이용해서 Selector에 등록하는데, 첫 번째 매개값은 Selector이고 두 번째 매개값은 채널의 작업 유형이다.

```java
SelectionKey selectionKey = serverSocketChannel.register(Selector sel, int ops);
SelectionKey selectionKey = socketChannel.register(Selector sel, int ops);
```

다음은 두 번째 매개값으로 사용할 수 있는 작업 유형별 SelectionKey의 상수들이다.

| SelectionKey의 상수 | 설명                                 |
| ------------------- | ------------------------------------ |
| OP_ACCEPT           | ServerSocketChannel의 연결 수락 작업 |
| OP_CONNECT          | SocketChannel의 서버 연결 작업       |
| OP_READ             | SocketChannel의 데이터 읽기 작업     |
| OP_WRITE            | SocketChannel의 데이터 쓰기 작업     |

register()는 채널과 작업 유형 정보를 담고 있는 SelectionKey를 생성하고 Selector의 관심키셋에 저장한 후 해당 SelectionKey를 리턴한다.

주의할 점은 동일한 SocketChannel로 두 가지 이상의 작업 유형을 등록할 수 없다. 즉, register()를 두 번 이상 호출할 수 없다. 등록은 한 번만 하되, 작업 유형이 변경되면 이미 생성된 SelectionKey를 수정해야 한다.

register()가 리턴한 SelectionKey는 작업 유형 변경, 첨부 객체 저장, 채널 등록 취소 등을 할 때 사용된다. SelectionKey를 별도로 관리할 필요는 없는데, 채널이 Selector를 등록하면 채널의 keyFor() 메소드로 SelectionKey를 언제든지 받을 수 있다.

```java
SelectionKey key = socketChannel.keyFor();
```

<br>

#### 선택된 키셋

Selector를 구동하려면 select() 메소드를 호출해야 한다. select()는 관심키셋에 저장된 SelectionKey로부터 작업 처리 준비가 되었다는 통보가 올 때까지 대기(블로킹)한다. 최소한 하나의 SelectionKey로부터 작업 처리 준비가 되었다는 통보가 오면 리턴한다. 이때 리턴값은 통보를 해온 SelectionKey의 수이다. 다음은 세 가지 종류의 select() 메소드를 설명한 표이다.

| 리턴 타입 | 메소드명(매개 변수)  | 설명                                                         |
| --------- | -------------------- | ------------------------------------------------------------ |
| int       | select()             | 최소한 하나의 채널이 작업 처리 준비가 될 때까지 블로킹된다.  |
| int       | select(long timeout) | select()와 동일한데, 주어진 시간(밀리세컨드) 동안만 블로킹된다. |
| int       | selectNow()          | 호출 즉시 리턴된다. 작업 처리 준비된 채널이 있으면 채널 수를<br />리턴하고, 없다면 0을 리턴한다. |

select()가 리턴하는 경우는 다음 세 가지이다.

* 채널이 작업 처리 준비가 되었다는 통보를 할 때
* Selector의 wakeup() 메소드를 호출할 때
* select()를 호출한 스레드가 인터럽트될 때

SocketChannel은 상황에 따라서 읽기 작업과 쓰기 작업을 번갈아가며 작업 유형을 변경할 수 있다. 채널의 작업 유형이 변경되면 SelectionKey의 작업 유형을 interestOps() 메소드로 변경해야 작업 스레드가 올바르게 채널 작업을 할 수 있다. 다음은 SelectionKey의 작업 유형을 OP_WRITE로 변경하는 코드이다.

```java
selectionKey.interestOps(SelectionKey.OP_WRITE);
selector.wakeup();
```

SelectionKey의 작업 유형이 변경되면 Selector의 wakeup() 메소드를 호출해서 블로킹되어 있는 select()를 즉시 리턴하고, 변경된 작업 유형을 감지하도록 select()를 다시 실행해야 한다. 다음은 select() 메소드가 1 이상의 값을 리턴할 경우, selectedKeys() 메소드로 작업 처리 준비된 SelectionKey들을 Set 컬렉션으로 얻는다. 이것이 선택된 키셋이다.

```java
int keyCount = selector.select();
if(keyCount > 0){
	Set<SelectionKey> selectedKeys = selector.selectedKeys();
}
```

<br>

#### 채널 작업 관리

작업 스레드가 해야 할 일은 선택된 키셋에서 SelectionKey를 하나씩 꺼내어 작업 유형별로 채널 작업을 처리하는 것이다.  SlectionKey가 어떤 작업 유형인지 알아내는 방법은 SelectionKey의 다음 메소드 중에서 어떤 것이 true를 리턴하는지 조사하면 된다.

| 리턴 타입 | 메소드(매개 변수) | 설명                          |
| --------- | ----------------- | ----------------------------- |
| boolean   | isAcceptable()    | 작업 유형이 OP_ACCEPT인 경우  |
| boolean   | isConnectable()   | 작업 유형이 OP_CONNECT인 경우 |
| boolean   | isReadable()      | 작업 유형이 OP_READ인 경우    |
| boolean   | isWritable()      | 작업 유형이 OP_WRITE인 경우   |

다음은 작업 스레드가 작업 유형별로 채널 작업을 처리하는 방법을 보여준다.

```java
Thread thread = new Thread() {
	@Override
	public void run(){
		while(true){
			try{
				int keyCount = selector.select();
				if(keyCount == 0){continue;}
				Set<SelectionKey> selectedKeys = selector.selectedKeys();
				Iterator<SelectionKey> iterator = selectedKeys.iterator();
				while(iterator.hasNext()){
					SelectionKey selectionKey = iterator.next();
					if(selectionKey.isAcceptable()){//연결 수락 작업 처리}
					else if(selectionKey.isReadable()){//읽기 작업 처리}
					else if(selectionKey.isWritable()){//쓰기 작업 처리}
					iterator.remove();
				}catch(Exception e){
					break;
				}
			}
		}
	}
};
thread.start();
```

작업 스레드가 채널 작업을 처리하려면 채널 객체가 필요한데, SelectionKey의 channel() 메소드를 호출하면 얻을 수 있다. 다음은 작업 유형이 OP_ACCEPT일 경우, 연결 수락 작업 처리에서 필요한 ServerSocketChannel을 얻는 코드이다.

```java
ServerSocketChannel serverSocketChannel = (ServerSocketChannel) selectionKey.channel();
```

작업 스레드가 채널 작업을 처리하다 보면 채널 객체 이외에 다른 객체가 필요할 수도 있다. 이러한 객체는 SelectionKey에 첨부해 두고, 사용할 수 있다. SelectionKey의 attach() 메소드는 객체를 첨부하고, attachment() 메소드는 첨부된 객체를 얻을 때 사용한다. 다음은 Client 객체를 SelectionKey에 첨부하고, 얻어내는 코드이다.

```java
[객체 첨부하기]
Client client = new Client(socketChannel);
SelectionKey selectionKey = socketChannel.register(selector, SelectionKey.OP_READ);
selectionKey.attach(client);

[첨부된 객체 얻기]
if (seelctionKey.isReadable()){
	Client client = (Client)selectionKey.attachment();
}
```

<br>

<br>

### TCP 비동기 채널

NIO는 TCP 비동기 채널로, AsynchronousServerSocketChannel과 AsynchronousSocketChannel을 제공한다. 각각 ServerSocketChannel과 SocketChannel에 대응하는 클래스이다. 이 두 클래스의 사용 방법을 알아보자.

<br>

#### 비동기 채널의 특징

TCP 비동기 채널은 연결 요청, 연결 수락, 읽기, 쓰기를 호출하면 즉시 리턴된다. 이것은 넌블로킹 방식과 동일하다. 차이점은 이 메소드들을 호출하면 스레드풀에게 작업 처리를 요청하고 이 메소드들은 즉시 리턴된다. 실질적인 작업 처리는 스레드풀의 작업 스레드가 담당한다. 작업 스레드가 작업을 완료하게 되면 콜백(callback) 메소드가 자동 호출되기 때문에 작업 완료 후 실행해야 할 코드가 있다면 콜백 메소드에 작성하면 된다.

<br>

#### 비동기 채널 그룹

비동기 채널 그룹은 같은 스레드풀을 공유하는 비동기 채널들의 묶음이라고 볼 수 있다. 하나의 스레드풀을 사용한다면 모든 비동기 채널은 같은 채널 그룹에 속해야 한다. 

비동기 채널을 생성할 때 채널 그룹을 지정하지 않으면 기본 비동기 채널 그룹이 생성된다. 기본 비동기 채널 그룹은 내부적으로 다음과 같이 스레드풀을 생성한다.

```java
new ThraedPoolExecutor(
	0, Integer.MAX_VALUE,
	Long.MAX_VALUE, TimeUnit.MILLSECONDS,
	new SynchronousQueu<Runnable>(),
	threadFactory);
```

이론적으로 Integer.MAX_VALUE 개만큼의 스레드가 증가할 수 있도록 되어 있다. 하지만 스레드풀은 대부분 최대 스레드 수를 지정해서 사용하므로 다음과 같이 AsynchronousChannelGroup을 직접 생성하고 사용하는 것이 일반적이다.

```java
AsynchronousChannelGroup channelGroup = AsynchronousChannelGroup.withFixedThreadPool(
	최대스레드수,
	Executors.defaultThreadFactory()
);
```

다음은 CPU 코어의 수만큼 스레드를 관리하는 스레드풀을 생성하고 이것을 이용하는 비동기 채널 그룹을 생성한다.

```java
AsynchronousChannelGroup channelGroup = AsynchronousChannelGroup.withFixedThreadPool(
	Runtime.getRuntime().availableProccessor(),
	Executors.defaultThreadFactory()
);
```

이렇게 생성된 비동기 채널 그룹은 비동기 채널을 생성할 때 매개값으로 사용된다. 비동기 채널 그룹을 더 이상 사용하지 않고 종료할 경우에는 shutdown()과 shutdownNow() 메소드를 호출할 수 있다.

```java
channelGroup.shutdown();
channelGroup.shutdownNow();
```

<br>

#### 비동기 서버소켓 채널

기본 비동기 채널 그룹에 포함되는 AsynchronousServerSocketChannel을 얻는 방법은 다음과 같이 매개값 없는 open() 메소드를 호출하는 것이다.

```java
AsynchronousServerSocketChannel asynchronousServerSocketChannel =
AsynchronousServerSocketChannel.open();
```

별도로 비동기 채널 그룹을 생성하고 여기 포함되는 AsynchronousServerSocketChannel을 얻고 싶다면 다음과 같이 비동기 채널 그룹을 매개값으로 갖는 open() 메소드를 호출하면 된다.

```java
AsynchronousChannelGroup channelGroup = AsynchronousChannelGroup.withFixedThreadPool(
	Runtime.getRuntime().availableProccessor(),
	Executors.defaultThreadFactory()
);

AsynchronousServerSocketChannel asynchronousServerSocketChannel =
AsynchronousServerSocketChannel.open(channelGroup);
```

AsynchronousServerSocketChannel을 생성하고 나서는 포트 바인딩을 위해 다음과 같이 bind() 메소드를 호출해야 한다.

```java
asynchronousServerSocketChannel.bind(new InetSocketAddress(5001));
```

AsynchronousServerSocketChannel을 더 이상 사용하지 않을 경우에는 close() 메소드를 호출해서 서버가 사용한 포트를 언바인딩 해준다.

```java
asynchronousServerSocketChannel.close();
```

AsynchronousServerSocketChannel은 연결 수락 작업을 스레드풀을 이용해서 비동기로 처리한다. 다음은 accept() 메소드를 호출하는 코드이다.

```java
accept(A atachment, CompletionHandler<AsynchronousSocketChannel, A> handler);
```

첫 번째 매개값은 콜백 메소드의 매개값으로 제공할 첨부 객체인데, 연결 수락 작업에는 별도의 첨부 객체가 필요하지 않기 때문에 null을 지정한다. 두 번째 매개값은 콜백 메소드를 가지고 있는 CompletionHandler&#60;AsynchronousSocketChannel, A> 구현 객체이다. A는 첨부 객체 타입인데, 연결 수락 작업에는 별도의 첨부 객체가 필요하지 않기 때문에 Void로 지정한다. 다음은 accept() 메소드를 호출하는 기본 뼈대를 보여준다.

```java
asynchronousServerSocketChannel.accept(null,
	new CompletionHandler<AsynchronousSocketChannel, Void>(){
	@Override
	public void completed(AsynchronousSocketChannel asynchronousSocketChannel,
										Void attachment){
		//연결 수락 후 실행할 코드
        asynchronousServerSocketChannel.accept(null, this);
	}
	@Override
	publid void failed(Throwable exc, Void attachment){
		//연결 수락 실패 시 실행할 코드
	}
});
```

위 코드에서 주목할 점은 accept()를 반복해서 호출하는 무한 루프가 없다는 것이다. 대신 completed() 메소드 끝에 accept() 를 재호출해서 반복적으로 클라이언트 연결 수락 작업을 수행한다.

<br>

#### 비동기 소켓 채널

AsynchronousServerSocketChannel이 생성하는 AsynchronousSocketChannel은 자동적으로 AsynchronousServerSocketChannel과 같은 비동기 채널 그룹에 속하게 된다.

클라이언트에서 AsynchronousSocketChannel을 생성하려면 두 가지 open() 메소드를 사용할 수 있다. 기본 비동기 채널에 포함되는 AsynchronousSocketChannel을 얻고 싶다면 매개값 없는 open() 메소드를 호출하면 된다.

```java
AsynchronousSocketChannel asynchronousSocketChannel = AsynchronousSocketChannel.open()
```

별도로 비동기 채널 그룹을 생성하고 여기에 포함되는 AsynchronousSocketChannel을 얻고 싶다면 다음과 같이 비동기 채널 그룹을 매개값으로 갖는 open() 메소드를 호출하면 된다.

```java
AsynchronousSocketChannel asynchronousSocketChannel = 
AsynchronousSocketChannel.open(channelGroup)
```

AsynchronousSocketChannel을 더 이상 사용하지 않을 경우에는 close() 메소드를 호출해서 연결을 끊어준다.

```java
asynchronousSocketChannel.close();
```

클라이언트가 생성하는 AsynchronousSocketChannel은 서버 연결 요청 작업을 스레드풀을 이용해서 비동기로 처리한다. 다음은 connect() 메소드를 호출하는 코드이다.

```java
connect(SocketAddress remote, A attachment, CompletionHandler<Void, A> handler);
```

첫 번째 매개값은 서버IP와 연결 포트 정보를 가진 InetSocketAddress 객체이다. 두 번째 매개값은 콜백 메소드의 매개값으로 제공할 첨부 객체인데, 연결 요청 작업에는 별도의 첨부 객체가 필요하지 않기 때문에 null을 지정한다.  세 번째 매개값은 CompletionHandler&#60;Void, A> 구현 객체이다. A는 첨부 객체 타입인데, 연결 요청 작업에는 별도의 첨부 객체가 필요하지 않기 때문에 Void로 지정한다. 다음은 connect() 메소드를 호출하는 기본 뼈대를 보여준다.

```java
asynchronousSocketChannel.connect(new InetSocketAddress("localhost", 5001), null,
	new CompletionHandler<Void, Void>(){
	@Override
	public void completed(Void result, Void attachment){
		//연결 성공후 실행할 코드
	}
	@Override
	public void failed(Throwable e, Void attachment){
		//연결 실패후 실행할 코드
	}
});
```

completed() 메소드는 연결이 성공했을 때 스레드풀의 스레드를 호출한다. 첫 번째 매개값은 null이 대입되고, 두 번째 매개값은 첨부 객체인데 connect() 의 두 번째 매개값이 null이므로 null이 대입된다. completed() 메소드에는 서버가 보낸 데이터를 받기 위한 코드가 일반적으로 작성된다.

<br>

#### 비동기 소켓 채널 데이터 통신

클라이언트와 서버가 연결되면 양쪽 AsynchronousSocketChannel의 read() 와 write() 메소드로 데이터 통신을 할 수 있다. 이 메소드들은 호출하는 즉시 리턴되고, 실질적인 입출력 작업은 스레드풀의 스레드가 담당한다. 다음은 read() 와 write()를 호출하는 코드이다.

```java
read(ByteBuffer dst, A attachment, CompletionHandler<Integer, A> handler);
write(ByteBuffer src, A attachment, CompletionHandler<Integer, A> handler);
```

첫 번째 매개값은 읽고 쓰기 위한 ByteBuffer 객체이다. 두 번째 매개값은 콜백 메소드의 매개값으로 제공할 첨부 객체이다. 세 번째 매개값은 CompletionHandler&#60;Integer, A> 구현 객체이다. Integer은 읽고 쓴 바이트 수이고, A는 첨부 객체 타입이다. 다음은 read() 메소드를 호출하는 기본 뼈대를 보여준다.

```java
asynchronousSocketChannel.read(byteBuffer, attachment,
	new CompletionHandler<Integer, A>(){
	@Override
	public void completed(Integer result, A attachment){
		//받은 데이터를 처리하는 코드
		asynchronousSocketChannel.read(byteBuffer, attachment, this);
	}
	@Override
	public void failed(Throwable exc, A attachment){
		//실패된 경우 실행할 코드
	}
});
```

위 코드에서 주목할 점은 read()를 반복해서 호출하는 무한 루프가 없다는 점이다. 대신 completed() 메소드 끝에 read()를 재호출해서 반복적으로 데이터를 받는 작업을 수행한다. 다음은 write() 메소드를 호출하는 기본 뼈대를 보여준다.

```java
asynchronousSocketChannel.write(byteBuffer, attachment,
	new CompletionHandler<Integer, A>(){
	@Override
	public void completed(Integer result, A attachment){
		//성공한 경우 실행할 코드
	}
	@Override
	public void failed(Throwable exc, A attachment){
		//실패된 경우 실행할 코드
	}
});
```

