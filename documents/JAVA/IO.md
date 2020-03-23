# IO

> Java IO 패키지에 대해 알아본다.

<br>

### 입력 스트림과 출력 스트림

자바의 기본적인 데이터 입출력(IO:Input/Output) API는 java.io 패키지에서 제공하고 있다. java.io 패키지에는 파일 시스템의 정보를 얻기 위한 File 클래스와 데이터를 입출력하기 위한 다양한 입출력 스트림 클래스를 제공하고 있다.

| java.io 패키지의 주요 클래스                                 | 설명                                                  |
| ------------------------------------------------------------ | ----------------------------------------------------- |
| File                                                         | 파일 시스템의 파일 정보를 얻기 위한 클래스            |
| Console                                                      | 콜솔로부터 문자를 입출력하기 위한 클래스              |
| InputStream / OutputStream                                   | 바이트 단위 입출력을 위한 최상위 입출력 스트림 클래스 |
| FileInputStream / FileOutputStream<br />DataInputStream / DataOutputStream<br />ObjectInputStream / ObjectOutputStream<br />PrintStream<br />BufferedInputStream<br />BufferedOutputStream | 바이트 단위 입출력을 위한 하위 스트림 클래스          |
| Reader / Writer                                              | 문자 단위 입출력을 위한 하위 스트림 클래스            |
| FileReader / FileWriter<br />InputStreamReader / OutputStreamWriter<br />PrintWriter<br />BufferedReader / BufferedWriter | 문자 단위 입출력을 위한 하위 스트림 클래스            |

<br>

#### InputStream

InputStream은 바이트 기반 입력 스트림의 최상위 클래스로 추상 클래스이다. 모든 바이트 기반 입력 스트림은 이 클래스를 상속받아서 만들어진다. 다음과 같은 형태를 가지고 있다.

![1](../img/JAVA/IO/1.PNG)

InputStream 클래스에는 바이트 기반 입력 스트림이 기본적으로 가져야 할 메소드가 정의되어 있다. 다음은 InputStream 클래스의 주요 메소드이다.

| 리턴 타입 | 메소드                                           | 설명                                                         |
| --------- | ------------------------------------------------ | ------------------------------------------------------------ |
| int       | read( )                                          | 입력 스트림으로부터 1바이트를 읽고 읽은 바이트를 리턴한다.   |
| int       | read(byte[] b)                                   | 입력 스트림으로부터 읽은 바이트들을 매개값으로 주어진 바이트<br />배열 b에 저장하고 실제로 읽은 바이트 수를 리턴한다. |
| int       | read(<br />byte[] b,<br />int off,<br />int len) | 입력 스트림으로부터 len 개의 바이트만큼 읽고 매개값으로 주어진<br />바이트 배열 b[off]부터 len개까지 저장한다. 그리고 실제로 읽은 바이트<br />수인 len개를 리턴한다. 만약 len개를 모두 읽지 못하면 실제로 읽은<br />바이트 수를 리턴한다. |
| void      | close( )                                         | 사용한 시스템 자원을 반납하고 입력 스트림을 닫는다.          |

<br>

#### OutputStream

OutputStream은 바이트 기반 출력 스트림의 최상위 클래스로 추상 클래스이다. 모든 바이트 기반 출력 클래스는 이 클래스를 상속받아서 만들어진다. 다음과 같은 형태를 가지고 있다.

![2](../img/JAVA/IO/2.PNG)

OutputStream 클래스에는 모든 바이트 기반 출력 스트림이 기본적으로 가져야 할 메소드가 정의되어 있다. 다음은 OutputStream 클래스의 주요 메소드이다.

| 리턴 타입 | 메소드                                            | 설명                                                         |
| --------- | ------------------------------------------------- | ------------------------------------------------------------ |
| void      | write(int b)                                      | 출력 스트림으로 1바이트를 보낸다(b의 끝 1바이트)             |
| void      | write(byte[] b)                                   | 출력 스트림으로 주어진 바이트 배열 b의 모든 바이트를 보낸다. |
| void      | write(<br />byte[] b,<br />int off,<br />int len) | 출력 스트림으로 주어진 바이트 배열 b[off]부터 len개까지의<br />바이트를 보낸다. |
| void      | flush( )                                          | 버퍼에 잔류하는 모든 바이트를 출력한다.                      |
| void      | close( )                                          | 사용한 시스템 자원을 반납하고 출력 스트림을 닫는다.          |



