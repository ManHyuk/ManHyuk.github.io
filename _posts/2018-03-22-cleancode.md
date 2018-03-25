---
layout: post
title: "CleanCode for JavaScript"
comments: false
description: ""
keywords: ""
---



클린코드 for 자바스크립트 ( 변수, 함수 )



**이 글은 단순히 스타일 가이드가 아니라 자바스크립트로 코드를 작성할때 읽기 쉽고, 재사용 가능하며 리팩토링 가능하게끔 작성하도록 돕기 위한 글이다.**


모든 원칙이 엄격히 지켜져야하는 것은 아니다. 이것들은 **지침** 일뿐이다.

## 변수(Variables)


- 의미있고 발음하기 쉬운 변수 이름을 사용하자

```javascript
// BAD
const yyyymmdstr = moment().format('YYYY/MMDD');

// GOOD
const currentData = moment().format('YYYY/MMDD');
```







- 검색가능한 이름을 사용하자
  - 작성할 코드보다 읽을 코드가 더 많기 때문에 읽기 쉽고 검색가능하게 작성해야한다.

```javascript
// BAD
setTimeout(blastOff, 86400000); // 86400000의 의미를 알 수 없다.

// GOOD
const MILLISECONDS_IN_A_DAY = 86400000; // const 변수를를 대문자로 선언
setTimeout(blastOff, MILLISECONDS_IN_A_DAY);
// 매직넘버 관리!!!
```








- 의도를 나타내는 변수를 사용하자
  - 의도는 알겠는데 코드는 모르겠다

```javascript
// BAD
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(address.match(cityZipCodeRegex)[1], address.match(cityZipCodeRegex)[2]);

// GOOD
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```



- 자신만 알아볼 수 있는 작명을 피하자
  - 명시적인 것이 암시적인 것보다 좋다.

```javascript
// BAD
const loacations = ['seoul', 'suwon', 'busan'];
locations.forEach((l) => { // l의 의미를 알지 못한다
  doStuff();
  doSomeOtherStuff();

  dispatch(l);
})

// GOOD
const locations = ['seoul', 'suwon', 'busan'];
locations.forEach(location => { // locations을 location으로 변경
  doStuff();
  doSomeOtherStuff();

  dispatch(location);
});
```







- 문맥상 필요 없는 것들을 쓰지 말자

```javascript
// BAD
const car = {
  carColor: 'blue'
}
function paintCar(car){
  car.carColor = 'red';
}

// GOOD
const car = {
  color: 'blue'
}
function paintCar(car){
  car.color = 'red';
}
```





## 함수 (Functions)



#### 함수 인자는 2개 이하가 인상적이다.



매개변수의 개수를 제한하는것은 테스팅을 쉽게 만들어 주기 때문에 중요하다.

**1개나 2개의 인자** 를 가지고 있는 것이 가장 이상적인 케이스이고 3개의 인자는 가능한 피해야한다.

만약 인자가 더 많아진다면 통합되어야 한다.

인자가 많다는 것은 함수가 너무 많은 역할을 하고 있다는 것.




- 함수가 기대하는 속성을 좀더 명확히 하기 위해서 es6의 비구조화(destructuring) 구문을 사용할 수 있다.
  1. 어떤 사람이 그 함수의 시그니쳐(인자의 타입, 반환되는 값의 타입 등)를 볼 때 어떤 속성이 사용되는지 즉시 알 수 있다.
  2. 비구조화는 함수에 전달된 인수 객체의 지정된 기본타입 값을 복제하며 이는 사이드이펙트가 일어나는 것을 방지한다. 참고로 인수 객체로부터 비구조화된 객체와 배열은 복제되지 않는다.
  3. Linter를 사용하면 사용하지않는 인자에 대해 경고해주거나 비구조화 없이 코드를 짤 수 없게 할 수 있다.

```javascript
// BAD
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

// GOOD
function createMenu({title, body, buttonText, cancellable}){
  // ...
}
createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
});
```







- 함수는 하나의 행동만 해야 한다.

