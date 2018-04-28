---
layout: post
title: "코드랩 day2"
comments: false
description: ""
keywords: ""
---



## Math

- 32비트 계산 관련
  - Math.imul(): 곲한 값을 32비트로 반환
  - C++ 의 비트와 맞추기 위한 것.
  - Math.clz32(): 32비트 값에서 비트 값이 0인 수
  - Math.fround(): 32비트 부동 소수 값으로 변환, 반올림


- Emscripten 프로젝트
  - source to source 컴파일러
  - LLVM 컴파일러를 사용하여 자바스크립트 코드 생성




## String



#### Unicode

- ES6에 유니코드 관련 프로퍼티와 메소드 추가
- 유니코드는 U+0031 형태
- 코드 포인트
  - 0031이 코드 포인트, 문자 코드로 알려져있다.
  - 코드 포인트 값으로 문자/기호/이모지/아이콘 표현
  - U+0000 에서 U+10FFFF까지 110만개 이상 표현
- plane: 코드 포인트 전체를 17개 평면(plane)으로나눔
- 이스케이프 시퀀스
  - 역슬래시와 16진수 값 3개 작성
  - \x31\x32\x33 -> 123
- 유니코드 이스케이프 시퀀스
  - \x31\x32\x33 -> \u0031\u0032\u0033
  - UTF-16진수로 U+0000에서 U+FFFF까지 사용 가능
- 유니코드 코드 포인트 이스케이프
  - U+FFFF보다 큰 포인트를 어떻게 작성할 것인가?
  - \u{34}\u{34}\u{35}







#### Template

- Tagged Template
  - 작성 형태
  - const one =1 , two = 2
  - function show(text, value){}
  - show `1+2=$(one+two)`





## Array



#### find()

- 콜백 함수에 넘겨주는 파라미터 및 순서
  - 배열 엘리먼트
  - 인덱스
  - ​



#### findIndex()

- find()와 처리 방법이 같음
  - 하지만 반환 값이 다름.





## RegExp



#### u플래그

- u(unicode) 플래그
  - 매치 대상을 유니코드로 인식
- 코드 형태
  - /\u{31}\u{32}/u.test{"12"}



#### y 플래그

- 정규 표현식은 매치된 문자열의 인덱스를 lastIndex에 설정
- y(sticky) 플래그
  - lastIndex 위치부터 매치 수행
  - 디폴트 값 0
  - ^패턴을 사용하지 않으면 첫 문자부터 매치



## Generator Object

#### function*

- Generator function
  - function* 키워드를 사용한 함수
- 제너레이터 함수 형태
  - function* 선언문, function* 표현식, GeneratorFunction
- 제너레이터 함수를 호출하면
  - **함수 블록{}을 실행하지 않고**
  - Generator 오브젝트를 생성 (이터레이터 오브젝트) 하여 반환
- 함수 블록 실행
  - Generator 오브젝트 메서드를 호출할 때
- new 연산자로 인스턴스 생성 불가





#### function* 선언문

- 코드 형태

  ```javascript
  function* sports(param1, param2) {
      yield param1 + param2
  };
  const generatorObj = sports(1,2);
  ```



####  function* 표현식







#### GeneratorFunction

- GeneratorFunction.constructor로 제너레이터 함ㅅ ㅜ생성
  - 문자열로 파라미터 작성





#### yield

Syntax: [returnValue] = yield[Expression]

- 코드 형태
  - const result = yield 10 + 20;
- yield: 제너레이터 함수를 멈추거나 재실행
- 오른쪽의 표현식을 평가하고 결과 반환
  - 표현식을 작성하지 않으면 undefined반환
- [[returnValue]]
  - 오른쪽의 평가 결과가 설정되는 것이 아니라
  - 다음 next()에서 파라미터에 정한 값이 설정됨



```javascript
function* sports(one) {
    const two = yield one; // two에 one값을 할당하지 않음
    const param = yield two + one;
    yield param + one;   
};
const genObj = sports(10);
let result = genObj.next();
console.log(result);

result = genObj.next();
console.log(result);

result = genObj.next(20);
console.log(result);
```





- yield 표현식 평가를 완료하면
  - 호출한 곳으로 {value: 값, done: true/false} 형태 반환
- value 값
  - yield 표현식의 평가 결과 설정
  - yield 처리를 하지 못하면 undefined
- done 값
  - yield 처리를 수행하면 false
  - 처리하지 못하면 true
  - 더이상 실행할 코드가 없을 때  true







```javascript
const gen = function*(value) {
    value = value + 10;
    yield ++value;
    value = value + 7;
    yield ++value;
};
const genObj = gen(1);
let result = genObj.next();
console.log(result);

result = genObj.next();
console.log(result);

result = genObj.next();
console.log(result);
```