<br>

#### Reader

Reader는 문자 기반 입력스트림의 최상위 클래스로 추상 클래스이다. 모든 문자 기반 입력 스트림은 이 클래스를 상속받아서 만들어진다. 다음과 같은 형태로 되어있다.

![2](../img/JAVA/IO/3.PNG)

Reader 클래스에는 문자 기반 입력 스트림이 기본적으로 가져야 할 메소드가 정의되어 있다. 다음은 Reader 클래스의 주요 메소드이다.

| 리턴 타입 | 메소드                                              | 설명                                                         |
| --------- | --------------------------------------------------- | ------------------------------------------------------------ |
| int       | read( )                                             | 입력 스트림으로부터 한 개의 문자를 일고 리턴한다.            |
| int       | read(char[] cbuf)                                   | 입력 스트림으로부터 읽은 문자들을 매개값으로 주어진 문자 배열 cbuf에<br />저장하고 실제로 읽은 문자 수를 리턴한다. |
| int       | read(<br />char[] cbuf,<br />int off,<br />int len) | 입력 스트림으로부터 len개의 문자를 읽고 매개값으로 주어진 문자 배열<br />cbuf[off]부터 len개까지 저장한다.<br />그리고 실제로 읽은 문자 수인 len개를 리턴한다. |
| void      | close( )                                            | 사용한 시스템 자원을 반납하고 입력 스트림을 닫는다.          |

<br>

#### Writer

Writer는 문자 기반 출력 스트림의 최상위 클래스로 추상 클래스이다. 모든 문자 기반 출력 스트림 클래스는 이 클래스를 상속받아서 만들어진다. 형태는 다음과 같다. 

![4](../img/JAVA/IO/4.PNG)

Writer 클래스에는 모든 문자 기반 출력 스트림이 기본적으로 가져야 할 메소드가 정의되어 있다. Writer 클래스의 주요 메소드는 다음과 같다.

| 리턴 타입 | 메소드                                    | 설명                                                         |
| --------- | ----------------------------------------- | ------------------------------------------------------------ |
| void      | write(int c)                              | 출력 스트림으로 주어진 한 문자를 보낸다(c의 끝 2바이트)      |
| void      | write(char[] cbuf)                        | 출력 스트림으로 주어진 문자 배열 cbuf의 모든 문자를 보낸다.  |
| void      | write(char[] cbuf,<br />int off, int len) | 출력 스트림으로 주어진 문자 배열 cbuf[off]부터 len개까지의<br />문자를 보낸다. |
| void      | write(String str)                         | 출력 스트림으로 주어진 문자열을 전부 보낸다.                 |
| void      | write(String str,<br />int off, int len)  | 출력 스트림으로 주어진 문자열 off순번부터 len개까지의<br />문자를 보낸다. |
| void      | flush( )                                  | 버퍼에 잔류하는 모든 문자열을 출력한다.                      |
| void      | close( )                                  | 사용한 시스템 자원을 반납하고 출력 스트림을 닫는다.          |

<br>

<br>

### 보조 스트림

보조 스트림이란 다른 스트림과 연결되어 여러 가지 편리한 기능을 제공해주는 스트림을 말한다. 보조 스트림을 필터(filter) 스트림이라고도 하는데, 이는 보조 스트림의 일부가 FilterInputStream, FilterOutputStream의 하위 클래스이기 때문이다. 

보조 스트림은 자체적으로 입출력을 수행할 수 없기 때문에 입력 소스와 바로 연결되는 InputStream, FileInputStream, Reader, FileReader, 출력 소스와 바로 연결되는 OutputStream, FileOutputStream, Writer, FileWriter 등에 연결해서 입출력을 수행한다. 보조 스트림은 문자 변환, 입출력 성능 향상, 기본 데이터 타입 입출력, 객체 입출력 등의 기능을 제공한다.

보조 스트림을 생성할 때에는 자신이 연결될 스트림을 다음과 같이 생성자의 매개값으로 받는다.

```java
보조스트림 변수 = new 보조스트림(연결스트림)
```

예를 들어 콘솔 입력 스트림을 문자 변환 보조 스트림인 InputStreamReader에 연결하는 코드는 다음과 같다.