함수가 1개 이상의 행동을 한다면 작성, 테스트, 이해 모두 어려워 진다. 함수에 하나의 행동을 정의하게 된다면 함수는 좀 더 고치기 쉬워지고 읽기 쉬워진다.

```javascript
// BAD
function emailClients(clients) {
  clients.forEach(client => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) { // 함수안에 두개의 행동
      email(client);
    }
  });
}

// GOOD
// 행동을 분리함
function emailClients(clients) {
  clients
    .filter(isClientActive)
    .forEach(email);
}

function isClientActive(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```





- 함수명은 함수가 무엇을 하는지 알 수 있어야 한다,



- 함수는 단일 행동을 추상화 해야한다.
추상화된 이름이 여러 의미를 내포하고 있다면, 그 함수는 너무 많은 일을 하게 설계된것이다.

```javascript
// BAD
function parseBetterJSAlternative(code) { // 함수명이 여러 의미를 내포하고 있다.
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach(token => {
    // lex...
  });

  ast.forEach(node => {
    // parse...
  });
}
// GOOD
function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      tokens.push( /* ... */ );
    });
  });

  return tokens;
}

function lexer(tokens) {
  const ast = [];
  tokens.forEach(token => {
    ast.push( /* ... */ );
  });

  return ast;
}

function parseBetterJSAlternative(code) { // 여러 의미를 내포하는 함수내부의 행동들을 나눔
  const tokens = tokenize(code);
  const ast = lexer(tokens);
  ast.forEach(node => {
    // parse...
  });
}
```







- 중복된 코드를 작성하지 마라.
  - 중복된 코드가 있다는 것은 어떤 로직을 수정해야 할 일이 생겼을 때 수정 해야할 코드가 한 곳 이상이라는 것을 뜻한다.



```javascript
// BAD
function showDeveloperList(developers) {
  developers.forEach(developers => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(manager => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}

// GOOD
function showEmployeeList(employees) {
  employees.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    let portfolio = employee.getGithubLink();

    if (employee.type === 'manager') {
      portfolio = employee.getMBAProjects();
    }

    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```





- 매개변수로 플래그를 사용하지 마라.
  - 플래그를 사용하는 것 자체가 그 함수가 한가지 이상의 역할을 하고 있다는 것을 뜻한다.
  - Boolean 기반으로 함수가 실행되는 코드가 나뉜다면 함수를 분리해라.

```javascript
// BAD
function createFile(name, temp){
    if (temp) {
        fs.create(`./temp/${name}`);
    } else {
        fs.create(name);
    }
}

// GOOD
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```







#### 사이드 이펙트를 피해라 - 1

- 함수는 값을 받아서 어떤 일을 하거나 값을 리턴할때 사이드이펙트를 만들어낸다. 
- 어떠한 구조체도 없이 객체 사이의 상태를 공유하거나, 같은 사이드이펙트를 만들어 내는 함수를 여러개 만들면 안된다.



```javascript
// BAD
// 아래 함수에 의해 참조되는 전역 변수다.
// 이 전역 변수를 사용하는 또 하나의 함수가 있다고 생각해자. 이제 이 변수는 배열이 될 것이고, 프로그램을 망가뜨리게 된다.
let name = 'Ryan McDermott';

function splitIntoFirstAndLastName() {
  name = name.split(' ');
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];


// GOOD
function splitIntoFirstAndLastName(name) {
  return name.split(' ');
}

const name = 'Ryan McDermott';
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```





#### 사이드 이펙트를 피해라 - 2

1. 실제로 입력된 객체를 수정하고 싶은 경우가 있을 수 있지만 예제를 보고 적용해보면 그런 경우는 거의 없다는 것을 알 수 있다.
2. 큰 객체를 복제하는것은 성능 측면에서 매우 비싼 코스트다. 하지만 큰 문제가 되지는 않는다. 왜냐하면 좋은라이브러리를 사용하면 해결 할 수 있다. 이는 개체와 배열을 수동으로 복제하는 것처럼 메모리 집약적이지 않게 하고, 빠르게 복제해준다.



