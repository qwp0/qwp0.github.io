---
title: "[Deep Dive] ES6 함수의 추가 기능"
categories: Js
published: true
typora-root-url: ../
author_profile: true
sidebar_main: true
---

## 함수의 구분

ES6 이전의 함수는 동일한 함수라도 다양한 형태로 호출할 수 있다.

```javascript
var foo = function () {
  return 1;
};

//일반적인 함수로서 호출
foo(); // -> 1

//생성자 함수로서 호출
new foo(); // foo{}

//메서드로서 호출
var obj = { foo: foo };
obj.foo(); // -> 1
```

죽, ES6 이전의 모든 함수는 일반 함수로서 호출(callable)할 수 는 것은 물론 생성자 함수로서 호출(constructor)할 수 있다.

여기서 주의해야 할 것은 객체에 바인딩된 함수도 callable이며 constructor로 일반 함수로서 호출할 수 있으며 생성자 함수로서도 호출할 수 있다는 것이다.

```javascript
//프로퍼티 f에 바인딩된 함수는 callable이며 constructor이다.
var obj = {
  x: 10,
  f: function () {
    return this.x;
  },
};

//프로퍼티 f에 바인딩된 함수를 메서드로서 호출
console.log(obj.f()); // 10

//프로퍼티 f에 바인딩된 함수를 일반 함수로서 호출
var bar = obj.f;
console.log(bar()); // undefined

//프로퍼티 f에 바인딩된 함수를 생성자 함수로서 호출
console.log(new obj.f()); // f{}
```

이는 객체에 바인딩된 함수가 prototype 프로퍼티를 가지며 프로토타입 객체도 생성한다는 것을 의미하기 때문에 성능 면에서 문제가 있다.

콜백 함수 역시 constructor이기 때문에 불필요한 프로토타입 객체를 생성한다.

```javascript
// 콜백 함수를 사용하는 고차 함수 map, 콜백 함수도 constructor이며 프로토타입을 생성한다.
[1, 2, 3].map(function (item) {
  return item * 2;
}); // -> [2,4,6]
```

이처럼 ES6 이전의 함수들은 호출 방식에 제약이 없고 생성자 함수로 호출되지 않아도 프로토타입 객체를 생성한다. 이는 실수를 유발할 수도 있고 성능에도 좋지 않다.

ES6에서는 함수를 사용 목적에 따라 세 가지 종류로 명확히 구분했다.

|  ES6 함수의 구분   | constructor | prototype | super | arguments |
| :----------------: | :---------: | :-------: | :---: | :-------: |
| 일반 함수(Normal)  |      O      |     O     |   X   |     O     |
|   메서드(Method)   |      X      |     X     |   O   |     O     |
| 화살표 함수(Arrow) |      X      |     X     |   X   |     X     |

일반 함수는 함수 선언문이나 함수 표현식으로 정의한 함수를 말하며 ES6 이전의 함수와 차이가 없다.
하지만 ES6 메서드와 화살표 함수는 ES6 이전의 함수와 명확한 차이가 있다.

## 메서드

> ES6 사양에서 메서드는 메서드 축약 표현으로 정의된 함수만을 의미한다.

```javascript
const obj = {
  x:1,
  // foo는 메서드다.
  foo(){ return this.x;}.
  // bar에 바인딩된 함수는 메서드가 아닌 일반 함수다.
  bar: function(){return this.x;}
};
```

ES6 사양에서 정의한 메서드는 인스턴스를 생성할 수 없는 non-constructor이므로 생성자 함수로서 호출할 수 없다.

```javascript
new.obj.foo(); // -> TypeError
new.obj.bar(); // -> bar{}
```

ES6 메서드는 인스턴스를 생성할 수 없으므로 prototype 프로퍼티가 없고 프로토타입도 생성하지 않는다.

```javascript
obj.foo.hasOwnProperty("prototype"); // -> false
obj.bar.hasOwnProperty("prototype"); // -> true
```

ES6 메서드는 자신을 바인딩한 객체를 가리키는 내부 슬롯[[HomeObject]]를 갖기 때문에 super 키워드를 사용할 수 있다.

```javascript
const base = {
  name: "Lee",
  sayHi() {
    return `Hi! ${this.name}`;
  },
};

const derived = {
  __proto__: base,
  // sayHi는 ES6 메서드다. ES6 메서드는 [[HomeObjec]]를 갖는다.
  // sayHi의 [[HomeObject]]는 sayHi가 바인딩된 객체인 derived를 가리키고
  // super는 sayHi의 [[HomeObject]]의 프로토타입인 base를 가리킨다.
  sayHi() {
    return `${super.sayHi()}. how are you doing?`;
  },
};
console.log(derived.sayHi()); // Hi! Lee. how are you doing?
```

## 화살표 함수

> 화살표 함수는 function 키워드 대신 화살표를 사용하여 기존의 함수 정의 방식보다 간략하게 함수를 정의할 수 있다.

화살표 함수는 내부 동작도 기존의 함수보다 간략해서 콜백 함수 내부에서 this가 전역 객체를 가리키는 문제를 해결하기 위한 대안으로 유용한데 이는 뒤에서 살펴보자.

### 화살표 함수 정의

- 함수 정의

  함수 선언문이 아닌 함수 표현식으로 정의해야 한다.

  ```javascript
  const multiply = (x, y) => x + y;
  multiply(2, 3); // -> 6
  ```

- 매개변수 선언

  매개변수가 여러 개인 경우 소괄호 () 안에 매개변수를 선언한다.

  ```javascript
  const arrow = (x, y) =>{ ... };
  ```

  매개변수가 한 개 인 경우 소괄호 ()를 생략할 수 있다.

  ```javascript
  const arrow = x =>{ ... };
  ```

  매개변수가 없는 경우 소괄호 ()를 생략할 수 없다.

  ```javascript
  const arrow = () =>{ ... };
  ```

