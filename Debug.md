## Main Concept
보통 버그라고 하면 한마디로 에러 즉 프로그램이 오작동하는 것을 의미한다.<br>

그렇다면 디버깅이란, 무엇일까?<br>
디버깅이란 오작동되는 현상(오류)을 해결하기 위해서, **버그를 찾아내고 해결하는 과정**을 의미한다.<br>
보통 여러가지 방식으로 디버깅을 진행하지만 **디버그의 90%는 값을 찍어보는것이다.**<br>
<br>
<br>

## VS Debuger 단축키
* **중단점** 삽입/삭제: F9

* 디버깅 시작: F5

* 디버깅 중지: Shift+F5

* 프로시저 단위 실행: F10
-> **한 줄씩 실행**하는 것으로 메소드 호출문 또한 한 줄로 인식해 호출된 메소드가 단박에 실행이 완료된다.<br>

* 한 단계씩 코드 실행: F11
-> 이 역시 한 줄씩 실행하는데, 메소드 호출 시 해당 **메소드 내로 들어간 후 한 줄씩 실행한다.**<br>

디버거 **조사식에 원하는 변수를 입력하거나 드래그로 끌어넣으면 해당 변수의 값을 추적**할 수 있다.<br>
<br>
<br>

## Unity Debuger
VS에서 제공하는 디버거를 Unity에서도 사용할 수 있다.<br>
-> **런타임 중 여러 값들을 확인할 수 있기에 디버깅에 굉장히 유용하다.**<br>

다음과 같은 절차를 통해 사용할 수 있다.<br>
1. F9 버튼을 통해 중단점을 설정한다.
2. **Unity에 연결 및 재생** 버튼을 클릭한다.
3. F10으로 한줄 씩 실행하며 디버깅한다.
<br>
<br>

## Debug.Log() 효과적으로 관리하는 방법
Debug.Log("문자열");<br>
이 메소드는 디버깅에서 필수이기 때문에 정말 자주 사용하는 메소드다.<br>
하지만 개발이 완료되고 출시에 이르렀을 때에는, 이 로그 코드가 문제가 된다.<br>

1) 퍼포먼스에 영향을 주고,<br>
2) 보안에도 문제가 생기기 때문이다.<br>
<br>

그래서 출시버전에서는 이 메소드가 작동하지 않도록 여러가지 방법을 썼었다.<br>
**대표적으로 Debug 클래스를 재정의해서 내부 메소드가 아무 동작도 하지 않도록 하는 방법** 이 있었다.<br>
이 방법도 그리 좋은 방법은 아니다.<br>

바로 **문자열 퍼포먼스 때문이다.**<br>
```c#
Debug.Log("My Level : " + char.level +  " , " + char.grade);
```
<br>

위와 같이 많이들 출력 할 것이다.<br>
'+' 연산자를 이용한 무분별한 문자열 결합은, 가비지 컬렉터가 자주 실행되게 함으로써 퍼포먼스에 좋지 않은 영향을 준다.<br>  
그래서 꼭 필요한 곳에 문자열 결합을 사용 해야 할 때에는 string 보다는 string builder 를 사용한다.<br>
하지만 쓰기가 불편하다는 단점 때문에, Log 출력에서 까지 쓰는 사람은 거의 없을거다..<br>
**Debug.Log() 가 아무 역할을 하지 않게 바꿔준다고는 해도, 메소드의 호출 자체를 막는건 아니기 때문에<br>
소스코드 여기저기 무분별하게 사용된 문자열 결합코드는 정말 난감하다.**<br>
<br>
<br>

'처음부터 이 메소드가 없었던 것 같은 상태'로 만들 수 있는 방법이 있다.<br>
**System.Diagnostics.Conditional** 이 바로 그것이다.<br>
**이 기능은 소스 코드에서 해당 메소드를 '처음부터 없었던 것 처럼' 간주하고 컴파일한다.<br>
그래서 호출부분도 소스에서 빠지게 된다.**<br>
<br>

사용 방법은 이렇다.<br>
1.  Player Settings 에서 사용할 심볼을 등록한다.<br>

![디버그로그](https://user-images.githubusercontent.com/43705434/132552427-083bdc82-194a-4396-bf86-7834408c12ec.PNG)<br>
<br>

TESTMODE_ON 이라고 등록했다.<br>
<br>

2. LOG를 출력하는 메소드를 아래와 같이 정의한다.

```c#
public class Util {

    [System.Diagnostics.Conditional("TESTMODE_ON")]
    public static void Log(string log_p){
        Debug.Log(log_p);
    }
    [System.Diagnostics.Conditional("TESTMODE_ON")]
    public static void LogError(string log_p){
        Debug.LogError(log_p);
    }
    [System.Diagnostics.Conditional("TESTMODE_ON")]
    public static void LogWarning(string log_p){
        Debug.LogWarning(log_p);
    }
}
```
<br>

3. 실제 사용
```c#
Util.Log("문자열"+"문자열");
```
<br>

이렇게 그냥 아무 조건 없이 사용하면 된다.<br>
<br>
<br>

**결론**<br>
**메소드 선언시 바로 윗줄에, [System.Diagnostics.Conditional("심볼문자")] 라고 해주면<br>
C# 컴파일러가 알아서 그 심볼이 있을때에만 메소드가 존재하는 것으로 빌드해 준다.<br>
만약 심볼이 정의되어있지 않다면 선언하는 부분과 호출하는 부분을 모두 제거하고 빌드가 된다.**<br>
<br>
<br>

## 참조링크
https://dydvn.tistory.com/42 <br>
https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=ogoon1004&logNo=221526336230 (Debug.log)<br>
