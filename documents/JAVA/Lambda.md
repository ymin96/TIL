# Lambda 

> 자바에서 람다의 사용방법에 대해 알아본다.

<br>

### 람다식이란?

람다식은 익명 함수(anonymous function)를 생성하기 위한 식으로 객체 지향 언어보다는 함수 지향 언어에 가깝다. 자바에서 람다식을 수용한 이유는 자바 코드가 매우 간결해지고, 컬렉션의 요소를 필터링하거나 매핑해서 원하는 결과를 쉽게 집계할 수 있기 때문이다. 람다식의 형태는 매개 변수를 가진 코드 블록이지만, 런타임 시에는 익명 구현 객체를 생성한다.

```
람다식 -> 매개 변수를 가진 코드 블록 -> 익명 구현 객체
```

<br>

<br>

### 람다식 기본 문법

함수적 스타일의 람다식을 작성하는 방법은 다음과 같다.

```java
(타입 매개변수, ...) -> {실행문; ...}
```

예를 하나 들어 int 매개 변수 a의 값을 콘솔에 출력하기 위해 다음과 같이 람다식을 작성할 수 있다.

```java
(int a) -> {System.out.println(a);}
```

배개 변수 타입은 런타임 시에 대입되는 값에 따라 자동으로 인식될 수 있기 때문에 람다식에서는 배개 변수의 타입을 일반적으로 언급하지 않는다. 그래서 위 코드는 다음과 같이 작성할 수 있다.

```java
(a) -> {System.out.println(a);}
```

하나의 매개 변수만 있다면 괄호( )를 생략할 수 있고, 하나의 실행문만 있다면 중괄호 { }도 생략 할 수 있다. 그래서 위 코드는 다음과 같이 작성할 수도 있다.

```java
a -> System.out.println(a)
```

만약 매개 변수가 없다면 람다식에서 매개 변수 자리가 없어지므로 다음과 같이 빈 괄호 ( )를 반드시 사용해야 한다.

```java
( ) -> {실행문; ....}
```

중괄호 { }를 실행하고 결과값을 리턴해야 한다면 다음과 같이 return 문으로 결과값을 지정할 수 있다.

```java
(x, y) -> { return x + y; };
```

중괄호 { }에 return문만 있을 경우, 람다식에서는 return 문을 사용하지 않고 다음과 같이 작성하는 것이 정석이다.

```java
(x, y) -> x + y
```

<br>

<br>

### 타겟 타입과 함수적 인터페이스

 ```
인터페이스 변수 = 람다식;
 ```

람다식은 인터페이스 변수에 대입된다. 이 말은 람다식은 인터페이스의 익명 구현 객체를 생성한다는 뜻이 된다. 인터페이스는 직접 객체화할 수 없기 때문에 구현 클래스가 필요한데, 람다식은 익명 구현 클래스를 생성하고 객체화한다. 람다식은 대입될 인터페이스의 종류에 따라 작성 방법이 달라지기 때문에 람다식이 대입될 인터페이스를 람다식의 타겟 타임(target type)이라고 한다.

<br>

#### 함수적 인터페이스(@FunctionalInterface)

하나의 추상 메소드가 선언된 인터페이스만이 람다식의 타겟 타입이 될 수 있는데, 이러한 인터페이스를 함수적 인터페이스(funtional interface)라고 한다. 함수적 인터페이스를 작성할 때 두 개 이상의 추상 메소드가 선언되지 않도록 컴파일러가 체킹해주는 기능이 있는데, 인터페이스 선언 시 @FuntionalInterface 어노테이션을 붙이면 된다. 이 어노테이션은 두 개 이상의 추상 메소드가 선언되면 컴파일 오류를 발생시킨다.

```java
@FuntionalInterface
public interface MyFuntionlInterface{
	public void method();
	public void othreMethod();	//컴파일 오류
}
```

@FuntionalInterface 어노테이션은 선택사항이다.

<br>

<br>

### 클래스 멤버와 로컬 변수 사용

람다식의 실행 블록에는 클래스의 멤버 (필드와 메소드) 및 로컬 변수를 사용할 수 있다. 클래스의 멤버는 제약 사항 없이 사용 가능하지만, 로컬 변수는 제약 사항이 따른다.

<br>