```javascript
// BAD
const addItemToCart = (cart, item) => {
    car.push({item, data:Date.now()});
};

// GOOD
const addItemToCart = (cart, item) => {
    return [...cart, {item, date : Date.now()}]
};
```





- 전역 함수를 사용하지 마라.
  - 전역 환경을 사용하는 것은 JavaScript에서 나쁜 관행이다. 왜냐하면 다른 라이브러리들과 충돌이 날 수 있고, 당신의 API를 쓰는 유저들은 운영환경에서 예외가 발생하기 전까지 문제를 인지 못하기 때문이다.



Array메소드를 확장하여 두 배열간의 차이를 보여주는 diff 메소드를 사용하려면 새로운 함수를 Array.prototype에 쓸 수도 있지만, 똑같은 일을 시도한 다른 라이브러이와 충돌 할 수 있다.

ES6을 사용하여 전역 Array를 상속 받는게 더 나은 방법이다.



```javascript
// BAD
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};

// GOOD
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```





- 명령형 프로그래밍보다 함수형 프로그래밍을 지향해라.

  - 함수형 언어는 더 깔끔하고 테스트하기 쉽다.

  ​

```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

// BAD
let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}

// GOOD
const totalOutput = programmerOutput
  .map(programmer => programmer.linesOfCode)
  .reduce((acc, linesOfCode) => acc + linesOfCode, INITIAL_VALUE);
```



- 조건문을 캡슐화 하자.

```javascript
// BAD
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}

// GOOD
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```



- 부정조건문을 사용하지 마라.



```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

// BAD
if (!isDOMNodeNotPresent(node)) {
  // ...
}

// GOOD
if (isDOMNodePresent(node)) {
  // ...
}
```





- 조건문 작성을 피해라.
  - 함수나 클래스에 if문을 쓴다면 그것은 그 함수나 클래스가 한가지 이상의 일을 수행하고 있다는것과 같다.



```javascript
// BAD
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount();
      case 'Air Force One':
        return this.getMaxAltitude();
      case 'Cessna':
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}

// GOOD
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```





- 타입-체킹을 피해라 - 1
  - 자바스크립트는 타입이 정해져있지 않다. 함수가 어떤 타입의 인자든 받을 수 있다는 것을 의미한다.
  - 때문에 함수에 타입-체킹을 시도 할 수 있다. 타입 체킹에는 많은 방법들이 존재한다.
  - 첫번째 방법은 일관성 있는 API를 사용하는 것 입니다.

```javascript
// BAD
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location('texas'));
  }
}

// GOOD
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location('texas'));
}
```



- 타입-체킹을 피해라 - 2
  - 기본 자료형을 사용하고 다형성을 사용할 수 없을때에도 여전히 타입-체킹이 필요하다고 느껴지면 타입스크립트를 사용해라



```javascript
// BAD
function combine(val1, val2) {
  if (typeof val1 === 'number' && typeof val2 === 'number' ||
      typeof val1 === 'string' && typeof val2 === 'string') {
    return val1 + val2;
  }
  
  throw new Error('Must be of type String or Number');
}

// GOOD
function combine(val1, val2) {
  return val1 + val2;
}
```



- 과도한 최적화를 지양해라.
  - 최신 브라우저들은 런타임시에 많은 최적화 작업을 수행한다. 때문에 대부분 코드 최적화에 들이는 시간들이 낭비가 될 수 있다.
  - 최적화가 부족한 곳을 알려주는 레퍼런스를 찾아 부족한 부분만 최적화 하자

```javascript
// 오래된 브라우저의 경우 캐시되지 않은 `list.length`를 통한 반복문은 높은 코스트를 가졌습니다.
// 그 이유는 `list.length`를 매번 계산해야만 했기 때문인데, 최신 브라우저에서는 이것이 최적화 되었습니다.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```





- 죽은 코드를 지워라
  - 죽은 코드는 중복된 코드 만큼이나 좋지 않다.
  - 호출되지 않는 코드가 있다면 지워라.
  - 코드가 필요하면 버전 히스토리에서 찾아라



```javascript
// BAD
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

// GOOD
function newRequestModule(url) {
  // ...
}


const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
```







