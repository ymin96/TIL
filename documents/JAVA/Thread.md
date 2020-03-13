# Multi Thread

> 자바에서 쓰레드 사용 방법에 대한 전체적인 방법을 알아본다.

<br>

멀티 쓰레드에 대한 개념은 타 언어와 다른점이 없으니 생략하고 바로 사용방법에 대해 설명하겠다.

### 작업 스레드 생성과 실행

자바에서는 작업 스레드도 객체로 생성되기 때문에 클래스가 필요하다. java.lang.Thread 클래스를 직접 객체화해서 생성해도 되지만, Thread를 상속해서 하위 클래스를 만들어 생성할 수도 있다.

<br>

#### Thread 클래스로부터 직접 생성

java.lang.Thread 클래스로부터 작업 스레드 객체를 직접 생성하려면 다음과 같이 Runnable을 매개값으로 갖는 생성자를 호출해야 한다.

```java
Thread thread = new Thread(Runnable target);
```

Runnable은 인터페이스 타입이기 때문에 구현 객체를 만들어 대입해야 한다. Runnable에는 run() 메소드 하나가 정의되어 있는데, 구현 클래스는 run()을 재정의해서 작업 스레드가 실행할 코드를 작성해야 한다.

```java
class Task implements Runnable{
	public void run(){
		// 스레드가 실행할 코드
	}
}
```

Runnable 구현 객체를 생성한 후, 이것을 매개값으로 해서 Thread 생성자를 호출하면 비로소 작업 스레드가 생성된다.

```java
Runnable task = new Task();
Thread thread = new Thread(task);
```

Thread 생성자를 호출할 때 Runnable 익명 객체를 매개값으로 사용할 수 있다. 오히려 이 방법이 더 많이 사용된다.

```java
Thread thread = new Thread(new Runnable(){
	public void run(){
		// 스레드가 실행할 코드
	}
});
```

Runnable 인터페이스는 run() 메소드 하나만 정의되어 있기 때문에 함수적 인터페이스이다. 따라서 다음과 같이 람다식을 매개값으로 사용할 수도 있다.

```java
Thread thread = new Thread(()->{
	// 스레드가 실행할 코드
});
```

작업 스레드는 생성되는 즉시 실행되는 것이 아니라. start() 메소드를 다음과 같이 호출해야만 비로소 실행된다.

```java
thread.start();
```

<br>

#### Thread 하위 클래스로부터 생성

작업 스레드가 실행할 작업을 Runnable로 만들지 않고, Thread의 하위 클래스로 작업 스레드를 정의하면서 작업 내용을 포함시킬 수도 있다. Thread 클래스를 상속한 후 run 메소드를 재정의(overriding)해서 스레드가 실행할 코드를 작성하면 된다.

```java
public class WorkerThread extends Thread{
	@Override
	public void run(){
		// 스레드가 실행할 코드
	}
}
Thread thread = new WorkerThread
```

코드를 절약하기 위해 다음과 같이 Thread 익명 개체로 작업 스데르 객체를 생성할 수도 있다.

```java
Thread thread = new Thread(){
	public void run(){
		//스레드가 실행할 코드
	}
};
```

이렇게 생성된 작업 스레드 객체에서 start() 메소드를 호출하면 작업 스레드는 자신의 run() 메소드를 실행하게 된다.

```java
thread.run()
```

<br>

#### 스레드의 이름

스레드는 자신의 이름을 가지고 있다. 메인 스레드는 "main"이라는 이름을 가지고 있고, 직접 생성한 스레드는 자동적으로 "Thread-n"이라는 이름으로 설정된다. Thread 클래스의 setName() 메소드를 이름을 변경할 수 있다.

```java
thread.setName("스레드 이름");
```

반대로 스레드 이름을 알고 싶을 경우에는 getName() 메소드를 호출하면 된다.

```java
thread.getName()
```

만약 스레드 객체의 참조를 가지고 있지 않다면, Thread의 정적 메소드인 currentThread()로 코드를 실행하는 현재 스레드의 참조를 얻을 수 있다.

```java
Thread thread = Thread.currentThread();
```

<br>

<br>

### 스레드 우선순위

멀티 스레드는 동시성(Concurrency) 또는 병렬성(Parallelism) 으로 실행된다. 동시성은 멀티 작업을 위해 하나의 코어에서 멀티 스레드가 번갈아가며 실행하는 성질을 말하고, 병렬성은 멀티 작업을 위해 멀티 코어에서 개별 스레드를 동시에 실행하는 성질을 말한다.

![1](../img/JAVA/Thread/1.PNG)