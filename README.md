# 중복코드
## 함수 추출하기 (Extract Function)   중요!!﻿

의도와 구현에 대해서만 분리할것 

코드를 읽을때 이 메소드가 어떤 일을 하는지 파악을 할때 코드를 마치 책 처럼 이해가 되지않는다면 구현을 보고있다고 생각하면 된다. 잘 읽힌다면 의도이다.

한줄짜리 메소드도 의도가 잘 드러난다면 좋다.



이클립스에서 사용하는 방법 1.

=> 추출하고 싶은 부분을 마우스로 드래그 해서, 오른쪽 버튼을 누르면 메뉴가 나오는데, 리펙토링->메소드 추출을 선택하면 된다.

이클립스에서 사용하는 방법 2.

 => 추출하고 싶은 부분을 마우스로 드래그 해서, 'ALT+SHIFT+M'



## 코드 정리

변수 선언은 메소드 안에서 최상단에 하지말고 해당 변수가 쓰기 직전에 선언해주자.

단축키 : eclipse의 경우 이동시키고 싶은 줄 맨앞에 커서 놓고 alt + 화살표 (이렇게 하면 복사하고 지워서 붙여넣을 필요없음)


## 메소드 올리기

하위 클래스에 있는 코드가 상위 클래스가 아닌 하위 클래스 기능에 의존하고 있다면 "필드 올리기"를 적용한 이후에 적용

eclipse 에서 pull up 기능 


# 긴 함수
## 임시 변수를 질의 변수로 바꾸기
 Replace Temp with Query ( 여기서의 Query는 함수를 의미한다.)

 임시 변수를 만들어 내는 표현식을 함수로 빼내라!

## 매개변수 객체 만들기

X( A, B ) 

Y(A, B, C) 처럼 A와 B가 여러 메소드에 걸쳐 나타난다면 A와 B를 묶은 자료구조를 만든다.

객체로 바꿀 A,B를 선택하고 Alt + Shift + T를 클릭하면 리팩토링 메뉴가 뜨는데 

Eclipse 3.3에 추가된 기능인 Introduce Parameter Object를 선택하면된다. 그리고 inner 클래스로 만들지 일반 클래스로 만들지 선택하고 ok 누르면 된다.


## 객체 통째로 넘기기 (Preserve Whole Object)

어떤 한 레코드에서 구할 수 있는 여러 값들을 함수에 전달하는 경우, 해당 매개변수를 레코드 하나로 교체할 수 있다.

매개변수 목록을 줄일 수 있다. 해당 메소드의 위치가 적절한지 생각해야한다. 

## 함수를 명령으로 바꾸기

Command 패턴(디자인패턴)을 적용하면 다음과 같은 장점을 가질수 있다.

1) undo 기능을 만들 수 있다.

2) 더 복잡한 기능을 구현하는데 필요한 여러 메소드를 추가할수 있다. 

3) 상속이나 템플릿을 활용 할 수 있다.

4) 복잡한 메소드를 여러 메소드나 필드를 활용해서 쪼갤 수도 있다.

코드는 간추릴수 있지만 복잡도는 커질수 있다.


## 조건문 분해하기 (Decompose Conditional)

기술적으로 "함수 추출하기"와 동일한 리팩토링이지만 의도만 다를뿐이다.

```c
private Participant findParticipant(String username, List<participant> participants) {

    if ( participants.stream().noneMatch(p -> p.userName().equals(username) ) { 

        participants = new participants(username);

        participants.add(participant);

    } else {

        participant = participants.stream().filter(p -> p.username().equals(username)).findFirst().orElseThrow();

    }

    return participant;

}
```
이 문장이 함수 추출하기처럼 리팩토링을 하면
```c
private Participant findParticipant(String username, List<participant> participants) {

    if( isNewParticipants(username, participants)){

        participant = createNewParticipant( username, participants);

    } else {

        participant = findExistingParticipant(username, participants);

    }

    return participant;

}

private boolean isNewParticipants(String username, List<participant> participants)_ {

    return participants.stream().noneMatch(p -> p.userName().equals(username);

}

private Participaant createNewParticipant(String username, List<participant> participants)_ {

    Participant participant;

    participant = new Participant(username);

    participants.add(participant);

    return participant;

}

private Participaant  findExistingParticipant(String username, List<participant> participants)_ {

    Participant participant;

    participant = participants.stream().filter(p -> p.username().equals(username)).findFirst().orElseThrow();

    return participant;

}
```
이렇게 리팩토링 할수 있고 또 리팩토링을 한다면
```c
private Participant findParticipant(String username, List<participant> participants) {

    return isNewParticipant(username, participants) ? createNewParticipant( username, participants) : findExistingParticipant(username, participants);

}
```
삼항 연산자를 써서 이렇게까지 리팩토링을 할수 있다.


## 반복문 쪼개기 ( Split Loop )