#### next()

- 함수는 호출될 때 마다 변수에 초깃값 설정
- 제너레이터는 제너레이터 오브젝트를 생성할 때 설정하고
  - next()로 실행할 때마다 초깃값을 설정하지 않음.
  - 변수의 값이 변경되더라도 유지
- yield의 왼쪽 변수를
  - 다음 next()의 파라미터 값을 받는 변수로 사용



#### next() 사례

- [시나리오]
- 금액 계산 함수와 할인 금액 계산 함수가 있음
- 금액 계산 함수는 수량과 단가를 파라미터로 받아
  - 금액을 계산하고 결과를 yield로 반환
- 할인 금액 계싼 함수 호출
  - yield로 반환한 값을 파라미터 값으로 넘겨 줌
  - 할인 금액을 계산하여 반환
- 금액 계산 함수를 호출하면서
  - 할인 금액을 파라미터 값으로 넘겨 줌
  - 합계 금액 결과 반환





```javascript
const getAmount = function*(qty, price) {
    const amount = Math.floor(qty * price);
    const discount = yield amount;
    yield amount - discount;
}
const getDiscount = function(amount){ 
	return amount > 1000 ? amount *0.2 : amount*0.1
}
const amountObj = getAmount(10, 60);
let result = amountObj.next();
console.log(result);

const dcAmount = getDiscount(result.value);
console.log(dcAmount);

result = amountObj.next(dcAmount);
console.log(result);
```





#### yield*

syntax: yield* [[expression]]

- yield* 표현식의 이터러블을 실행한 후





```javascript
function* gen() {
    yield* [10, 20];
}
const genObj = gen();
let result = genObj.next();
console.log(result);

result = genObj.next();
console.log(result);

result = genObj.next(77);
console.log(result);


```









## Class



#### class 특징

- class는 function과 같지만 몇가지 특징이 있다
- 'use strict'를 작성하지 않아도 strict 모드에서 실행
- class안에 메소드 작성
  - getName(){}
  - getName:function(){}형태에서 메소드 이름만 작성
  - 메소드와 메소드 사이에 콤마(,) 작성하지 않음
  - 세미콜론(;) 선택
- prototype에 연결하여 작성하지 않음
  - class안에 getName(){} 형태로 작성하면
  - 엔진이 프로토타입에 연결된 구조로 변환
  - class밖에 프로토타입을 사용하여 연결 가능
- class 로 인스턴스를 생성한후
  - 프로토타입에 메소드 추가하면
  - 모든 인스턴스에서 공유
- 윈도우 오브젝트에 설정되지 않음



#### constructor

- class로 인스턴스 생성 및 인스턴스 초기화
- 디폴트 constructor
  - class에  constructor를 작성하지 않으면 디폴트 생성자 호출
  - 엔진이 class 키워드를만나 class오브젝트를 생성할 때
  - 생성자에서 class전체를 참조하도록 설정
- 생성자에서 인스턴스가 아닌 다른 값 반환 가능
  - number, string을 반환하면 이를 무시하고 인스턴스 반환
  - 오브젝트를 반환 하게 되면 



```javascript
class Member {
    constructor(name){
        this.name = name
    };
    getName(){
        return this.name
    }
}
const memberObj = new Member('sports');
const result = memberObj.getName();
console.log(result) // sports

```



- 생성자에서 Number, String을 리턴하면 이를 무시하고 생성한 인스턴스 반환
- 생성자에서 Object를 리턴하면 Object가 반환된다



#### getter

```javascript
class Member {
    constructor(name){
        this.name = name
    };
    get getName(){
        return this.name
    }
}

const memberObj = new Member('sports');
const result = memberObj.getName;  // 함수 호출이 아닌 getter 호출
console.log(result) // sports
```



#### setter

```javascript
class Member {
    constructor(name){
        this.name = name
    };
    set setName(name){
        this.name = name
    };
    get getName(){
        return this.name
    };
}

const memberObj = new Member();
memberObj.setName = '축구';
const result = memberObj.getName;  // 함수 호출이 아닌 getter 호출
console.log(result) // 축구
```





#### 상속

- 상속은 객체지향 기능중에 하나
- 다른 클래스를 상속받아 메소드와 프로퍼티 사용
- 상속받을 클래스
  - 부모 클래스, 슈퍼클래스라고 부름
  - 강좌에서는 슈퍼클래스로 사용
  - ES6는 super 키워드로 슈퍼클래스 지칭
- 상속받는 클래스
  - 자식 클래스, sub클래스 등으로 부름
  - 강좌에서는 서브클래스로 부름
  - 슈퍼와 서브의 시맨틱을 맞추기 위한 것