#### 클래스의 멤버 사용

람다식 실행 블록에는 클래스의 멤버인 필드와 메소드를 제약 사항 없이 사용할 수 있다. 하지만 this 키워드 사용엔 주의가 따른다. 일반적으로 익명 객체 내부에서 this는 익명 객체의 참조이지만, 람다식에서 this 는 람다식을 실행한 객체의 참조이다. 

<br>

#### 로컬 변수 사용

람다식에서 메소드의 매개 변수 또는 로컬 변수를 사용하면 이 두 변수는 final 특성을 가져야 한다. 따라서 매개 변수 또는 로컬 변수를 람다식에서 읽는 것은 허용되지만, 람다식 내부 또는 외부에서 변경할 수 없다. 

<br>

<br>

### 표준 API의 함수적 인터페이스

자바 8부터는 빈번하게 사용되는 함수적 인터페이스(functional interface) 는 java.util.function 표준 API 패키지로 제공한다. 이 패키지에서 제공하는 함수적 인터페이스의 목적은 메소드 또는 생성자의 매개 타입으로 사용되어 람다식을 대입할 수 있도록 하기 위해서이다. 함수적 인터페이스는 크게 Consumer, Supplier, Function, Operator, Predicate 로 구분된다. 구분 기준은 인터페이스에 선언된 추상 메소드의 매개값과 리턴값의 유무이다.

| 종류      | 추상 메소드 타입                                             |
| --------- | ------------------------------------------------------------ |
| Consumer  | - 매개값은 있고, 리턴값은 없음                               |
| Supplier  | - 매개값은 없고, 리턴값은 있음                               |
| Functoin  | - 매개값도 있고, 리턴값도 있음<br />- 주로 매개값을 리턴값으로 매핑(타입 변환) |
| Operator  | - 매개값도 있고, 리턴값도 있음<br />- 주로 매개값을 연산하고 결과를 리턴 |
| Predicate | - 매개값은 있고, 리턴 타입은 boolean<br />- 매개값을 조사해서 true/false 를 리턴 |

<br>

#### Comsumer 함수적 인터페이스

Consumer 함수적 인터페이스의 특징은 리턴값이 없는 accept( ) 메소드를 가지고 있다. accept( ) 메소드는 단지 매개값을 소비하는 역할만 한다. 여기서 소비한다는 말은 사용만 할 뿐 리턴값이 없다는 뜻이다.

매개 변수의 타입과 수에 따라서 아래와 같은 Consumer 들이 있다.

| 인터페이스명             | 추상 메소드                    | 설명                           |
| ------------------------ | ------------------------------ | ------------------------------ |
| Consumer&#60;T>          | void accept(T t)               | 객체 T를 받아 소비             |
| BiConsumer&#60;T,U>      | void accept(T t, U u)          | 객체 T와 U를 받아 소비         |
| DoubleConsumer           | void accept(double value)      | double 값을 받아 소비          |
| IntConsumer              | void accept(int value)         | int 값을 받아 소비             |
| LongConsumer             | void accept(long value)        | long 값을 받아 소비            |
| ObjDoubleConsumer&#60;T> | void accept(T t, double value) | 객체 T와 double 값을 받아 소비 |
| ObjIntConsumer&#60;T>    | void accept(T t, int value)    | 객체 T와 int 값을 받아 소비    |
| ObjLongConsumer&#60;T>   | void accept(T t, long value)   | 객체 T와 long 값을 받아 소비   |

Consumer&#60;T> 인터페이스를 타겟 타입으로 하는 람다식은 다음과 같이 작성할 수 있다.

```java
Consumer<String> consumer = t -> {t를 소비하는 실행문; };
```

<br>

#### Supplier 함수적 인터페이스

Supplier 함수적 인터페이스의 특징은 매개 변수가 없고 리턴값이 있는 getXXX{} 메소드를 가지고 있다. 이 메소드드들은 실행 후 호출한 곳으로 데이터를 리턴(공급) 하는 역할을 한다.

리턴 타입에 따라서 아래와 같은 Supplier 함수적 인터페이스들이 있다.

