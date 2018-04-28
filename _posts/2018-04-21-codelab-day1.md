---
layout: post
title: "코드랩 day1"
comments: false
description: ""
keywords: ""
---




- property key, name 차이
  - name: String value
  - key: String value + Symbol value







##  let, const

- 구분 목적
  - 스코프 검색
  - 자신의 영역에서 사용
  - 공용에 따른 문제 방지



- 글로벌 오브젝트도 스코프
- var one
  - var 키워드로 변수 선언이 정상
- var 키워드를 작성하지 않으면
  - 글로벌 변수로 간주
  - 객체지향 관점에서 보면 이상한 모습



#### use strict 사용

- 함수 안에서 글로벌 변수 선언 방지
  - 근본적인 접근은 아님
- ES6
  - use strict가 디폴트
  - 전체는 아님
- node.js에서는 작성



#### let 변수

- var 문제 해결
- let sports = '스포츠'
  - 스코프를 가진 변수 선언
- 스코프 범위
  - 블록, 문장, 표현식
- 블록 안의 블록도 스코프
  - 블록 밖의 변수는 스코프가 다름
  - 값이 대체 되지 않음
- let 변수는 호이스팅 되지 않음



#### let 작성

- Syntax: name1[=value1]1[,name2=[value2]]

  - name1, name2에 변수 이름 작성
    - 식별자로 사용
  - value1, value2에 초깃값 작성
    - 표현식 작성 가능, 평과 결과 사용
    - 값을 할당하지 않고 변수 선언만 가능
    - 값을 할당하지않고 선언만 한다면  초깃값으로 undefined가 할당
  - Temporal dead zone
    - 같은 스코프에 같은 이름 선언 불가

  ​

#### 블록 스코프

- let 사용의 가장 큰 목적
- 블록 기준
  - function () { 코드 }
  - if (a === 1) { 코드 }



#### let과 this

- 글로벌 오브젝트에서
  - let 변수를 this로 참조 불가
- code

```javascript
var music = '음악';
console.log(this.music); // 음악

let sports = '축구';
console.log(sports) // 축구
console.log(this.sports) // undefined
```

- 글로벌 오브젝트의 let 변수를 this로 액세스
  - undefined 출력



#### function

- function도 블록스코프
- function 안과 밖에
  - 같은 이름의 let 변수 선언 가능 -> 스코프가 다르기 때문
- code

```javascript
let sports = '축구';
let music = '재즈';

function get() {
    let music = '클래식';
    console.log('music', music);
    console.log('sports', sports);
    console.log('this', this.music);
};

window.get();
get();

// 결과
// 음악 클래식
// 스포츠 축구
// this undefined
// 음악 클래식
// 스포츠 축구
```

- window.get() 형태로 호출하면 this가 window오브젝트참조
  - music 변수가 widnow에 없음 따라서 undefined 출력



- ES6에서 get() 앞에 오브젝트를 작성하지 않고 get()형태로 호출하면
  - this로 참조하는 오브젝트가 undefined로 설정됨 (es5이전에는 글로벌로 설정)
  - 즉 undefined.get()형태가 되어 에러 발생





#### try-catch

- 블록 스코프 try, catch 문까지 하나의 블록 (try 하나, catch 하나. 총 두개 아님)



#### switch-case

- switch문이 블록 스코프이므로 case에 이름이 같은 let 변수 작성 불가
- code

```javascript
switch(count) {
    case 1:
        let music;
        break;
    case 2:
        let music; // Error
    default:
        console.log(music);
}
```



#### 호이스팅

- let 변수는 호이스팅 되지 않음



```javascript
window.onlad = function() {
    console.log(sports);
    var sports = '스포츠';

    console.log(music);
    let music = '음악';
}
```

- 아래의 var sports에서 값을 설정하므로 이 위치에서 sports 값은 undefined
- sports를 인식하지 못하면 에러 발생, 에러가 발생하지 않고 undefined 출력
- 이러한 순환이 호이스팅



- console.log(music)을 실행하고 내려와서 sports에 스포츠 할당





#### const

Syntax name1 [=value1], [name2[=value2]]