```java
InputStream is = System.in;
InputStream reader = new InputStreamReader(is);
```

<br>

#### 문자 변환 보조 스트림

소스 스트림이 바이트 기반 스트림이면서 입출력 데이터가 문자라면 Reader 와 Writer로 변환해서 사용하는 것을 고려해야 한다. 그 이유는 Reader와 Writer는 문자 단위로 입출력하기 때문에 바이트 기반 스트림보다는 편리하고, 문자셋의 종류를 지정할 수 있기 때문에 다양한 문자를 입출력할 수 있다.

##### InputStreamReader

InputStreamReader는 바이트 입력 스트림에 연결되어 문자 입력 스트림인 Reader로 변환시키는 보조 스트림이다.

```java
Reader reader = new InputStreamReader(바이트입력스트림);
```

##### OutputStreamWriter

OutputStreamWriter는 바이트 출력 스트림에 연결되어 문자 출력 스트림인 Writer로 변환시키는 보조 스트림이다.

```java
Writer writer = new OutputStreamWriter(바이트출력스트림);
```

<br>

#### 성능 향상 보조 스트림

보조 스트림 중에서는 메모리 버퍼를 제공하여 프로그램의 실행 성능을 향상시키는 것들이 있다. 바이트 기반 스트림에는 BufferedInputStream, BufferedOutputStream이 있고, 문자 기반 스트림에는 BufferedReader, BufferedWriter가 있다. 

##### BufferedInputStream과 BufferedReader

BufferedInputStream과 BufferedReader는 입력 소스로부터 자신의 내부 버퍼 크기만큼 데이터를 미리 읽고 버퍼에 저장해 둔다. 프로그램은 외부의 입력 소스로부터 직접 읽는 대신 버퍼로부터 읽음으로써 읽기 성능이 향상된다.

이 보조 스트림은 다음과 같이 생성자의 매개값으로 준 입력 스트림과 연결되어 8192내부 버퍼 사이즈를 가지게 된다. BufferedInputStream은 최대 8192바이트가, BufferedReader는 최대 8192 문자가 저장될 수 있다.

```java
BufferedInputStream bis = new BufferedInputStream(바이트입력스트림);
BufferedReader br = new BufferedReader(문자입력스트림);
```

데이터를 읽어들이는 방법은 InputStream  또는 Reader와 동일하다. 

##### BufferedOutputStream과 BufferedWriter

BufferedOutputStream과 BufferedWriter는 프로그램에서 전송한 데이터를 내부 버퍼에 쌓아두었다가 버퍼가 꽉 차면, 버퍼의 모든 데이터를 한꺼번에 보낸다. 프로그램 입장에서 보면 직접 데이터를 보내는 것이 아니라, 메모리 버퍼로 데이터를 고속 전송하기 때문에 실행 성능이 향상되는 효과를 얻게 된다.

```java
BufferedOutputStream bos = new BufferedOutputStream(바이트출력스트림);
BufferedWriter bw = new BufferedWriter(문자출력스트림);
```

BufferedOutputStream과 BufferedWriter로 데이터를 출력하는 방법은 OutputStream 또는 Writer와 동일하다.

<br>

#### 기본 타입 입출력 보조 스트림

바이트 스트림은 바이트 단위로 입출력하기 때문에 자바의 기본 데이터 타입인 boolean, char, short, int, long, float, double 단위로 입출력할 수 없다. 그러나 DataInputStream과 DataOutputStream 보조 스트림을 연결하면 기본 데이터 타입으로 입출력이 가능하다.

다음은 DataInputStream과 DataOutputStream 객체를 생성하는 코드이다.

```java
DataInputStream dis = new DataInputStream(바이트입력스트림);
DataOutputStream dos = new DataOutputStream(바이트출력스트림);
```

다음은 기본 데이터 타입을 입출력하기 위해 DataInputStream과 DataOutputStream이 제공하는 메소드들을 보여준다.

##### DataInputStream

| 리턴 타입 | 메소드명       |
| --------- | -------------- |
| boolean   | readBoolean( ) |
| byte      | readByte( )    |
| char      | readChar( )    |
| double    | readDouble( )  |
| float     | readFloat( )   |
| int       | readInt( )     |
| long      | readLong( )    |
| short     | readShort( )   |
| String    | readUTF( )     |