| 인터페이스명    | 추상 메소드             | 설명              |
| --------------- | ----------------------- | ----------------- |
| Supplier&#60;T> | T get( )                | T 객체를 리턴     |
| BooleanSupplier | boolean getAsBoolean( ) | boolean 값을 리턴 |
| DoubleSupplier  | double getAsDouble( )   | double 값을 리턴  |
| IntSupplier     | int getAsInt( )         | int 값을 리턴     |
| LongSupplier    | long getAsLong( )       | long 값을 리턴    |

Supplier&#60;T> 인터페이스를 타겟 타입으로 하는 람다식은 다음과 같이 작성할 수 있다.

```java
Supplier<String> supplier = () -> {...; return "문자열"; };
```

<br>

#### Function 함수적 인터페이스

Function 함수적 인터페이스의 특징은 매개값과 리턴값이 있는 applyXXX( ) 메소드를 가지고 있다. 이 메소드들은 매개값을 리턴값으로 매핑(타입 변환)하는 역할을 한다.

매개 변수 타입과 리턴 타입에 따라 아래와 같은 Function 함수적 인터페이스들이 있다. 

| 인터페이스명                | 추상 메소드                      | 설명                       |
| --------------------------- | -------------------------------- | -------------------------- |
| Function&#60;T,R>           | R apply(T t)                     | 객체 T를 객체 R로 매핑     |
| BiFunction&#60;T,U,R>       | R apply(T t,U u)                 | 객체 T와 U를 객체 R로 매핑 |
| DoubleFunction&#60;R>       | R apply(double value)            | double을 객체 R로 매핑     |
| IntFunction&#60;R>          | R apply(int value)               | int를 객체 R로 매핑        |
| IntToDoubleFunction         | double applyAsDouble(int value)  | int를 double로 매핑        |
| IntToLongFunction           | long applyAsLong(int value)      | int를 long으로 매핑        |
| LongToDoubleFunction        | double applyAsDouble(long value) | long을 double로 매핑       |
| LongToIntFunction           | int applyAsInt(long value)       | long을 int로 매핑          |
| ToDoubleBiFunction&#60;T,U> | double applyAsDouble(T t,U u)    | 객체 T와 U를 double로 매핑 |
| ToDoubleFunction&#60;T>     | double applyAsDoule(T t)         | 객체 T를 double로 매핑     |
| ToIntBiFunction&#60;T,U>    | int applyAsInt(T t,U u)          | 객체 T와 U를 int로 매핑    |
| ToIntFunction&#60;T>        | int applyAsInt(T t)              | 객체 T를 int로 매핑        |
| ToLongBiFunction&#60;T,U>   | long applyAsLong(T t,U u)        | 객체 T와 U를 long으로 매핑 |
| ToLongFunction&#60;T>       | long applyAsLong(T t)            | 객체 T를 long으로 매핑     |

Function&#60;T,R> 인터페이스를 타겟 타입으로 하는 람다식은 다음과 같이 작성할 수 있다.

```java
Function<Student, String> function = t -> { return t.getName(); };
또는
Function<Student, String> function = t -> t.getName(); 
```

<br>

#### Operator 함수적 인터페이스

Operator 함수적 인터페이스는 Function과 동일하게 매개 변수와 리턴값이 있는 applyXXX() 메소드를 가지고 있다. 하지만 이 메소드들은 매개값을 리턴값으로 매핑 ( 타입 변환 ) 하는 역할보다는 매개값을 이용해서 연산을 수행한 후 동일한 타입으로 리턴값을 제공하는 역할을 한다.

매개 변수의 타입과 수에 따라서 아래와 같은 Operator 함수적 인터페이스가 있다.

| 인터페이스명          | 추상 메소드                              | 설명                    |
| --------------------- | ---------------------------------------- | ----------------------- |
| BinaryOperator&#60;T> | BiFunction&#60;T,U,R> 의 하위 인터페이스 | T와 U를 연산한 후 R리턴 |
| UnaryOperator&#60;T>  | Function&#60;T,R> 의 하위 인터페이스     | T를 연산한 후 R 리턴    |
| DoubleBinaryOperator  | double applyAsDouble(double,double)      | 두 개의 double 연산     |
| DoubleUnaryOperator   | double applyAsDouble(doubl)              | 한 개의 double 연산     |
| IntBinaryOperator     | int applyAsInt(int, int)                 | 두 개의 int 연산        |
| IntUnaryOperator      | int applyAsInt(int)                      | 한 개의 int 연산        |
| LongBinaryOperator    | long applyAslong(long, long)             | 두 개의 long 연산       |
| LongUnaryOperator     | long applyAsLong(long)                   | 한 개의 long 연산       |