- 함수 몸체 정의

함수 몸체가 하나의 문으로 구성된다면 함수 몸체를 감싸는 중괄호 {}를 생략할 수 있다. 또한 이때 함수 내부의 문이 값으로 평가될 수 있는 표현식인 문이라면 암묵적으로 반환된다.

함수 몸체를 감싸는 중괄호를 생략한 경우 함수 몸체 내부의 문이 표현식이 아닌 문이라면 에러가 발생한다.
따라서 함수 몸체가 하나의 문으로 구성된다 해도 표현식이 아닌 문이라면 중괄호를 생략해야 한다.

```javascript
const power = x => x**2;
power(2);  // -> 4

// 함수 몸체 내부의 문이 표현식이 아닌 문
const arrow = () => const x = 1; // SyntaxError
// 다음과 같이 정의해야 함
const arrow = () => { const x =1};
```

객체 리터럴을 반환하는 경우 객체 리터럴을 소괄호로 감싸 주어야 한다.

```javascript
const create = (id, content) => ({ id, content });

// 위 표현은 다음과 동일하다.
const create = (id, content) => {
  return { id, content };
};
```

함수 몸체가 여러 개의 문으로 구성된다면 중괄호를 생략할 수 없다.

화살표 함수도 일급 객체이므로 고차 함수에 인수로 전달할 수 있다.

```javascript
// ES5
[1, 2, 3].map(function (v) {
  return v * 2;
});
// ES6
[1, 2, 3].map((v) => v * 2);
```

이처럼 화살표 함수는 콜백 함수로서 정의할 때 유용하다.

### 화살표 함수와 일반 함수의 차이

1. 화살표 함수는 인스턴스를 생성할 수 없는 non-constructor이다.

   화살표 함수는 인스턴스를 생성할 수 없으므오 prototype 프로퍼티가 없고 프로토타입도 생성하지 않는다.

2. 중복된 매개변수 이름을 선언할 수 없다.

   일반 함수는 중복된 매개변수 이름을 선언해도 에러가 발생하지 않는다.

   ```javascript
   function normal(a, a) {
     return a + a;
   }
   ```

   화살표 함수에서는 중복된 매개변수 이름을 선언하면 에러가 발생한다.

   ```javascript
   const arrow = (a, a) => a + a; // SyntaxError
   ```

3. 화살표 함수는 함수 자체의 this, arguments, super, new.target 바인딩을 갖지 않는다.

   따라서 화살표 함수 내부에서 this,arguments,super,new.target을 참조하면 스코프 체인을 통해 상위 스코프의 this, arguments,super,new.target을 참조한다.

### this

화살표 함수가 일반 함수와 구별되는 가장 큰 특징은 this이다. ES6 이전에 콜백 함수 내부의 this가 외부 함수의 this와 다르기 때문에 발생하는 문제를 화살표 함수로 해결할 수 있다.

```javascript
class Prefixer {
  constructor(prefix) {
    // 1
    this.prefix = prefix;
  }
  add(arr) {
    return arr.map(function (item) {
      return this.prefix + item; // 2
    });
  }
}
const prefixer = new Prefixer("-webkit-");
console.log(prefixer.add(["transition", "user-select"]));
```

위 예제에서 프로토타입 메서드 내부인 1에서 this는 메서드를 호출한 객체를 가리킨다. 하지만 콜백함수 내부인 2에서 this는 메서드가 콜백 함수를 일반 함수로 호출하기 때문에 undefined를 가리킨다. 둘이 서로 다른 값을 가리키고 있기 때문에 TypeError가 나는 콜백 함수 내부의 this 문제가 발생한다.

ES6에서 화살표 함수를 이용해서 해결할 수 있다.

```javascript
class Prefixer {
  constructor(prefix) {
    this.prefix = prefix;
  }
  add(arr) {
    return arr.map((item) => this.prefix + item);
  }
}
const prefixer = new Prefixer("-webkit-");
console.log(prefixer.add(["transition", "user-select"]));
```

화살표 함수는 함수 자체의 this 바인딩을 갖지 않기에 화살표 함수 내부에서 this를 참조하면 상위 스코프의 this를 그대로 참조한다. 이를 lexical this라고 한다.

### super

화살표 함수는 함수 자체의 super 바인딩을 갖지 않는다. 화살표 함수 내부에서 super를 참조하면 this와 마찬가지로 상위 스코프의 super를 참조한다.

### arguments

화살표 함수는 함수 자체의 arguments 바인딩을 갖지 않는다. 화살표 함수 내부에서 arguments를 참조하면 this와 마찬가지로 상위 스코프의 arguments를 참조한다.

argments 객체는 함수를 정의할 때 매개변수의 개수를 확정할 수 없는 가변 인자 함수를 구현할 때 유용하다.
하지만 화살표 함수는 함수 자체의 arguments 객체를 갖지 않는다.

따라서 화살표 함수로 가변 인자 함수를 구현해야 할 때는 Rest 파라미터를 사용해야 한다.

## Rest 파라미터

매개변수 이름 앞에 세 개의 점을 붙여서 정의한 매개변수로 함수에 전달된 인수들의 목록을 배열로 전달받는다.

```javascript
function sum(...args) {
  return args.reduce((pre, cur) => pre + cur, 0);
}
console.log(sun(1, 2, 3, 4, 5)); // 15
```

출처 : 이웅모, 『모던 자바스크립트 deep dive』, 위키북스
