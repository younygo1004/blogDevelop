
# 들어가기에 앞서...

해당 글의 내용을 인용 혹은 공개할 시, 반드시 해당 글의 링크와 저작권자를 표기하셔야 합니다.
또한 글에 오탈자 및 잘못된 내용이 있을 수 있으며, 그 경우에는 댓글 남겨주시면 확인 후 수정하도록 하겠습니다.


# 개발 환경

IDE : IntelliJ IDEA 2023.2.1 (Community Edition)
JDK : 17.0.10

# for Loop의 문법

```java
for (초기식; 조건식; 실행문) {  
      // 루프를 진행하는 동안 실행할 내용 입력
}
```

위 코드는 java의 전통적인 for 문의 문법이다. 

1. 초기식
	- 반복문 실행 시, 최초 1회 실행할 구문을 입력한다. 보통은 `int i = 0`과 같이 조건문 내에서 사용할 변수를 선언한다.
2. 조건식
	- 해당 조건문을 실행하기 위한 조건을 입력한다. 내용을 입력하지 않을 시, 무한 루프를 실행한다. 단, 조건식을 입력하는 경우, 반드시 `boolean`값을 `return`해야 한다.
3. 실행문
	- 조건문을 타는 동안, 매번 실행할 구문을 입력한다. 보통 조건식과 연관된 실행문을 입력한다.

# for Loop의 실행 순서

다음과 같은 for 문이 있다.

```java
public class ForLoopExample {  
  
    public static void main(String[] args) {  
  
        int test = 1;  
        for (int i = 0; i < 5; i++) {  
            System.out.printf("i값 = %d, 내부 출력문 실행%n", i);  
        }  
  
    }  
  
      
}
```

해당 for 문은 먼저 `int i = 0;` 구문을 실행하여 for 문 내부 블록에 `int`형 변수 `i`를 0으로 초기화하여 할당한다.

다음으로, 중간의 `i < 5;`의 조건식이 `true`인지 검사한 후, `true`값이면 내부 블록의 `System.out.println("for문 내부 출력문");`를 실행한다.
그 후 `i++`구문을 실행하고, `i < 5` 조건식을 다시 검사한 후, 내부 출력문을 실행 여부를 판단하는 것을 반복한다.

`i < 5` 조건문이 `false`가 되는 경우, 즉시 for 문을 종료한다.

```java
// 실행 결과

/*
i값 = 0, 내부 출력문 실행
i값 = 1, 내부 출력문 실행
i값 = 2, 내부 출력문 실행
i값 = 3, 내부 출력문 실행
i값 = 4, 내부 출력문 실행
*/
```

그런데 위의 코드에서는 내부 실행 순서만 출력으로 볼 수 있기 때문에, 조금 더 쉽게 볼 수 있도록 코드를 약간 바꿔서 실행해 보려고 한다.

# method를 이용한 for Loop의 실행 순서 시각화

이번에는 `ForLoopExample` 클래스 내부에 `static method`를 선언하고, 이를 이용하여 for 문의 실행 순서를 좀 더 직접적으로 확인해 보려고 한다.

전체 소스 코드는 다음과 같다.
전반적으로 이름을 대충 지었으니 감안하고 보도록 하자.
```java
public class ForLoopExample2 {  
  
    public static void main(String[] args) {  
  
        int test = 1;  
        for (int i = initStatementForLoop(); conditionalStatement(i, 5); i = lastStatement(i)) {  
            System.out.printf("i값 = %d, 내부 출력문 실행%n", i);  
            System.out.println();  
        }  
  
    }  
  
    /**  
     * for Loop의 초기식 실행 여부를 출력하기 위한 껍데기 method  
     * @return 0  
     */    public static int initStatementForLoop() {  
        System.out.println("초기식 실행");  
        return 0;  
    }  
  
    /**  
     * for Loop의 두 번째 구문인 조건식 실행 여부를 출력하기 위한 껍데기 method  
     * @param first 조건식의 초기식에서 선언한 변수를 전달  
     * @param second 조건식을 반복할 횟수를 전달  
     * @return 조건식을 만족하는지 여부에 대해 true, false값을 반환  
     */  
    public static boolean conditionalStatement(int first, int second) {  
        System.out.println("조건식 실행");  
        return first < second;  
    }  
  
    /**  
     * for Loop의 세 번째 구문인 실행문 실행 여부를 출력하기 위한 껍데기 method  
     * @param first 조건식의 초기식에서 선언한 변수를 전달  
     * @return first+1  
     */    public static int lastStatement(int first) {  
        System.out.println("세번째 실행문 실행");  
        return first + 1;  
    }  
  
}
```


먼저 for 문을 살펴보면 다음과 같다.
```java
for (int i = initStatementForLoop(); conditionalStatement(i, 5); i = lastStatement(i)) {  
    System.out.printf("i값 = %d, 내부 출력문 실행%n", i);  
    System.out.println();  
}
```
초기식에는 `initStatementForLoop()` method를, 조건식에는 `conditionalStatement(first, second)` method를, 마지막 실행문에는 `lastStatement(first)` method를 사용하였다.

다음으로 for 문에서 사용한 3가지 method를 살펴보도록 하겠다.

```java
public static int initStatementForLoop() {  
    System.out.println("초기식 실행");  
    return 0;  
}
```
method의 로직은 없다고 봐도 무방하고, 초기식이 실행되는 시점에 `"초기식 실행"`이라는 문구가 출력되도록 하였다. `return 0;`구문을 통해 조건식에서 사용한 `i`변수를 `0`으로 초기화했다.

```java
public static boolean conditionalStatement(int first, int second) {  
    System.out.println("조건식 실행");  
    return first < second;  
}
```
두 번째 method 또한 조건식을 검사하는 시점에 `"조건식 실행"`이라는 문구를 출력하도록 하였고, 조건식은 실행 결과가 `boolean`값이어야 하기 때문에 `return first < second;`를 통해 `true` 혹은 `false`를 반환하도록 작성했다.

```java
public static int lastStatement(int first) {  
    System.out.println("세번째 실행문 실행");  
    return first + 1;  
}
```
세 번째 method 또한 `"세번째 실행문 실행"`문구를 출력하고, `int first` parameter를 통해 argument로 받은 값에 1을 더해 반환하도록 하였다.

마지막으로 이제 전체 소스 코드 실행 결과를 보자.
```java
//실행 결과

/*
초기식 실행
조건식 실행
i값 = 0, 내부 출력문 실행

세번째 실행문 실행
조건식 실행
i값 = 1, 내부 출력문 실행

세번째 실행문 실행
조건식 실행
i값 = 2, 내부 출력문 실행

세번째 실행문 실행
조건식 실행
i값 = 3, 내부 출력문 실행

세번째 실행문 실행
조건식 실행
i값 = 4, 내부 출력문 실행

세번째 실행문 실행
조건식 실행
*/

```

위의 실행 결과를 통해 초기식은 최초 1회만 실행된 것을 확인할 수 있다.
그 이후는 내부 출력문 > i값을 증가시키는 3번째 실행문 > 조건식 검사를 진행한 후, 조건식이 false가 되는 즉시 for loop가 종료되는 것을 확인할 수 있다.