하나의 반복문에서 여러 다른 작업을 하는 코드를 쉽게 찾아볼수 있다.
해당 반복문을 수정 할 때 여러 작업을 모두 고려하며 코딩을 해야한다.
성능 문제를 야기 할 수 있지만 '리팩토링'은 '성능 최적화'와 별개의 작업이다. 리팩토링을 하고 성능최적화를 하길...

```c
for ( ~ ) {

    기능 1 

    if(){ //기능 2

    }

}
```
라면 

for문을 2개 만들고 하나의 for문에는 기능1만 또 다른 하나의 for문에는 기능2를 넣어서 별도의 메소드로 빼고 해당 메소드 들을 호출한다.
```c
{

AAA();

BBB();

}

private AAA () {

    for(){

        기능1;

    }

}

private BBB() {}

    for(){

        if() {기능2; }

    }

}
```

## 조건문을 다형성으로 바꾸기 ( Replace Conditional with Polymorphism)

여러 타입에 따라 각기 다른 로직으로 처리해야 하는 경우에 다형성을 적용해서 조건문을 보다 명확하게 분리할 수 있다. 반복되는 switch문을 각기 다른 클래스를 만들어 제거 할 수 있다.
공통으로 사용되는 로직은 상위클래스에 두고 달라지는 부분만 하위클래스에 둠으로써, 달라지는 부분만 강조할 수 있다.
모든 조건문을 다형성으로 바꿔야 하는 것은 아니다.

# 긴 매개변수 목록
## 긴 매개변수 목록 (Long Parameter List)

매개변수들이 너무 많으면

어떤 매개 변수를 다른 매개변수를 통해 알아낼수 있다면 : "매개변수를 질의함수로 바꾸기" ,

세부적인 데이터를 가져와서 여러 매개변수로 넘기는대신 : "객체 통째로 넘기기",

일부 매개변수들이 대부분 같이 넘겨진다면 : "매개변수 객체 만들기"

매개변수가 플래그로 사용된다면 : "플래그 인수 제거하기"


## 매개변수를 질의 함수로 바꾸기 (Replace Parameter with Query)

함수의 매개변수 목록은 함수의 다양성을 대변하며, 짧을수록 이해하기 좋다.
어떤 한 매개변수를 다른 매개변수를 통해 알아낼수있다 => "중복 매개변수"라고 생각
매개변수에 값을 전달하는 것은 " 함수를 호출하는 쪽"의 책임이다. 가능하면 함수를 호출하는 쪽의 책임을 줄이고 함수 내부에서 책임지도록 노력한다.
"임시 변수를 질의 함수로 바꾸기"와 "함수 선언 변경하기"를 통해 이 리팩토링을 적용


## 플래그 인수 제거하기 (Remove Flag Argument)

한 메소드에 flag가 2개 이상이면 해당 메소드는 너무 많은 일을 하는것이다. 분리해주어야한다.

1개라면 메소드를 호출하는 쪽에서는 호출할 메소드에 들어가서 보기전까지 flag에 대해서 알수있는 정보가 없다.

조건문 분해하기(Decompose Condition) 리팩토링 기술을 활용 할수 있다.


## 여러 함수를 클래스로 묶기 (Combine Functions into Class)

별도의 클래스를 따로 만들어서 관련있는 데이터를 한곳에 모으고(메소드를 모아놓으면 매개변수도 많이 줄일수있다.)

생성자로 해당 클래스 호출시 넘겨주는 파라미터를 받아서 클래스안에서 메소드를 만들어서 묶어준다.

move to class 기능 사용

# 전역 데이터
## 전역 데이터 (Global Data)

ex) 자바의 public static 변수

아무곳에서나 변경될 수 있다는 문제가 있따.
어떤 코드로 인해 값이 바뀐것인지 파악하기 어렵다.
'변수 캡슐화(Encapsulate Variable)'를 적용해서 접근을 제어하거나 어디서 사용하는지 파악하기 쉽게 만들 수 있다.
적용하면 변수 변경이 쉬워지고 변수에 접근 제약을 할수 있다.

## 변수 캡슐화하기 (Encapsulate Variable)

메소드는 점진적으로 새로운 메소드로 변경할 수 있으나, 데이터는 한번에 모두 변경해야 한다.
데이터 구조를 변경하는 작업은 그보다는 조금 더 수월한 메소드 구조 변경 작업으로 대체할 수 있다.
데이터가 사용되는 범위가 클수록 캡슐화를 하는것이 더 중요해진다.
   - 함수를 사용해서 값을 변경하면 보다 쉽게 검증 로직을 추가하거나 변경에 따르는 후속 작업을       추가하는 것이 편리하다.

불변 데이터의 경우에는 이런 리팩토링을 적용할 필요가 없다.


ex_)

todo.status = 4;
todo.isRepeat = false;

이렇게 변수를 사용중이라면 
todo class에서 

private int status;
private boolean isRepeat; 로 선언되어있는것을 getter / setter를 이용해서 메소드화 해서 
todo.setStatus();
todo.setIsRepeat();

로 처리함으로써 변수를 캡슐화 시킨다.

# 