IntBinaryOperator 인터페이스를 타겟 타입으로 하는 람다식은 다음과 같이 작성할 수 있다.

```java
IntBinaryOperator operator = (a,b) -> { ...; return int값; }
```

<br>

#### Predicate 함수적 인터페이스

Predicate 함수적 인터페이스는 매개 변수와 boolean 리턴값이 있는 testXXX( ) 메소드를 가지고 있다. 이 메소드들은 매개값을 조사해서 true 또는 false를 리턴하는 역할을 한다.

매개 변수 타입과 수에 따라서 아래와 같은 Predicate 함수적 인터페이스들이 있다.

| 인터페이스명         | 추상 메소드                | 설명                   |
| -------------------- | -------------------------- | ---------------------- |
| Predicate&#60;T>     | boolean test(T t)          | 객체 T를 조사          |
| DoublePredicate      | boolean test(double value) | double 값을 조사       |
| IntPredicate         | boolean test(int value)    | int 값을 조사          |
| LongPredicate        | boolean test(long value)   | long 값을 조사         |
| BiPredicate&#60;T,U> | boolean test(T t, U u)     | 객체 T와 U를 비교 조사 |

Predicate&#60;T> 인터페이스를 타겟 타입으로 하는 람다식은 다음과 같이 작성할 수 있다.

```java
Predicate<Student> predicate = t -> { return t.getSex().equals("남자");};
또는
Predicate<Student> predicate = t -> t.getSex().equals("남자");
```

<br>

<br>

### 메소드 참조

메소드 참조(Method References)는 말 그대로 메소드를 참조해서 매개 변수의 정보 및 리턴 타입을 알아내어, 람다식에서 불필요한 매개 변수를 제거하는 것이 목적이다.

<br>

#### 정적 메소드와 인스턴스 메소드 참조

정적(static) 메소드를 참조할 경우에는 클래스 이름 뒤에 :: 기호를 붙이고 정적 메소드 이름을 기술하면 된다.

```java
클래스 :: 메소드	
```

인스턴스 메소드일 경우에는 먼저 객체를 생성한 다음 참조 변수 뒤에 :: 기호를 붙이고 인스턴스 메소드 이름을 기술하면 된다.

```java
참조변수 :: 메소드
```

<br>

#### 매개 변수의 메소드 참조

메소드는 람다식 외부의 클래스 멤버일 수도 있고, 람다식에서 제공되는 매개 변수의 멤버일 수도 있다. 다음과 같이 람다식에서 제공되는 a 매개 변수의 메소드를 호출해서 b 매개 변수를 매개값으로 사용하는 경우도 있다.

```java
(a,b) -> {a.instanceMethod(b);}
```

이것을 메소드 참조로 표현하면 다음과 같다. a의 클래스 이름 뒤에 :: 기호를 붙이고 메소드 이름을 기술하면 된다. 작성 방법은 정적 메소드 참조와 동일하지만, a의 인스턴스 메소드가 참조되므로 전혀 다른 코드가 된다.

```java
클래스 :: instanceMethod
```

<br>

#### 생성자 참조

메소드 참조(method references) 는 생성자 참조도 포함한다. 생성자를 참조한다는 것은 객체 생성을 의미한다. 단순히 메소드 호출로 구성된 람다식을 메소드 참조로 대치할 수 있듯이, 단순히 객체를 생성하고 리턴하도록 구성된 람다식은 생성자 참조로 대치할 수 있다. 다음 코드를 보면 람다식은 단순히 객체 생성 후 리턴만 한다.

```java
(a,b) -> { return new 클래스(a,b); }
```

이 경우, 생성자 참조로 표현하면 다음과 같다. 클래스 이름 뒤에 :: 기호를 붙이고 new 연산자를 기술하면 된다. 생성자가 오버로딩되어 여러 개가 있을 경우, 컴파일러는 인터페이스의 추상 메소드와 동일한 매개 변수 타입과 개수를 가지고 있는 생성자를 찾아 실행한다.

```java
클래스 :: new
```