##### DataOutputStream

| 리턴 타입 | 메소드명                |
| --------- | ----------------------- |
| void      | writeBoolean(boolean V) |
| void      | writeByte(int v)        |
| void      | writeChar(int v)        |
| void      | writeDouble(double v)   |
| void      | writeFloat(float v)     |
| void      | writeInt(int v)         |
| void      | writeLong(long v)       |
| void      | writeShort(int v)       |
| void      | writeUTF(String str)    |

이 메소드들로 입출력할 때 주의할 점이 있는데, 데이터 타입의 크기가 모두 다르므로 DataOutputStream으로 출력한 데이터를 다시 DataInputStream으로 읽어올 때는 출력한 순서와 동일한 순서로 읽어야 한다.

<br>

#### 프린터 보조 스트림

PrintStream과 PrintWriter는 프린터와 유사하게 출력하는 print( ), println( ) 메소드를 가지고 있는 보조 스트림이다. 매우 빈번히 사용했던 콘솔 출력 스트림인 System.out이 바로 PrintStream 타입이기 때문에 print( ), println( ) 메소드를 사용할 수 있었다. 

PrintStream과 PrintWriter는 다른 보조 스트림과 마찬가지로 연결할 출력 스트림을 생성자의 매개값으로 받는다.

```java
PrintStream ps = new PrintStream(바이트출력스트림);
PrintWirter	pw = new PrintWriter(문자출력스트림);	
```

<br>

#### 객체 입출력 보조 스트림

자바는 메모리에 생성된 객체를 파일 또는 네트워크로 출력할 수가 있다. 객체는 문자가 아니기 때문에 바이트 기반 스트림으로 출력해야 한다. 객체를 출력하기 위해서는 객체의 데이터(필드값)를 일렬로 늘어선 연속적인 바이트로 변경해야 하는데, 이것을 객체 직렬화(serialization)라고 한다. 반대로 파일에 저장되어 있거나 네트워크에서 전송된 객체를 읽을 수도 있는데, 입력 스트림으로부터 읽어들인 연속적인 바이트를 객체로 복원하는 것을 역직렬화(desirialization)라고 한다.

##### ObjectInputStream, ObjectOutputStream

ObjectOutputStream은 바이트 출력 스트림과 연결되어 객체를 직렬화 하는 역할을 하고, ObjectInputStream은 바이트 입력 스트림과 연결되어 객체로 역직렬화하는 역할을 한다.

ObjectInputStream과 ObjectOutputStream은 다른 보조 스트림과 마찬가지로 연결할 바이트 입출력 스트림을 생성자의 매개값으로 받는다.

```java
ObjectInputStream ois = new ObjectInputStream(바이트입력스트림);
ObjectOutputStream oos = new ObjectOutputStream(바이트출력스트림);
```

ObjectOutputStream으로 객체를 직렬화하기 위해서는 writeObject( ) 메소드를 사용한다.

```java
oos.writeObject(객체);
```

반대로 ObjectInputStream의 readObject( ) 메소드는 입력 스트림에서 읽은 바이트를 역직렬화 해서 객체로 생성한다. readObject( ) 메소드의 리턴 타입은 Object 타입이기 때문에 객체 원래의 타입으로 변환해야 한다.

```java
객체타입 변수 = (객체타입) ois.readObject();	
```

##### serialVersionUID 필드

직렬화된 객체를 역직렬화할 때는 직렬화했을 때와 같은 클래스를 사용해야 한다. 클래스의 이름이 같더라도 클래스의 내용이 변경되면, 역직렬화는 실패하며 예외가 발생한다.

serialVersionUID는 같은 클래스임을 알려주는 식별자 역할을 하는데, Serializable 인터페이스를 구현한 클래스를 컴파일하면 자동적으로 seialVersionUID 정적 필드가 추가된다. 문제는 클래스를 재컴파일하면 serialVersionUID의 값이 달라진다는 것이다. 네트워크로 객체를 직렬화하여 전송하는 경우, 보내는 쪽과 받는 쪽이 모두 같은 serialVersionUID를 갖는 클래스를 가지고 있어야 하는데 한 쪽에서 클래스를 변경해서 재컴파일하면 다른 serialVersionUID를 가지게 되므로 역직렬화에 실패하게 된다.