- name1에 변수 이름 작성, 식별자로 사용
- value1, value2에 초기값 작성
  - 반드시 값 작성, 변수 선언만 불가
  - 표현식 작성 가능, 평가 결과 사용
- 값을 바꿀 수 없는 변수 선언
  - const가 상수 개념이지만 차이 있다
- code

```javascript
const sports = '축구';
try {
    sports = '농구';
} catch (err) {
    console.log('재할당 불가1');
}

const obj = { name: 'mh' };
try{
    obj = {}
} catch (err) {
    console.log('재할당 불가2');
}
obj.name = '만혁';
console.log(obj.name);

// 결과
// 재할당 불가1
// 재할당 불가2
// 만혁
```





## Arrow Function

- Arrow 사전적 의미
  - 화살, 화살표 =>
- 코드 형태
  - (param) => (함수 코드)
- 스코프가 window



#### Syntax

- (param) => (코드)
  - 파라미터가 하나 일 때
- param => (코드)
  - 파라미터가 하나일 때, 소괄호 생략 가능
- (p1, p2, ...rest) => (코드)
  - rest 파라미터
- (p1, p2 = 123) => (코드);
  - 디폴트 파라미터 값 사용
- ([one, two] = [10, 20]) => ( 코드 );
  - Array 디스트럭처링
- ({key: sum } = {key: 10 + 20}) => ( 코드 );
  - Object 디스트럭처링
- 줄 바꿈은 화살표가 끝난 다음에!!







```javascript
let get = () => 123;
get();

// get: function()
// 	arguments: { ... }
//	caller: {}
//	length: 0
//	name: 'get
//	__proto__: function()
//		call: function call()
```



- 화살표 함수는 new로 인스턴스 생성 불가
- 왼쪽의 화살표 함수 구조는 크롬 부라우저에서 전개한 것
- es5의 함수는 proto가 있다, 하지만 화살표함수는 proto가 없다(function임)
- 그 결과, 함수 자체의 기능만 할 수 있도록 만든게 아닐까 싶다



#### arrow와 arguments

- arrow 함수에서 arguments 사용 불가
  - rest 파라미터 사용 권장

```javascript
function book() {
    let music = param => {
        const aa = arguments.length;
        for(var k = 0 ; k<aa; k++) {
            console.log(arguments[k]);
        }
        music('모짜르트', '베토벤');
    }
}
book('자바스크립트');

// 결과
// 자바스크립트
```





#### arrow 와 this



```javascript
let Sports = function() {
    this.count = 20;
};
Sports.prototype = {
    plus: function() {
        this.count += 1;
    },
    get: function() {
        setTimeOut(function() {
            console.log(this === window);
            console.log(this.plus);
        }, 1000);
    }
}
let sportsObj = new Sports();
sportsOb.get();

// result
// true
// undefined
```





##### arrow 함수 작성

```javascript
let Sports = function() {
    this.count = 20;
};
Sports.prototype = {
    plus: function() {
        this.count += 1;
    },
    get: function() {
        setTimeOut(function() {
            console.log(this === window);
            console.log(this.plus);
        }, 1000);
    },
    arrow: function() {
        setTimeOut( () => {
            this.plus();
            console.log(this.count);
        }, 1000)
    }
}
let sportsObj = new Sports();
sportsOb.arrow();

// result
// 21
```



- sportsObj.arrow()로 호출하므로 this는 sportsOjb 인스턴스 참조
- this.plus() 호출
  - this가 sportsObj 인스턴스를 참조하
  - sportsObj의 plus()가 호출됨
  - this.plus에서 1이 증가된 값 출력





##### prototpye에 arrow 함수 작성

```javascript
Sports.prototype = {
    add: () => {
        this.count +=1 ; // this는 윈도우를 참조하게 됨
    }
}
```



#### Lambda

- Lambda function
  - 함수 이름이 없는 익명/무명함수
  - function(){} 형태
- Lisp 언어가 원조, 1958년



## Iteration



- 이터레이터
  - 반복을 의미
  - 반복하기 위한 프로토콜 필요
- 프로토콜
  - 통신 프로토콜이 연상 됨
  - 약속된 기준과 방법으로 송수신
  - 이를 통신 프로토콜(규약) 이라고 함
  - 즉, 프로토콜은 규약!



