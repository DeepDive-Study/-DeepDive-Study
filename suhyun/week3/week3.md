# 3주차

### 23장 실행 컨텍스트

실행 컨텍스트는 자바 스크립트의 동작 원리를 담고 있는 핵심 개념 

이해하면 아래 개념들 이해 가능 

- 스코프 기반 식별자와 식별자에 바인딩된 값 관리 방식
- 호이스팅 발생 이유
- 클로저 동작 방식
- 태스크 큐와 함께 동작하는 이벤트 핸들러와 비동기 처리 동작 방식

23.1 소스코드의 타입

| 소스코드 타입 | 설명 |
| --- | --- |
| 전역 코드 | 전역에 존재하는 소스코드  전역에 정의된 함수 , 클래스 등 내부 코드는 포함 x |
| 함수 코드 | 함수 내부에 존재하는 소스코드  ,  함수 내부에 중첩된 함수 클래스등 내부 코드는 포함x |
| eval 코드 | 빌트인 전역 함수인 eval함수에 인수로 전달되어 실행되는 소스코드 |
| 모듈 코드 | 모듈 내부에 존재하는 소스코드   모듈 내부의 함수 클래스 등 내부코드 포함 x |

→ 소스 코드 타입에따라 실행 컨텍스트를 생성하는 과정과 관리 내용이 다르기에 4가지로 분류 

1. 전역 코드 
- 전역 변수 관리를 위해 최상위 스코프 (전역 스코프) 생성
- var 키워드로 선언된 전역 변수와 함수 선언문으로 정의된 전역 함수를 전역 객체의 프로퍼티와 메서드로 바인딩하고 참조하기 위해 전역 객체와 연결
- 전역 코드가 평가되면 **전역 실행 컨텍스트**가 생성됨
- 임마는 그냥 var 전역 변수랑 함수 선언문으로 선언된 코드를 먼저 전역 객체랑 연결해서 인식하는 건가??

1. 함수 코드
- 지역 스코프 생성하고 지역 변수  , 매개변수 , arguments 객체를 관리
- 생성된 지역 스코프를 전역 스코프에서 스코프체인으로 연결
- 함수코드가 평가되면 **함수 실행 컨텍스트**가 생성

1. eval 코드
- strict mode(엄격모드) 에서만 독자적인 스코프 생성
- eval 코드가 평가되면 eval 전역 스코프가 생성

1. 모듈 코드 
- 모듈별로 독립적인 모듈 스코프 생성
- 평가 시 모듈 실행 컨텍스트가 생성

23.2 소스코드의 평가와 실행 

모든 소스코드는 위의 평가 과정을 거친 다음 코드를 실행하기 위한 준비를 한다 

→ js엔진은 소스코드를 **소스코드의 평가 + 소스코드의 실행** 로 처리 한다 

1. 소스코드 평가 
- 각 과정에 맞는 실행 컨텍스트 생성
- 변수와 함수 등의 선언문만 먼저 실행한 다음 생성된 변수와 함수 식별자를 키로 실행 컨텍스트가 관리하는 스코프에 등록
1. 소스코드의 실행 
- 선언문 제외 소스코드가 순차적으로 실행
- 런타임 시작
- 소스코드 실행에 필요한 정보 ( 변수 , 함수의 참조 )를 실행 컨텍스트가 관리하는 스코프에 검색한 후 취득
- 변수 값의 변경 등 소스코드의 실행결과를 다시 실행 컨텍스트가 관리하는 스코프에 등록

  

![Untitled](3%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%209f71a3827e4d45e0b836fa4b787819da/Untitled.png)

```
var x;
x = 1;
```

소스코드 평가 

- 변수 선언문 var x 실행
- x는 실행 컨텍스트가 관리하는 스코프에 등록되고 초기화는 undefined로
- 평가 끝

소스코드 실행

- 선언문 제외하고 실행됨
- 할당문 x=1이 실행되고 x변수가 선언되어 있는지 컨텍스트가 관리하는 스코프에 확인
- 있으면 x를 1로 초기화
- 결과를 실행 컨텍스트에 등록하고 관리

23.2 실행 컨텍스트의 역할 

```
// 전역 변수 선언
const x = 1;
const y = 2;

// 함수 정의
function foo(a) {
  // 지역 변수 선언
  const x = 10;
  const y = 20;

  // 메서드 호출
  console.log(a + x + y); // 130
}

// 함수 호출
foo(100);

// 메서드 호출
console.log(x + y); // 3
```

1. 전역 코드 평가 
- 선언문 실행 (전역 코드의 변수 선언문과 함수 선언문 )
- 생성된 전역 변수와 전역 함수가 실행 컨텍스트가 관리하는 전역 스코프에 등록
- var키워드로 선언된 전역 변수와 함수 선언문으로 정의된 전역 함수는 전역 객체의 프로퍼티와 메서드가 됨

1. 전역 코드 실행 
- 런타임 시작
- 전역 코드가 순차적으로 실행
- 전역 변수 초기화 및 함수 호출 등이 순차적으로 실행
- 홈수 호출 시 전역 코드 실행을 중단하고 함수 내부로 진입 이 때 함수 코드 평가가 이루어짐

1. 함수 코드 평가 
- 매개 변수와 지역 변수 선언문들이 실행 → 생성된 친구들은 지역 스코프에 등록
- 함수 내부에서 지역 변수 처럼 사용할 수 있는 arguments 객체가 생성되어 지역 스코프에 등록되고 this바인딩이 결정

1. 함수 코드 실행 
- 런타임 실행
- 매개변수와 지역변수에 값이 할당
- console.log메서드 호출 → 식별자 console을 스코프 체인으로 검색 → 상위로 검색해서 전역객체에 프로퍼티로 전재하는 거 확인 → log 프로퍼티를 console객체의 프로토타입 체인을 통해 검색 → 메서드 실행 종료

실행 컨텍스트가 하는 거 

1. 선언에 의해 생성된 모든 식별자 (변수 , 함수, 클래스 등 ) 스코프를 구분하여 등록하고 상태변화를 지속적으로 관리
2. 스코프는 중첩관계에 의해 스코프 체인을 형성해야함 상위로 가서 검색 가능 
3. 현재 실행중인 코드의 실행순서를 변경 ( 전역에서 함수내부로 들어가기) 할 수 잇고 다시 되돌아 올 수 있어야함 

→ 소스 코드 실행하는데 필요한 환경을 제공하고 코드의 실행 결과를 실제로 관리하는 영역 

→ 식별자를 등록하고 관리하는 스코프와 코드 실행 순서 관리를 구현한 내부 매커니즘 

→ 모든 코드는 실행 컨텍스트를 통해 실행되고 관리 

23.4 실행 컨텍스트 스택 

→ 생성된 컨텍스트는 스택 자료구조로 관리됨 

