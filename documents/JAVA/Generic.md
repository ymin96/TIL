# Generic

> 제네릭의 문법을 알아본다.

<br>

### 제네릭을 사용하는 이유

제네릭은 클래스와 인터페이스, 그리고 메소드를 정의할 때 타입(type)을 파라미터(parameter)로 사용할 수 있도록 한다. 타입 파라미터는 코드 작성 시 구체적인 타입으로 대체되어 다양한 코드를 생성하도록 해준다. 제네릭을 사용하는 코드는 비제네릭 코드에 비해 다음과 같은 이점을 가지고 있다.

#### 1. 컴파일 시 강한 타입 체크를 할 수 있다.

자바 컴파일러는 코드에서 잘못 사용된 타입 때문에 발생하는 문제점을 제거하기 위해 제네릭 코드에 대해 강한 타입 체크를 한다.

#### 2. 타입 변환(casting)을 제거한다.

제네릭 코드는 타입 변환을 할 필요가 없어 프로그램 성능이 향상된다.

<br>

<br>

### 제네릭 타입(class&#60;T>, interface&#60;T>)

제네릭 타입은 타입을 파라미터로 가지는 클래스와 인터페이스를 말한다. 제네릭 타입은 클래스 또는 인터페이스 이름 뒤에 `<>` 부호가 붙고, 사이에 타입 파라미터가 위치한다. 아래 코드에서 타입 파라미터의 이름은 T이다.

```java
public class 클래스명<T>{...}
public interface 인터페이스명<T> {...}
```

타입 파라미터는 변수명과 동일한 규칙에 따라 작성할 수 있지만, 일반적으로 대문자 알파벳 한 글자로 표현한다. 제네릭 타입을 실제 코드에서 사용하려면 타입 파라미터에 구체적인 타입을 지정해야 한다.

<br>

<br>

### 멀티 타입 파라미터(class&#60;K,V,....>, interface&#60;K,V,...>)

제네릭 타입은 두 개 이상의 멀티 타입 파라미터를 사용할 수 있는데, 이 경우 각 타입 파라미터를 콤마로 구분한다. 

```java
public class 클래스명<K,V,...>{...}
public interface 인터페이스명<K,V,...> {...}
```

<br>

<br>

### 제네릭 메소드(&#60;T,R> R method(T t))

제네릭 메소드는 매개 타입과 리턴 타입으로 파라미터를 갖는 메소드를 말한다. 제네릭 메소드를 선언하는 방법은 리턴 타입 안에 `<>` 기호를 추가하고 타입 파라미터를 기술한 다음, 리턴 타입과 매개 타입으로 타입 파라미터를 사용하면 된다.

```java
public <타입파라미터,...> 리턴타입 메소드명(매개변수,...) {...}
```

제네릭 메소드는 두 가지 방법으로 호출할 수 있다. 코드에서 타입 파라미터의 구체적인 타입을 명시적으로 지정해도 되고, 컴파일러가 매개값의 타입을 보고 구체적인 타임을 추정하도록 할 수도 있다.

```java
리턴타입 변수 = <구체적타입> 메소드명(매개값);	//명시적으로 구체적 타입을 지정
리턴타입 변수 = 메소드명(매개값);	//매개값을 보고 구체적 타입을 추정
```

<br>

<br>

### 제한된 타입 파라미터(&#60;T extends 최상위타입>)

타입 파라미터에 지정되는 구체적인 타입을 제한할 때 사용된다. 제한된 타입 파라미터를 선언하려면 타입 파라미터 뒤에 extends 키워드를 붙이고 상위 타입을 명시하면 된다. 상위 타입은 클래스뿐만 아니라 인터페이스도 가능하다. 인터페이스라고 해서 implements를 사용하지는 않는다.

```java
public <T extends 상위타입> 리턴타입 메소드(매개변수, ...) {...}
```

타입 파라미터에 지정되는 구체적인 타입은 상위 타입이거나 상위 타입의 하위 또는 구현 클래스만 가능하다. 주의할 점은 메소드의 중괄호 {} 안에서 타입 파라미터 변수로 사용 가능한 것은 상위 타입의 멤버(필드, 메소드)로 제한된다. 

<br>

<br>

### 와일드카드 타입(&#60;?>, &#60;? extends ...>, &#60;? super ...>)

코드에서 ?를 일반적으로 와일드카드(wildcard)라고 부른다. 제네릭 타입을 매개값이나 리턴 타입으로 사용할 때 구체적인 타입 대신에 와일드카드를 다음과 같이 세 가지 형태로 사용할 수 있다.

* **제네릭타입&#60;?> -** Unbounded Wildcards(제한 없음)
  타입 파라미터를 대치하는 구체적인 타입으로 모든 클래스나 인터페이스 타입이 올 수 있다.
* **제네릭타입&#60;? extends 상위타입> -** Upper Bounded Wildcards(상위 클래스 제한)
  타입 파라미터를 대치하는 구체적인 타입으로 상위 타입이나 하위 타입만 올 수 있다.
* **제네릭타임&#60;? super 하위타입> -** Lower Bounded Wildcards(하위 클래스 제한)
  타입 파라미터를 대치하는 구체적인 타입으로 하위 타입이나 상위 타입이 올 수 있다.

<br>

<br>

### 제네릭 타입의 상속과 구현

제네릭 타입도 다른 타입과 마찬가지로 부모 클래스가 될 수 있다. 다음은 Product&#60;T, M> 제네릭 타입을 상속해서 ChildProduct&#60;T, M> 타입을 정의한다.

```java
public class ChildProduct<T, M> extends Product<T, M> {...}
```

자식 제네릭 타입은 추가적으로 타입 파라미터를 가질 수 있다. 다음은 세 가지 타입 파라미터를 가진 자식 제네릭 타입을 선언한 것이다.

```java
public class ChildProduct<T, M, C> extends Product<T, M> {...}
```