- 이터레이션(반복)
  - Array가 연상됨
  - 배열을 반복하기 위해서는 배열이 반복할 수 있는 오브젝트이어야 하고
  - 반복을 위한 함수를 가지고 있어야 한다.
  - 이것이 이터레이션 프로토콜(규약)
- 이터레이션 구성
  - 이터레이블(iterable) 프로토콜 / 반복할 수 있는 형태
  - 이터레이터(iterator) 프로토콜 / 반복자



#### iterable protocol

- 이터러블 프로토콜
  - 오브젝트의 반복 동작을 정의
- 빌트인 오브젝트는
  - 디폴트로 이터러블 프로토콜을 갖고 있기 때문에 반복할 수 있다.
  - String, Array, TypedArray, Map, Set
  - Arguments, DOM NodeList
- 이터러블 오브젝트
  - 이터러블 프로토콜이 설정?
- 이터러블 오브젝트 조건
  - Symbol.iterator()가 있어야함
  - 빌트인 이터러블 오브젝트에는 디폴트로 설정됨
  - 자체 오브젝트에는 없지만 상속 받은 prototype chain에 있어도 된다.
  - 즉, Array를 상속받으면 이터러블 오브젝트가 됨.
- 프로토콜이 규약이므로
  - 개발자 코드로 이터러블 프로토콜 정의 가능



```javascript
let arrayObj = [];
let result = arrayObj[Symbol.iterator];
console.log(result);

let objectObj = {};
let result = objectObj[Symbol.iterator];

// result
// function values() { [native code]}
// undefined
```

1. array가 이터러블 오브젝트이므로 []로 생성한 arrayObj에 Symboliterator가 존재





- 이터레이터 프로토콜은
  - 오브젝트의 값을 순서로 처리할 수 있는 방법
  - 이 방법은 오브젝트에 있는 next()로 구현



```javascript
let arrayObj = [1,2,3];
let iteratorObj = arrayObj[Symbol.iterator]();
```



1. Symbol.iterator()를 호출하면 이터레이터 오브젝트를 생성하여 반환
2. 이터레이션을 하려면 오브젝트가 이터레이터 오브젝트이어야 한다.





#### spread

- syntax: [...iterable]
- 이터러블 오브젝트를 하나씩 전개
- [...iterable]
- let values = [10, 20, 30];
  - [...values]



#### Array spread

- Array spread 작성 형태
  - const two = [21, 22];
  - const five = [51, 52];
  - const one = [11, ...two, 12, ...five];
  - [11, 21, 22, 12, 51, 52]
- 작성한 위치에 배열 엘리먼트를 전개



#### String spread

- [] 안에 대상 문자열 작성

- String spread wㅏㄱ성 형태

  - let str = 'hello'
  - let chars = [...str]
  - ['h', 'e', 'l', 'l', 'o']

  ​



#### push(...spread)

```javascript
let two = [ 21, 22 ];
let str = 'hi'l
two.push(str);

// [21, 22, 'h', 'i'];
```





#### Object spread

- [] 안에 대상 오브젝트 작성
  - const one = [{name1: 11}, {name2: 22}];
  - const two = [{name3: 33}, ...one];





#### function spread

- 호출하는 함수의 파라미터에 spread 대상 작성
  - function get(one, two, three){};
  - const values = [10,20,30];
  - get(...values);
- 함수를 호출하기 전에 엘리먼트 단위로 전개
- 각 엘리먼트 값을 파라미터로 넘겨줌
- 호출받는 함수의 파라미터에 순서로 매핑





#### rest 파라미터

- 호출 받는 function() 파라미터에 ...괕이 점(.) 3개 작성
  - function(one, ...rest){ } 형태
- 하나 이상의 파라미터를 배열로 묶음
- argument가 사라진 이유 중 하나?





#### Array-like

- 배열처럼 이터러블 가능한 오브젝트
  - let one = { 0: value, 1:value, length:2 }
  - 프로퍼티 key 값이 0부터 순서 값
  - 전체 프로퍼티 수를 length로 작성
- Object 타입이지만 for() 문으로 전개 가능





#### rest, arguments 차이

- Argument 오브젝트
  - 파라미터의 작성에 관계없이 전체 설정
  - Array-like 이므로 배열 메소드 사용 불가
  - proto에 Object 첨부