```
const x = 1;

function foo () {
  const y = 2;

  function bar () {
    const z = 3;
    console.log(x + y + z);
  }
  bar();
}

foo(); // 6
```

1. 전역 코드 평가와 실행
2. foo함수 코드 평가와 실행 
3. bar함수 코드 평가와 실행 
4. foo 함수 코드 복귀
5. 전역 코드로 복귀 

23.5 렉시컬 환경 

렉시컬 환경 

- 식별자와 식별자에 바인딩 된 값 , 상위 스코프에 대한 참조기록하는 자료 구조
- 실행 컨텍스트 구성하는 컴포넌트
- **스코프와 식별자를 관리**

![[https://velog.io/@1g2g/JS-클로저](https://velog.io/@1g2g/JS-%ED%81%B4%EB%A1%9C%EC%A0%80)](3%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%209f71a3827e4d45e0b836fa4b787819da/Untitled%201.png)

[https://velog.io/@1g2g/JS-클로저](https://velog.io/@1g2g/JS-%ED%81%B4%EB%A1%9C%EC%A0%80)

키와 값 (객체) 형태의 스코프를 생성하여 식별자를 키로 등록하고 바인딩된 값을 관리 

→ 스코프를 구분해 식별자 등록 및 관리하는 저장소 역할 

[VariableEnvironment / LexicalEnvironment차이점 - 인프런 | 질문 & 답변](https://www.inflearn.com/questions/134138/variableenvironment-lexicalenvironment차이점)

23.6 실행 컨텍스트의 생성과 식별자 검색 과정 

```
var x = 1;
const y = 2;

function foo (a) {
  var x = 3;
  const y = 4;

  function bar (b) {
    const z = 5;
    console.log(a + b + x + y + z);
}
  bar(10);
}

foo(20); // 42
```

1. 전역 객체 생성
2. 전역 코드 평가 
3. 전역 코드 실행
4. foo함수 평가 실행 
5. bar함수 평가 실행 종료
6. foo함수 종료
7. w전역 코드 실행 종료

---

### 13장 스코프

13.1 스코프란?

- `식별자가 유효한 범위`
- 

```jsx
function add(x, y) {
  // 매개변수는 함수 몸체 내부에서만 참조할 수 있다.
  // 즉, 매개변수의 스코프(유효범위)는 함수 몸체 내부다.
  console.log(x, y); // 2 5
  return x + y;
}

add(2, 5);

// 매개변수는 함수 몸체 내부에서만 참조할 수 있다.
console.log(x, y); // ReferenceError: x is not defined
```

```jsx
var var1 = 1; // 코드의 가장 바깥 영역에서 선언한 변수

if (true) {
  var var2 = 2; // 코드 블록 내에서 선언한 변수
  if (true) {
    var var3 = 3; // 중첩된 코드 블록 내에서 선언한 변수
  }
}

function foo() {
  var var4 = 4; // 함수 내에서 선언한 변수

  function bar() {
    var var5 = 5; // 중첩된 함수 내에서 선언한 변수
  }
}

console.log(var1); // 1
console.log(var2); // 2
console.log(var3); // 3
console.log(var4); // ReferenceError: var4 is not defined
console.log(var5); // ReferenceError: var5 is not defined––
```

변수는 자신이 선언된 위치에 의해 자신이 유효한 범위가 설정됨 

**모든 식별자(변수 이름 , 함수 이름 ,클래스 이름 등 )자신이 선언된 위치에 의해 다른코드가 식별자 자신을 참조할 수 잇는 유효범위가 결정된다**

 

```
var x = 'global';

function foo() {
  var x = 'local';
  console.log(x); // ①
}

foo();

console.log(x); // ②
```

1 :  local

2 : global

식별자 결정 : 이름이 같은 두 변수 중 어떤 변수를 참조할 것인지 결정하는 것 

해당 식별자 결정할 때 스코프를 통해 어떤 변수를 참조할 것인지 사용한다 

→ 여기선 같은 변수라도 해당 변수가 유효한 범위가 다르기에 결과값도 달라진다

스코프를 통해 식별자의 충돌을 방지하여 같은 이름의 변수도 사용할 수 있도록 함 

스코프 내에서 식별자는 유일해야하지만 다른 스코프에는 같은 이름의 식별자를 사용할 수 있다 

**스코프 = 네임 스페이스**

```
function foo() {
  var x = 1;
  // var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
  // 아래 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.
  var x = 2;
  console.log(x); // 2
}
foo();
```

var의 경우 같은 유효스페이스라도 중복을 허용 

```
function bar() {
  let x = 1;
  // let이나 const 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용하지 않는다.
  let x = 2; // SyntaxError: Identifier 'x' has already been declared
}
bar();
```

let의 경우 중복을 허용하지 않음 

| 구분 | 설명 | 스코프 | 변수 |
| --- | --- | --- | --- |
| 전역 | 코드의 가장 바깥영역 | 전역 스코프 | 전역 변수 |
| 지역 | 함수 몸체 내부 | 지역 스코프 | 지역 변수 |

전역 

- 코드의 가장 바깥 영역
- 전역 스코프
- 전역 변수는 어디서든 참조 가능

13.2.2 지역과 지역 스코프 

**지역** : 함수 몸체 내부 

지역 변수 : 자신이 선언된 지역과 하위 지역 스코프에서 유효 

13.3 스코프 체인 

함수는 전역에서도 지역에서도 정의 가능 

중첩 함수 : 함수 몸체 내부에서 정의한 함수

외부 함수 : 중첩함수를 포함하는 함수 

스코프는 함수의 중첩에 의해 계층적인 구조를 가진다 = 마치 디렉토리 처럼 

→ 스코프가 계층적으로 연결된 것을 스코프체인이라고 함 : 중첩함수의 계층적 구조 

변수를 참조할 떄 js엔지은 스코프체인을 통해서 변수를 참조하는 코드의 스코프 → 상위 스코프 방향으로 이동하며 변수를 검색 

→ 상위 스코프에서 선언한 변수를 하위 스코프에서 사용할 수 있는 이유 

→ 하위에서 변수 선언한 경우 이를 참조할 때 일단 해당 스코프에서 찾은 뒤 없으면 그 위로 위로 상위 스코프로 가서 찾는 느낌인 듯 

→ 하위에서 선언한 거 검색했을 때 해당 스코프에서 있으면 검색 종료 없으면 상위로 감 

13.3.1 스코프 체인에 의한 변수 검색 

상위 스코프에서 유요한 변수는 하위 스코프에서 자유롭게 참조가능 하지만 하위 스코프에서 유효한 변수는 상위 스코프에서 참조 불가 → 애초에 방향이 위로 가는거지 아래로 찾는게 아님 

→ 상속이랑 유사 : 자식이 부모꺼 쓰지 , 부모는 자식꺼 못 씀 

13.3.2 스코프 체인에 의한 함수 검색 

```
// 전역 함수
function foo() {
  console.log('global function foo');
}

function bar() {
  // 중첩 함수
  function foo() {
    console.log('local function foo');
  }

  foo(); // ①
}

bar();
```

함수 선언문으로 함수 정의하면 런타임 이전에 함수 객체가 생성되고 함수 이름과 같은 식별자가 암묵적으로 선언 됨 

foo함수 실행시 실행시 여기서도 스코프 범위 내에서 찾아서 하는 듯 

변수 검색이랑 다름이 없다! 

13.4 함수 레벨 스코프 

지역은 함수 몸체 내부  → **코드 블록이 아닌 함수에 의해서만 지역 스코프가 생성됨**

보통 프로그래밍 언어는 코드 블럭 ( if , try catch , for) 등으로 지역 스코프를 만든다 → 블록 레벨 스코프 

var키워드로 선언된 변수는 오로지 함수의 코드 블록(함수 몸체)만을 지역 스코프로 인정 = 함수 레벨 스코프 

```
var x = 1;

if (true) {
  // var 키워드로 선언된 변수는 함수의 코드 블록(함수 몸체)만을 지역 스코프로 인정한다.
  // 함수 밖에서 var 키워드로 선언된 변수는 코드 블록 내에서 선언되었다 할지라도 모두 전역 변수다.
  // 따라서 x는 전역 변수다. 이미 선언된 전역 변수 x가 있으므로 x 변수는 중복 선언된다.
  // 이는 의도치 않게 변수 값이 변경되는 부작용을 발생시킨다.
  var x = 10;
}

console.log(x); // 10
```

```
var i = 10;

// for 문에서 선언한 i는 전역 변수다. 이미 선언된 전역 변수 i가 있으므로 중복 선언된다.
for (var i = 0; i < 5; i++) {
  console.log(i); // 0 1 2 3 4
}

// 의도치 않게 변수의 값이 변경되었다.
console.log(i); // 5
```

var로 선언된 변수는 블록 레벨 스코프를 인정하지 않는다 즉 전역변수에서 정의 된 변수명이랑 같은 지역 변수명이 있다면 이를 중복 허용을 통해 둘 다 같은 전역변수로 바라보게 되어서 값의 수정이 일어날 수가 있다 

→ let, const를 써야하는 이유 중 하나 

13.5 렉시컬 스코프

```
var x = 1;

function foo() {
  var x = 10;
  bar();
}

function bar() {
  console.log(x);
}

foo(); // ?
bar(); // ?
```

1. 함수를 어디서 호출 했는지에 따라 상위 스코프를 결정 : bar 기준 상위 스코프는 foo랑 전역 스코프 
2. 함수를 어디서 정의 했는지에 따라 상위 스코프를 결정 : bar 기준 상위 스코프는 전역 스코프뿐 

동적 스코프 : 첫번째 방식 

렉시컬 스코프 ( 정적 스코프 ) : 함수를 어디서 정의했는지에 따라 상위 스코프를 결정 호출 위치는 관계 없음 함수의 상위 스코프는 언제나 자신이 정의된 스코프 뿐 

참고로 값은 둘 다 x 

bar가 호출된 시점에서 x값은 전역변수 x = 1 뿐

---

### 14장 전역 변수의 문제점

전역 변수 무분별하게 사용하면 위험함 굳이 필요가 없다면 지역 변수를 쓰자

14.1 변수의 생명 주기 

14.1.1 지역 변수의 생명 주기 

변수는 선언된 위치에서 생성되고 소멸함 

→ 함수 내부 지역 변수면 함수 호출시 생성 함수 종료시 삭제

```
function foo() {
  var x = 'local';
  console.log(x); // local
  return x;
}

foo();
console.log(x); // ReferenceError: x is not defined
```

변수 선언은 선언문이 어디에 있던 가장 먼저 시행됨 → 런타임 이전 단계에 엔진에 의해서 실행됨  전역변수 한정

```
var x = 'global';

function foo() {
  console.log(x); // ①
  var x = 'local';
}

foo();
console.log(x); // global
```

1 : undefinded

→ 지역 변수 를 참조해서 값 출력  

→ 이미 함수 실행 시점에서 x가 undefinded로 초기화 된 상태에서 변수 검색하고 지역 변수 x를 출력하게 됨 

→ 호이스팅 : 변수 선언이 스코프의 선두로 끌어 올려진 것 처럼 동작하는 것 

14.1.2 전역 변수의 생명 주기 

var키워드로 선언한 전역 변수의 생명주기는 전역 객체의 생명주기와 일치

14.2 전역 변수의 문제점 

1. 암묵적 결합
- 어디서든 전역 변수 값 참조하고 변경이 됨

1. 긴 생명 주기
- 메모리 리소스를 오랫동안 차지함

1. 스코프 체인 상에서 종점에 존재
- 변수 검색시 전역 변수가 마지막에 검색되니까 속도가 가장 느리다

1. **네임 스페이스 오염** 
- 자바스크립트의 큰 문제점은 파일이 분리되어 있어도 전역 스코프를 공유한다
- 파일 내에 동일한 이름의 전역 변수나 전역함수가 있으면 예상 못한 결과 초래 가능

14.3 전역 변수 사용 억제하는 법

웬만하면 그냥 지역변수 써라 

14.3.2 네임스페이스 객체 

```
var MYAPP = {}; // 전역 네임스페이스 객체

MYAPP.name = 'Lee';

console.log(MYAPP.name); // Lee
```

네임스페이스의 계층적 구성도 가능함 

```
var MYAPP = {}; // 전역 네임스페이스 객체

MYAPP.person = {
  name: 'Lee',
  address: 'Seoul'
};

console.log(MYAPP.person.name); // Lee
```

14.3.3 모듈 패턴 

관련있는 변수와 함수를 모아 즉시 실행함수로 감싸서 하나의 모듈로 만든 것 

자바스크립트는 접근 제어자가 없음 

모듈 패턴으로 전역 네임스페이스 오염을 막고 정보 은닉에 사용 가능 

```
var Counter = (function () {
  // private 변수
  var num = 0;

  // 외부로 공개할 데이터나 메서드를 프로퍼티로 추가한 객체를 반환한다.
  return {
    increase() {
      return ++num;
    },
    decrease() {
      return --num;
    }
  };
}());

// private 변수는 외부로 노출되지 않는다.
console.log(Counter.num); // undefined

console.log(Counter.increase()); // 1
console.log(Counter.increase()); // 2
console.log(Counter.decrease()); // 1
console.log(Counter.decrease()); // 0
```

즉시 실행함수는 객체를 반환함 

해당 객체는 외부에 노출하고 싶은 변수나 함수를 담아서 반환 

반환도디는 객체의 프로퍼티는 외부에 노출되는 퍼블릭 멤버 

반환되지 않은 객체는 프라이빗멤버가 됨 

14.3.4 es6모듈 

더 이상 전역변수 사용 불가 

es6모듈은 파일 자체의 독자적인 모듈 스코프를 제공 

→ 개꿀인데? 

---

### 15장  let , const 키워드와 블록레벨  스코프

15.1 var 키워드로 선언한 변수의 문제점 

15.1.1 변수 중복 선언 허용 

```
var x = 1;
var y = 1;

// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
// 초기화문이 있는 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.
var x = 100;
// 초기화문이 없는 변수 선언문은 무시된다.
var y;

console.log(x); // 100
console.log(y); // 1
```

15.1.2 함수 레벨 스코프 

var키워드로 선언한 변수는 오로지 함수의 코드 블록만을 지역스코프로 인정 

함수 외부에서 var키워드 서언한 변수는 코드내에서 선언해도 전역변수로 취급 

```
var x = 1;

if (true) {
  // x는 전역 변수다. 이미 선언된 전역 변수 x가 있으므로 x 변수는 중복 선언된다.
  // 이는 의도치 않게 변수값이 변경되는 부작용을 발생시킨다.
  var x = 10;
}

console.log(x); // 10
```

```
var i = 10;

// for문에서 선언한 i는 전역 변수이다. 이미 선언된 전역 변수 i가 있으므로 중복 선언된다.
for (var i = 0; i < 5; i++) {
  console.log(i); // 0 1 2 3 4
}

// 의도치 않게 i 변수의 값이 변경되었다.
console.log(i); // 5

```

15.1.3 변수 호이스팅 

```
// 이 시점에는 변수 호이스팅에 의해 이미 foo 변수가 선언되었다(1. 선언 단계)
// 변수 foo는 undefined로 초기화된다. (2. 초기화 단계)
console.log(foo); // undefined

// 변수에 값을 할당(3. 할당 단계)
foo = 123;

console.log(foo); // 123

// 변수 선언은 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 실행된다.
var foo;
```

15.2 let키워드 

15.2.1 변수 중복 선언 금지 

```
var foo = 123;
// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
// 아래 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.
var foo = 456;

let bar = 123;
// let이나 const 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용하지 않는다.
let bar = 456; // SyntaxError: Identifier 'bar' has already been declared
```

15.2.2 블록 레벨 스코프 

let키워드로 선언한 변수는 모든 코드 플록을 지역 스코프로 인정하는 블록 레벨 스코프를 따름 

```
let foo = 1; // 전역 변수

{
  let foo = 2; // 지역 변수
  let bar = 3; // 지역 변수
}

console.log(foo); // 1
console.log(bar); // ReferenceError: bar is not defined
```

15.1.3 변수 호이스팅 

```
console.log(foo); // ReferenceError: foo is not defined
let foo;
```

var키워드로 선언된 변수는 런타임 이전에 이미 선언이랑 초기화가 진행됨 

```
// var 키워드로 선언한 변수는 런타임 이전에 선언 단계와 초기화 단계가 실행된다.
// 따라서 변수 선언문 이전에 변수를 참조할 수 있다.
console.log(foo); // undefined

var foo;
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

let키워드로 선언한 변수는 선언 단계와 초기화 단계가 분리되어서 선언만 되고 나중에 변수 선언문에서 초기화가 진행됨 

```
// 런타임 이전에 선언 단계가 실행된다. 아직 변수가 초기화되지 않았다.
// 초기화 이전의 일시적 사각 지대에서는 변수를 참조할 수 없다.
console.log(foo); // ReferenceError: foo is not defined

let foo; // 변수 선언문에서 초기화 단계가 실행된다.
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

```
let foo = 1; // 전역 변수

{
  console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
  let foo = 2; // 지역 변수
}
```

여전히 호이스팅이 발생ㅇ하긴 함 

15.2.4 전역 객체와 let

```
// 이 예제는 브라우저 환경에서 실행해야 한다.

// 전역 변수
var x = 1;
// 암묵적 전역
y = 2;
// 전역 함수
function foo() {}

// var 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티다.
console.log(window.x); // 1
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(x); // 1

// 암묵적 전역은 전역 객체 window의 프로퍼티다.
console.log(window.y); // 2
console.log(y); // 2

// 함수 선언문으로 정의한 전역 함수는 전역 객체 window의 프로퍼티다.
console.log(window.foo); // ƒ foo() {}
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(foo); // ƒ foo() {}
```

let 키워드로 서언한 전역 변수는 전역 객체 (window, global)의 객체 프로퍼티가 아님 

```
// 이 예제는 브라우저 환경에서 실행해야 한다.
let x = 1;

// let, const 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티가 아니다.
console.log(window.x); // undefined
console.log(x); // 1
```

15.3 const 키워드 

상수 선언위해 사용 

15.3.1 선언과 초기화

const는 반드시 선언과 동시에 초기화 해야함 

`const foo = 1;`

`const foo; // SyntaxError: Missing initializer in const declaration`

```
{
  // 변수 호이스팅이 발생하지 않는 것처럼 동작한다
  console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
  const foo = 1;
  console.log(foo); // 1
}

// 블록 레벨 스코프를 갖는다.
console.log(foo); // ReferenceError: foo is not defined
```

15.3.2 재할당 금지

```
const foo = 1;
foo = 2; // TypeError: Assignment to constant variable.
```

15.3.3 상수

재할당이 금지된 변수 

가독성과 상태 유지 , 유지보수를 위해 적극적으로 사용하자!

```
// 세전 가격
let preTaxPrice = 100;

// 세후 가격
// 0.1의 의미를 명확히 알기 어렵기 때문에 가독성이 좋지 않다.
let afterTaxPrice = preTaxPrice + (preTaxPrice * 0.1);

console.log(afterTaxPrice); // 110
```

상수 사용으로 가독성 높아짐 

```
// 세율을 의미하는 0.1은 변경할 수 없는 상수로서 사용될 값이다.
// 변수 이름을 대문자로 선언해 상수임을 명확히 나타낸다.
const TAX_RATE = 0.1;

// 세전 가격
let preTaxPrice = 100;

// 세후 가격
let afterTaxPrice = preTaxPrice + (preTaxPrice * TAX_RATE);

console.log(afterTaxPrice); // 110
```

15.3.4 const 키워드 

변수는 변경이 불가능하지만 const 키워드로 선언된 변수에 객체를 할당한 경우 값을 변경할 수 있다 

→ 객체는 참조값이니까 

```
const person = {
  name: 'Lee'
};

// 객체는 변경 가능한 값이다. 따라서 재할당없이 변경이 가능하다.
person.name = 'Kim';

console.log(person); // {name: "Kim"}
```

---

### 16장 프로퍼티 어트리뷰트

16.1 내부 슬롯과 내부 메서드 

자바스크립트 엔진의 구현 알고리즘 설명을 위해 사용하는 의사 프로퍼티와 의사메서드 

[[..]] 로 감싼 이름들이 내부 슬롯과 내부 메서드 

16.2 프로퍼티 어트리뷰트와 프로퍼티 드스크립터 객체 

자바스크립트 엔진은 프로퍼티 생성할 때 프로퍼티의 상태를 나타내는 프로퍼티 어트리뷰트를 기본값으로 자동 정의 

```
const person = {
  name: 'Lee'
};

// 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 반환한다.
console.log(Object.getOwnPropertyDescriptor(person, 'name'));
// {value: "Lee", writable: true, enumerable: true, configurable: true}
```

메서드 호출 시 첫번쨰 매개변수에 객체의 참조

두번 째 매개변수에 프로퍼티 키를 문자열로 전달 

Object.getOwnPropertyDescriptor() 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체 반환 

```
const person = {
  name: 'Lee'
};

// 프로퍼티 동적 생성
person.age = 20;

// 모든 프로퍼티의 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체들을 반환한다.
console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: {value: "Lee", writable: true, enumerable: true, configurable: true},
  age: {value: 20, writable: true, enumerable: true, configurable: true}
}
*/
```

16.3 데이터 프로퍼티와 접근자 프로퍼티 

데이터 프로퍼티

- 키와 값으로 구성된 일반적인 프로퍼티

접근자 프로퍼티 

- 자체적인 값은 없고 다른 데이터 프로퍼티 값을 읽거나 저장할 때 호출되는 접근자 함수로 구성됨

16.3.1 데이터 프로퍼티 

![[https://velog.io/@dev0408/modern-javascript-deep-dive-12](https://velog.io/@dev0408/modern-javascript-deep-dive-12)](3%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%209f71a3827e4d45e0b836fa4b787819da/Untitled%202.png)

[https://velog.io/@dev0408/modern-javascript-deep-dive-12](https://velog.io/@dev0408/modern-javascript-deep-dive-12)

```
const person = {
  name: 'Lee'
};

// 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 취득한다.
console.log(Object.getOwnPropertyDescriptor(person, 'name'));
// {value: "Lee", writable: true, enumerable: true, configurable: true}
```

value프로퍼티의 값은 Lee → 프로퍼티 어트리뷰트 [[Valeu]] 값이 Lee

위와 같이 프로퍼티가 생성될 대 value값은 프로퍼티 값으로 초기화되거 나머지 ㄱ밧은 true로 초기화됨 

프로퍼티를 동적 추가해도 같음 

```
const person = {
  name: 'Lee'
};

// 프로퍼티 동적 생성
person.age = 20;

console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: {value: "Lee", writable: true, enumerable: true, configurable: true},
  age: {value: 20, writable: true, enumerable: true, configurable: true}
}
*/
```

16.3.2 접근자 프로퍼티 

자체적인 값은 없고 다른 데이터 프로퍼티 값 읽거나 저장할 때 사용

**getter setter함수**라고도 부름 

```
const person = {
  // 데이터 프로퍼티
  firstName: 'Ungmo',
  lastName: 'Lee',

  // fullName은 접근자 함수로 구성된 접근자 프로퍼티다.
  // getter 함수
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
  // setter 함수
  set fullName(name) {
    // 배열 디스트럭처링 할당: "31.1 배열 디스트럭처링 할당" 참고
    [this.firstName, this.lastName] = name.split(' ');
  }
};

// 데이터 프로퍼티를 통한 프로퍼티 값의 참조.
console.log(person.firstName + ' ' + person.lastName); // Ungmo Lee

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출된다.
person.fullName = 'Heegun Lee';
console.log(person); // {firstName: "Heegun", lastName: "Lee"}

// 접근자 프로퍼티를 통한 프로퍼티 값의 참조
// 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출된다.
console.log(person.fullName); // Heegun Lee

// firstName은 데이터 프로퍼티다.
// 데이터 프로퍼티는 [[Value]], [[Writable]], [[Enumerable]], [[Configurable]] 프로퍼티 어트리뷰트를 갖는다.
let descriptor = Object.getOwnPropertyDescriptor(person, 'firstName');
console.log(descriptor);
// {value: "Heegun", writable: true, enumerable: true, configurable: true}

// fullName은 접근자 프로퍼티다.
// 접근자 프로퍼티는 [[Get]], [[Set]], [[Enumerable]], [[Configurable]] 프로퍼티 어트리뷰트를 갖는다.
descriptor = Object.getOwnPropertyDescriptor(person, 'fullName');
console.log(descriptor);
// {get: ƒ, set: ƒ, enumerable: true, configurable: true}
```

```
// 일반 객체의 __proto__는 접근자 프로퍼티다.
Object.getOwnPropertyDescriptor(Object.prototype, '__proto__');
// {get: ƒ, set: ƒ, enumerable: false, configurable: true}

// 함수 객체의 prototype은 데이터 프로퍼티다.
Object.getOwnPropertyDescriptor(function() {}, 'prototype');
// {value: {...}, writable: true, enumerable: false, configurable: false}
```

16.4 프로퍼티 정의 

→ 새로운 프로퍼티를 추가하면서 프로퍼티의 속성을 명시적으로 정의하거나 기존 프로퍼티의 프로퍼티 속성을 재정의 하는 것 

Object.defineProperty메서드 사용하면 프로퍼티의 속성을 정의 할 수 있음 

```
const person = {};

// 데이터 프로퍼티 정의
Object.defineProperty(person, 'firstName', {
  value: 'Ungmo',
  writable: true,
  enumerable: true,
  configurable: true
});

Object.defineProperty(person, 'lastName', {
  value: 'Lee'
});

let descriptor = Object.getOwnPropertyDescriptor(person, 'firstName');
console.log('firstName', descriptor);
// firstName {value: "Ungmo", writable: true, enumerable: true, configurable: true}

// 디스크립터 객체의 프로퍼티를 누락시키면 undefined, false가 기본값이다.
descriptor = Object.getOwnPropertyDescriptor(person, 'lastName');
console.log('lastName', descriptor);
// lastName {value: "Lee", writable: false, enumerable: false, configurable: false}

// [[Enumerable]]의 값이 false인 경우
// 해당 프로퍼티는 for...in 문이나 Object.keys 등으로 열거할 수 없다.
// lastName 프로퍼티는 [[Enumerable]]의 값이 false이므로 열거되지 않는다.
console.log(Object.keys(person)); // ["firstName"]

// [[Writable]]의 값이 false인 경우 해당 프로퍼티의 [[Value]]의 값을 변경할 수 없다.
// lastName 프로퍼티는 [[Writable]]의 값이 false이므로 값을 변경할 수 없다.
// 이때 값을 변경하면 에러는 발생하지 않고 무시된다.
person.lastName = 'Kim';

// [[Configurable]]의 값이 false인 경우 해당 프로퍼티를 삭제할 수 없다.
// lastName 프로퍼티는 [[Configurable]]의 값이 false이므로 삭제할 수 없다.
// 이때 프로퍼티를 삭제하면 에러는 발생하지 않고 무시된다.
delete person.lastName;

// [[Configurable]]의 값이 false인 경우 해당 프로퍼티를 재정의할 수 없다.
// Object.defineProperty(person, 'lastName', { enumerable: true });
// Uncaught TypeError: Cannot redefine property: lastName

descriptor = Object.getOwnPropertyDescriptor(person, 'lastName');
console.log('lastName', descriptor);
// lastName {value: "Lee", writable: false, enumerable: false, configurable: false}

// 접근자 프로퍼티 정의
Object.defineProperty(person, 'fullName', {
  // getter 함수
  get() {
    return `${this.firstName} ${this.lastName}`;
  },
  // setter 함수
  set(name) {
    [this.firstName, this.lastName] = name.split(' ');
  },
  enumerable: true,
  configurable: true
});

descriptor = Object.getOwnPropertyDescriptor(person, 'fullName');
console.log('fullName', descriptor);
// fullName {get: ƒ, set: ƒ, enumerable: true, configurable: true}

person.fullName = 'Heegun Lee';
console.log(person); // {firstName: "Heegun", lastName: "Lee"}
```

Object.definProperty메서드는 한 번에 하나의 프로퍼티만 정의 

Object.definProperties 메서드는 여러 개의 프로퍼티 한 번에 정의 간으 

```
const person = {};

Object.defineProperties(person, {
  // 데이터 프로퍼티 정의
  firstName: {
    value: 'Ungmo',
    writable: true,
    enumerable: true,
    configurable: true
  },
  lastName: {
    value: 'Lee',
    writable: true,
    enumerable: true,
    configurable: true
  },
  // 접근자 프로퍼티 정의
  fullName: {
    // getter 함수
    get() {
      return `${this.firstName} ${this.lastName}`;
    },
    // setter 함수
    set(name) {
      [this.firstName, this.lastName] = name.split(' ');
    },
    enumerable: true,
    configurable: true
  }
});

person.fullName = 'Heegun Lee';
console.log(person); // {firstName: "Heegun", lastName: "Lee"}
```

16.5 객체 변경 방지 

객체는 변경 가능한 값이니까 재할당 없이 직접 변경 가능 → 프로퍼티 추가 삭제 갱신 가능 및 위의 메서드 사용해서 재정의도 가능 

![[https://velog.io/@dev0408/modern-javascript-deep-dive-12](https://velog.io/@dev0408/modern-javascript-deep-dive-12)](3%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%209f71a3827e4d45e0b836fa4b787819da/Untitled%203.png)

[https://velog.io/@dev0408/modern-javascript-deep-dive-12](https://velog.io/@dev0408/modern-javascript-deep-dive-12)

16.5.1 객체 확장 금지 

Object.preventExtensions 메서드는 객체의 확장을 금지 

```
const person = { name: 'Lee' };

// person 객체는 확장이 금지된 객체가 아니다.
console.log(Object.isExtensible(person)); // true

// person 객체의 확장을 금지하여 프로퍼티 추가를 금지한다.
Object.preventExtensions(person);

// person 객체는 확장이 금지된 객체다.
console.log(Object.isExtensible(person)); // false

// 프로퍼티 추가가 금지된다.
person.age = 20; // 무시. strict mode에서는 에러
console.log(person); // {name: "Lee"}

// 프로퍼티 추가는 금지되지만 삭제는 가능하다.
delete person.name;
console.log(person); // {}

// 프로퍼티 정의에 의한 프로퍼티 추가도 금지된다.
Object.defineProperty(person, 'age', { value: 20 });
// TypeError: Cannot define property age, object is not extensible
```

16.5.2 객체 밀봉 

Object.seal 메서드는 객체를 밀봉 → 읽기와 쓰기만 가능핟로ㅗㄱ 만듦 

```
const person = { name: 'Lee' };

// person 객체는 밀봉(seal)된 객체가 아니다.
console.log(Object.isSealed(person)); // false

// person 객체를 밀봉(seal)하여 프로퍼티 추가, 삭제, 재정의를 금지한다.
Object.seal(person);

// person 객체는 밀봉(seal)된 객체다.
console.log(Object.isSealed(person)); // true

// 밀봉(seal)된 객체는 configurable이 false다.
console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: {value: "Lee", writable: true, enumerable: true, configurable: false},
}
*/

// 프로퍼티 추가가 금지된다.
person.age = 20; // 무시. strict mode에서는 에러
console.log(person); // {name: "Lee"}

// 프로퍼티 삭제가 금지된다.
delete person.name; // 무시. strict mode에서는 에러
console.log(person); // {name: "Lee"}

// 프로퍼티 값 갱신은 가능하다.
person.name = 'Kim';
console.log(person); // {name: "Kim"}

// 프로퍼티 어트리뷰트 재정의가 금지된다.
Object.defineProperty(person, 'name', { configurable: true });
// TypeError: Cannot redefine property: name
```

16.3 객체 동결 

Object.freeze 메서드는 객체를 동결 → 읽기만 가능 

```
const person = { name: 'Lee' };

// person 객체는 동결(freeze)된 객체가 아니다.
console.log(Object.isFrozen(person)); // false

// person 객체를 동결(freeze)하여 프로퍼티 추가, 삭제, 재정의, 쓰기를 금지한다.
Object.freeze(person);

// person 객체는 동결(freeze)된 객체다.
console.log(Object.isFrozen(person)); // true

// 동결(freeze)된 객체는 writable과 configurable이 false다.
console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: {value: "Lee", writable: false, enumerable: true, configurable: false},
}
*/

// 프로퍼티 추가가 금지된다.
person.age = 20; // 무시. strict mode에서는 에러
console.log(person); // {name: "Lee"}

// 프로퍼티 삭제가 금지된다.
delete person.name; // 무시. strict mode에서는 에러
console.log(person); // {name: "Lee"}

// 프로퍼티 값 갱신이 금지된다.
person.name = 'Kim'; // 무시. strict mode에서는 에러
console.log(person); // {name: "Lee"}

// 프로퍼티 어트리뷰트 재정의가 금지된다.
Object.defineProperty(person, 'name', { configurable: true });
// TypeError: Cannot redefine property: name
```

16.5.4 불변 객체 

위의 변경 방지 메서드들은 얕은 변경 방지 → 직속 프로퍼티만 변경이 방지되고 중첩 객체에는 영향을ㅇ 못 줌 

```
const person = {
  name: 'Lee',
  address: { city: 'Seoul' }
};

// 얕은 객체 동결
Object.freeze(person);

// 직속 프로퍼티만 동결한다.
console.log(Object.isFrozen(person)); // true
// 중첩 객체까지 동결하지 못한다.
console.log(Object.isFrozen(person.address)); // false

person.address.city = 'Busan';
console.log(person); // {name: "Lee", address: {city: "Busan"}}
```

객체 중첩까지 동결할려면  객체 값으로 갖는 모든 프로퍼티에 대해 재귀적으로 메서드 호출이 필요 

```
function deepFreeze(target) {
  // 객체가 아니거나 동결된 객체는 무시하고 객체이고 동결되지 않은 객체만 동결한다.
  if (target && typeof target === 'object' && !Object.isFrozen(target)) {
    Object.freeze(target);
    /*
      모든 프로퍼티를 순회하며 재귀적으로 동결한다.
      Object.keys 메서드는 객체 자신의 열거 가능한 프로퍼티 키를 배열로 반환한다.
      ("19.15.2. Object.keys/values/entries 메서드" 참고)
      forEach 메서드는 배열을 순회하며 배열의 각 요소에 대하여 콜백 함수를 실행한다.
      ("27.9.2. Array.prototype.forEach" 참고)
    */
    Object.keys(target).forEach(key => deepFreeze(target[key]));
  }
  return target;
}

const person = {
  name: 'Lee',
  address: { city: 'Seoul' }
};

// 깊은 객체 동결
deepFreeze(person);

console.log(Object.isFrozen(person)); // true
// 중첩 객체까지 동결한다.
console.log(Object.isFrozen(person.address)); // true

person.address.city = 'Busan';
console.log(person); // {name: "Lee", address: {city: "Seoul"}}
```

---

### 17장 생성자 함수에 의한 객체 생성

17.1 Object생성자 함수 

new연산자와 함께 Object 생성자 함수를 호출하면 빈 객체를 생성하여 반환 

이후 빈 객체에 프로퍼티 또는 메서드 추가하여 객체 완성 가능 

```
// 빈 객체의 생성
const person = new Object();

// 프로퍼티 추가
person.name = 'Lee';
person.sayHello = function () {
  console.log('Hi! My name is ' + this.name);
};

console.log(person); // {name: "Lee", sayHello: ƒ}
person.sayHello(); // Hi! My name is Lee
```

생성자 함수란 new연산자와 함께 호출하여 객체를 생성하ㅡㄴ 함수 

생성자 함수로 생성된 객체 = 인스턴스 

Object 생성자 함수 이외에 String, Number , Boolean ,Function , Array 등 빌트인 생성자 함수 제공 

```
// String 생성자 함수에 의한 String 객체 생성
const strObj = new String('Lee');
console.log(typeof strObj); // object
console.log(strObj);        // String {"Lee"}

// Number 생성자 함수에 의한 Number 객체 생성
const numObj = new Number(123);
console.log(typeof numObj); // object
console.log(numObj);        // Number {123}

// Boolean 생성자 함수에 의한 Boolean 객체 생성
const boolObj= new Boolean(true);
console.log(typeof boolObj); // object
console.log(boolObj);        // Boolean {true}

// Function 생성자 함수에 의한 Function 객체(함수) 생성
const func = new Function('x', 'return x * x');
console.log(typeof func); // function
console.dir(func);        // ƒ anonymous(x)

// Array 생성자 함수에 의한 Array 객체(배열) 생성
const arr = new Array(1, 2, 3);
console.log(typeof arr); // object
console.log(arr);        // [1, 2, 3]

// RegExp 생성자 함수에 의한 RegExp 객체(정규 표현식) 생성
const regExp = new RegExp(/ab+c/i);
console.log(typeof regExp); // object
console.log(regExp);        // /ab+c/i

// Date 생성자 함수에 의한 Date 객체 생성
const date = new Date();
console.log(typeof date); // object
console.log(date);        // Mon May 04 2020 08:36:33 GMT+0900 (대한민국 표준시)
```

굳이 Object만 사용하는 게 아니라 객체 리터럴 쓴느게 더 간편 

17.2 생성자 함수 

17.2.1 객체 리터럴에 의한 객체 생성 방식의 문제점 

객체 리터럴 생성은 직관적이고 간편하지만 단 하나의 객체만 생성함 

동일한 프로퍼티를 갖는 객쳐를 여러개 생성할 경우 비효율적 

```
const circle1 = {
  radius: 5,
  getDiameter() {
    return 2 * this.radius;
  }
};

console.log(circle1.getDiameter()); // 10

const circle2 = {
  radius: 10,
  getDiameter() {
    return 2 * this.radius;
  }
};

console.log(circle2.getDiameter()); // 20
```

객체는 프로퍼티로 상태 표현하고 메서드로 동작을 표현 

즉 객체마다 동작은 같지만 상태가 다른 것들이 보통 많음 

객체 리터럴로 만들면 구조가 종일해도 매번 위에 처럼 작성할 코드가 늘어남 

17.2.2 생성자 함수에 의한 객체 생성 방식의 장점 

객체를 생성하기 위한 클래스처럼 생성자 함수를 써서 프로퍼티 구조가 동일한 객체를 여러개 만들 수 있음 

```
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 인스턴스의 생성
const circle1 = new Circle(5);  // 반지름이 5인 Circle 객체를 생성
const circle2 = new Circle(10); // 반지름이 10인 Circle 객체를 생성

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

this

this는 객체 자신의 프로퍼티나 메서드 참조하기 위한 자기 참조 변수 this의 값은 함수 호출 방식에 따라 동적으로 결정됨 

| 함수 호출 방식 | this가 가리키는 값 |
| --- | --- |
| 일반 함수로서 호출 | 전역 객체 |
| 메서드로서 호출 | 메서드 호출한 객체 (마침표 앞의 객체 ) |
| 생성자 함수로서 호출 | 생성자 함수가 생성할 인스턴스  |

```
// 함수는 다양한 방식으로 호출될 수 있다.
function foo() {
  console.log(this);
}

// 일반적인 함수로서 호출
// 전역 객체는 브라우저 환경에서는 window, Node.js 환경에서는 global을 가리킨다.
foo(); // window

// 메서드로서 호출
const obj = { foo }; // ES6 프로퍼티 축약 표현
obj.foo(); // obj

// 생성자 함수로서 호출
const inst = new foo(); // inst
```

생성자 함수 = 객체 생성하는 함수 

new로 붙여서 호출해야 생성자 함수로 동작 

```
// new 연산자와 함께 호출하지 않으면 생성자 함수로 동작하지 않는다.
// 즉, 일반 함수로서 호출된다.
const circle3 = Circle(15);

// 일반 함수로서 호출된 Circle은 반환문이 없으므로 암묵적으로 undefined를 반환한다.
console.log(circle3); // undefined

// 일반 함수로서 호출된 Circle내의 this는 전역 객체를 가리킨다.
console.log(radius); // 15
```

17.2.3 생성자 함수의 인스턴스 생성과정 

인스턴스 생성 + 인스턴스 초기화 

```
// 생성자 함수
function Circle(radius) {
  // 인스턴스 초기화
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 인스턴스 생성
const circle1 = new Circle(5);  // 반지름이 5인 Circle 객체를 생성
```

this에 프로퍼티 추가하고 필요에 따라 전달된 인수로 프로퍼티 초기값을 할당하여 초기화 

다만 여기 보면 반환하는 코드가 없다  → 엔진이 암묵적으로 인스턴스 처리 후 반환하는 것 

1. 인스턴스 생성과 this 바인딩 
- 암묵적으로 빈객체 생성 → 생성자 함수가 만든 인스턴스
- 빈객체가 this에 바인딩 → 함수 내부의 this가 인스턴스 가리키는 이유  ( 런타임 이전에 실행 )

```
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.
  console.log(this); // Circle {}

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

1. 인스턴스 초기화
- 코드가 한줄씩 실행되면서 this에 바인딩된 인스턴스를 초기화

```
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

1. 인스턴스 반환 
- 함수 실행이 끝나면 인스턴스가 바인딩 된 this가 암묵적으로 반환됨

```
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다
}

// 인스턴스 생성. Circle 생성자 함수는 암묵적으로 this를 반환한다.
const circle = new Circle(1);
console.log(circle); // Circle {radius: 1, getDiameter: ƒ}
```

this가 아닌 객체를 명시적 반환하면 return 뒤에 객체가 반환 

→ 웬만하면 return 쓰지 마라 

```
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  // 3. 암묵적으로 this를 반환한다.
  // 명시적으로 객체를 반환하면 암묵적인 this 반환이 무시된다.
  return {};
}

// 인스턴스 생성. Circle 생성자 함수는 명시적으로 반환한 객체를 반환한다.
const circle = new Circle(1);
console.log(circle); // {}
```

다만 객체 반환이 아니라 원시값 반환이면 무시 

```
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  // 3. 암묵적으로 this를 반환한다.
  // 명시적으로 원시값을 반환하면 원시값 반환은 무시되고 암묵적으로 this가 반환된다.
  return 100;
}

// 인스턴스 생성. Circle 생성자 함수는 명시적으로 반환한 객체를 반환한다.
const circle = new Circle(1);
console.log(circle); // Circle {radius: 1, getDiameter: ƒ}
```

17.2.4 내부 메서드 Call , Construct 

함수 선언문 또는 함수 표현식으로 만든 함수도 new연산자로 호출해서 생성자 함수로 사용 가능 

함수는 객체니까 일반 객체와 동일하게 동작 가능 

```
// 함수는 객체다.
function foo() {}

// 함수는 객체이므로 프로퍼티를 소유할 수 있다.
foo.prop = 10;

// 함수는 객체이므로 메서드를 소유할 수 있다.
foo.method = function () {
  console.log(this.prop);
};

foo.method(); // 10
```

함수는 일반객체와 달리 함수는 호출가능 

함수 객체가 가지고 있는 내부 슬롯과 내부 메서드를 쓸 수 있고 Call, Construct 같은 내부 메서드도 추가로 가지고 있음 

함수가 일반 함수로 호출되면 내부 메서드 Call이 호출 

new 연산자로 호출시 내부 메서드 Constuct가 호출

```
function foo() {}

// 일반적인 함수로서 호출: [[Call]]이 호출된다.
foo();

// 생성자 함수로서 호출: [[Construct]]가 호출된다.
new foo();
```

17.2.4 constructor . non-constructor 구분 

- constructor : 함수 선언문 , 함수 표현식, 클래스
- non-constructor : 화살표 함수 , 메서드
- 

```
// 일반 함수 정의: 함수 선언문, 함수 표현식
function foo() {}
const bar = function () {};
// 프로퍼티 x의 값으로 할당된 것은 일반 함수로 정의된 함수다. 이는 메서드로 인정하지 않는다.
const baz = {
  x: function () {}
};

// 일반 함수로 정의된 함수만이 constructor이다.
new foo();   // -> foo {}
new bar();   // -> bar {}
new baz.x(); // -> x {}

// 화살표 함수 정의
const arrow = () => {};

new arrow(); // TypeError: arrow is not a constructor

// 메서드 정의: ES6의 메서드 축약 표현만을 메서드로 인정한다.
const obj = {
  x() {}
};

new obj.x(); // TypeError: obj.x is not a constructor
```

17.2.6 new 연산자 

new 연산자와 함꼐 호출하는 함수는 무조건 constructor이어야함 

```
// 생성자 함수로서 정의하지 않은 일반 함수
function add(x, y) {
  return x + y;
}

// 생성자 함수로서 정의하지 않은 일반 함수를 new 연산자와 함께 호출
let inst = new add();

// 함수가 객체를 반환하지 않았으므로 반환문이 무시된다. 따라서 빈 객체가 생성되어 반환된다.
console.log(inst); // {}

// 객체를 반환하는 일반 함수
function createUser(name, role) {
  return { name, role };
}

// 일반 함수를 new 연산자와 함께 호출
inst = new createUser('Lee', 'admin');
// 함수가 생성한 객체를 반환한다.
console.log(inst); // {name: "Lee", role: "admin"}
```

new연산자 안 쓰면 내부메서드 Call이 호출됨

```
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// new 연산자 없이 생성자 함수 호출하면 일반 함수로서 호출된다.
const circle = Circle(5);
console.log(circle); // undefined

// 일반 함수 내부의 this는 전역 객체 window를 가리킨다.
console.log(radius); // 5
console.log(getDiameter()); // 10

circle.getDiameter();
// TypeError: Cannot read property 'getDiameter' of undefined
```

17.2.7 [new.target](http://new.target) 

new연산자와 함께 생성자 함수로서 호출되면 함수 내부의 [new.target](http://new.target)은 함수 자신을 가리킨다 new 연산자 없이 일반 함수로서 호출된 함수 내부의 new.target = undefinded 

```
// 생성자 함수
function Circle(radius) {
  // 이 함수가 new 연산자와 함께 호출되지 않았다면 new.target은 undefined다.
  if (!new.target) {
    // new 연산자와 함께 생성자 함수를 재귀 호출하여 생성된 인스턴스를 반환한다.
    return new Circle(radius);
  }

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// new 연산자 없이 생성자 함수를 호출하여도 new.target을 통해 생성자 함수로서 호출된다.
const circle = Circle(5);
console.log(circle.getDiameter());  // 10
```