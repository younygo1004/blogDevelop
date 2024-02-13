
### 주의 사항

해당 글에는 잘못된 내용이 있을 수 있으며, 그로 인한 피해에는 어떠한 법적 책임도 지지 않습니다. 따라서 본 글의 내용을 적용할 시, 반드시 검증 후 사용하시기를 권장드립니다.

또한 글의 내용을 인용할 경우,  해당 글의 링크와 저작권자를 표기하시기 바랍니다. 

### 서론

필자가 최근 받은 질문이 있는데, 살을 좀 붙여서 말하자면 이런 질문이었다.

"ArrayList의 기본 할당 크기가 10이라고 하는데, 어떻게 그걸 확인할 수 있나요?"

자주 사용하던 Class임에도 깊게 생각해 본 적은 없던 터라, 궁금하기도 해서 분석해보고자 한다. 즉, 위 질문이 이번 글의 주제다.

미리 말하자면, 이 글은 ArrayList의 사용법을 다루지 않는다. 사용법이 궁금하다면 직접 검색해보길 바란다.

### 개발 환경

OS : Windows 11 Pro (22H2)
JDK : jdk-17.0.10
IDE : IntelliJ IDEA 2023.2.1 (Community Edition)


### List인 척 하는 Object 배열, ArrayList

![[ArrayList_fields.png]]

위의 이미지는 Java의 ArrayList를 캡쳐한 것이다.
마치 Java의 Primitive type을 감싼 Wrapper Class처럼, ArrayList도 Object 배열을 사용하는 것을 확인할 수 있다.

```java
private static final int DEFAULT_CAPACITY = 10;
```
해당 라인부터 살펴보면, 기본 초기화 capacity를 10으로 설정해 놓았음을 알 수 있다.

```java
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
```
해당 구문은 빈 상수(immutable) Object 배열인데, 후에 더 살펴보도록 하겠다.

```java
transient Object[] elementData;
```
elementData가 실제로 우리가 사용할 Object 배열이라고 보면 되겠다.

```java
private int size;
```
항상 사용하던 ArrayList의 size() method가 반환하는 size 변수다.

ArrayList Class의 field 값들을 살펴봤으니, ArrayList의 동작을 한번 살펴보고, 실제로 예제를 작성하여 동작을 보도록 하자.

### ArrayList의 동작 방식

ArrayList의 default constructor를 호출하는 경우, ArrayList는 위 코드의 빈 배열을 참조하도록 되어 있다.

필자가 코드를 분석하면서 가장 혼란스러웠던 것은 다음 코드인데, 내용은 다음과 같다.

![[ArrayList_default_constructor.png]]
분명 기본 생성자 호출 시, elementData는 빈 배열을 참조하도록 되어 있다.
그런데 그 위의 주석을 살펴보면, 초기 크기가 10인 비어있는 list로 구성된다고 적혀 있다.

분명 비어있는 배열을 참조하는데, 초기 크기가 10이라니? 
필자가 영어를 못하는 걸 수도 있지만, 궁금증을 풀고자 예제 코드를 debug mode로 분석해보았다.

초기 질문인 "어떻게 초기 크기가 10인 것을 확인할 수 있는지"에 대해서도 알아볼 겸.

```java
import java.util.ArrayList;  
  
public class ArrayListTest {  
  
    public static void main(String[] args) {  
  
        ArrayList<String> testList = new ArrayList<>();  
  
        System.out.println();  
  
        testList.add("test");  
  
        System.out.println();  
  
    }  
  
}
```

ArrayList를 기본 생성자로 초기화하고, add() method를 호출해 보았다.
add() method를 호출하는 이유는 아래에서 마저 설명하도록 하겠다.

![[sample_code_ArrayList_default_constructor.png]]

2개의 System.out.println(); 문에 breakpoint를 걸고, testList에 할당된 값을 살펴보았다.

11번째 line의 breakpoint 시점에서 ArrayList의 elementData에 할당된 값을 살펴보자.
```java
ArrayList<String> testList = new ArrayList<>();
```
String 객체를 담는 ArrayList를 선언과 동시에 기본 생성자를 통해 초기화 했고, 아래는 그 결과다.

![[Object_array_empty.png]]
보는 것과 같이, 크기가 0인 Object을 참조하고 있는 것을 볼 수 있다.
이는 위에서 살펴본 ArrayList의 기본 생성자 코드가 빈 배열을 참조하는 것과 일치하는 결과이다.

다음으로 add() method를 호출한 후, 다시 결과를 보도록 하겠다.
![[sample_code_ArrayList_add.png]]

위의 이미지처럼, 15번째 line에서 breakpoint를 걸고, elementData를 살펴보았다.
```java
testList.add("test");
```
위 코드가 호출된 직후인데, 이 시점에서 elementData를 확인해 보았다.

![[ArrayList_add_10_capacity.png]]
결과는 위 이미지와 같다.

결론은 ArrayList는 기본 생성자 호출 시에는 내부의 빈 Object 배열을 참조하고, add() method 호출 시점에 기본 capacity만큼의 배열을 할당한다는 것이다.

그러면 10 capacity의 배열로 초기화된다는 것은 무슨 뜻일까?

### ArrayList의 lazy inintialize

stackoverflow에서 답을 찾을 수 있었는데, 다음 링크의 글을 참조했다.

https://stackoverflow.com/questions/34250207/in-java-8-why-is-the-default-capacity-of-arraylist-now-zero

질문의 내용이 필자가 궁금했던 내용과 정확히 일치하는 것을 보아, 영어를 못해서 혼란스러웠던 것은 아닌 듯 하다.

답변을 한번 살펴보도록 하자
![[stackoverflow_answer.png]]lazy initialize, 즉 게으른 초기화를 고려한다면 기본 크기가 10이 맞다는 것이다.

그런데 답변의 아래 부분의 이메일 내용을 살펴보다가 다음 링크를 발견했다.
https://cr.openjdk.org/~mduigou/JDK-7143928/1/webrev/src/share/classes/java/util/ArrayList.java.udiff.html

위 링크에서는 ArrayList의 기본 생성자 구현이 변경된 것을 볼 수 있었는데, 다음과 같다.

![[ArrayList()_changed.png]]
위 코드는 https://cr.openjdk.org/~mduigou/JDK-7143928/1/webrev/ 에서의 변경점인데, 삭제된 코드를 보면 this(10)을 통해 10 크기의 배열을 할당하는 코드가 삭제되었다.

그렇다면 왜 바로 10 크기의 배열을 할당하지 않고, add() method 호출 시점에서 배열을 할당하는 것일까?

![[answer_Lazy_allocatoion.png]]

위의 내용에서 보는 것처럼, 85%의 ArrayList가 default size로 생성되므로, 대부분의 경우 성능 향상을 기대할 수 있다는 것이다.


### 정리

결과적으로, ArrayList는 많은 사람들이 아는 것과 같이 10 크기의 기본 할당 크기를 가진다.
그러나 기본 크기를 지정하지 않는 경우, ArrayList는 Lazy allocation으로 인해 초기에는 빈 배열을 참조한다.