- rest 파라미터





## Operator

#### Destrucutring

- 코드 형태
  - let one, two, three;
  - [one, two, three] = [1, 2, 3];
  - one에 1, two에 2, three에 3 할당
- 사전적 의미: ~ 구조를 파괴하다.
  - 원 데이터는 변경 되지 않음.



#### Object 분할

- Object의 프로퍼티를 분할하여 프로퍼티 이름이 같은 프로퍼티 값으로 할당
- 사용 형태
  - {one: two} = {one :1, two: 2}
  - one에 1 , two에 2 할당





#### Object Operation

- 같은 프로퍼티 key 사용
  - { key:1 , key:2 } // key 이름이 같음
  - es5 strict 모드에서는 이름이 같으면 에러
  - ES6에서는 뒤에 작성된 키값으로 대체됨



- 초기값 설정

  - const one = 1, two = 2;
  - const values = {one, two} // { one :1, two:2 }

  ​

- function 키워드 작성하지 않음

```javascript
const obj = {
    getTotal(param) {
        return param + 123;
    }
}
coonsole.log(obj.getTotal(400));
```





### Descriptor

- es5
- 프로퍼티 디스크립터 타입
  - 속성 이름(ex.enumerable)과 속성 값(true/false)으로 구성
  - 프로퍼티 속성의 처리 기준 정의
- 프로퍼티 디스크립터 타입 분류
  - 데이터 프로퍼티 디스크립터
  - 액세스 프로퍼티 디스크립터





- get 속성

```javascript
var obj = {};
Object.defineProperty(obj, 'book', {
    get: function() {return '책'}
})ㅣ
var result = obj.book;


```

- getter
  - obj.book을 실행하면 obj.book의 get()을 자동으로 호출
  - 그러나 obj.book.get()을 호출하면 에러 발생



- setter









#### assign()



- assign()
  - 두 번째 파라미터의 오브젝트 프로퍼티를 첫 번째 파라미터에 복사하고, 첫 번째 파라미터 반환
  - own property만 복사, [[prototype]]은 복사하지 않음
- 첫 번째 파라미터에 열거 가능한 Object 작성
  - 작성하지 않거나 undefined, null이면 TypeError
  - Boolean, Number, String, Number로 작성하면 인스턴스로 생성하여 사용
- 필요한 이유
  - ㄹ









- 연동 되지 않음

```javascript
const sports = {
    event: 'soccer',
    player: 11
}

const dup ={};
Object.assign(dup, sports);
console.log(dup.player);

dup.player = 33;
console.log(sports.player)

sports.event = 'swim';
console.log(dup.event);

// result
// 11
// 11
// soccer
```





- 연습문제



```javascript
const book = {
    item: {
        title: 'js'
    }
};
const bookCopy = {};
Object.assign(bookCopy, book);

book.item.title = 'book'
console.log(book.item.title) // book

console.log(bookCopy.item.title) // book

// depth가 1이 아니라면 연동됨
```



- 복사할 Object가







#### setPrototypeOf()

- 첫 번째 파라미터의 proto에 두 번째 파라미터 설정
- 첫 번째 파라미터에 오브젝트 또는 인스턴스 작성
  - Object.isExtensible() 결과가 false라면 TypeError
  - Boolean, Number, String, Symbol이면 인스턴스 생성
  - 두 번째 파라미터를 반영하지 않고 생성한 인스턴스 반환
- 두 번째 파라미터에 오브젝트 또는 null 작성
- Es5 에 getPrototypeOf 있음



- proto에 설정하는 목적
-  첫 번째 오브젝트.method() 형태로 호출







```javascript
const Sports = function() {
    this.count = 123;
}
Sports.prototype.getCount = function() {
    return this.count;
}

const protoObj = Object.setPrototypeOf({}, Sports.prototype);
console.log(typeof (protoObj.__proto__));

const result = protoObj.getCount();
console.log(result);

// result
// object
// undefined
```



- 첫번째 파라미터에 빈 오브젝트 작성
- Sports.prototype에 연결된 프로퍼티 인스턴스를 생성하여 빈 Object.proto 에 첨부
- 이 결과를 protoObj에 할당
