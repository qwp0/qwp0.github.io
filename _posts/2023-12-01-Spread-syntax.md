---
title: "[Deep Dive] 스프레드 문법"
categories: Js
published: true
typora-root-url: ../
author_profile: true
sidebar_main: true
---

ES6에서 도입된 스프레드 문법(전개 문법) ...은 하나로 뭉쳐 있는 여러 값들의 집합을 펼쳐서 개별적인 값들의 목록으로 만든다.
스프레드 문법을 사용할 수 있는 대상은 Array, String, Map, Set, DOM 컬렉션, arguments와 같이 for...of 문으로 순회할 수 있는 이터러블에 한정된다.

```javascript
// ...[1, 2, 3]은 [1, 2, 3]을 개별 요소로 분리한다(→ 1, 2, 3)
console.log(...[1, 2, 3]); // 1 2 3

// 문자열은 이터러블이다.
console.log(..."Hello"); // H e l l o

// Map과 Set은 이터러블이다.
console.log(
  ...new Map([
    ["a", "1"],
    ["b", "2"],
  ])
); // [ 'a', '1' ] [ 'b', '2' ]
console.log(...new Set([1, 2, 3])); // 1 2 3

// 이터러블이 아닌 일반 객체는 스프레드 문법의 대상이 될 수 없다.
console.log(...{ a: 1, b: 2 });
// TypeError: Found non-callable @@iterator
```

위 예제에서 펼쳐서 만들어진 1 2 3은 값들의 목록이다. 값이 아니라 목록이기 때문에 변수에 할당할 수 없다.

이처럼 스프레드 문법의 결과물은 값으로 사용할 수 없고, 다음과 같이 쉼표로 구분한 값의 목록을 사용하는 문맥에서만 사용할 수 있다.

- 함수 호출문의 인수 목록
- 배열 리터럴의 요소 목록
- 객체 리터럴의 프로퍼티 목록

## 함수 호출문의 인수 목록에서 사용하는 경우

```javascript
const arr = [1, 2, 3];

// 배열 arr을 1, 2, 3으로 펼쳐서 Math.max에 전달한다.
// Math.max(...[1, 2, 3])은 Math.max(1, 2, 3)과 같다.
const max = Math.max(...arr); // -> 3
```

스프레드 문법은 앞에서 살펴본 Rest 파라미터와 형태가 동일하여 혼동할 수 있으므로 주의할 필요가 있다.

Rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 받기 위해 사용된다.
스프레드 문법은 여러 개의 값이 하나로 뭉쳐 있는 배열과 같은 이터러블을 펼쳐서 개별적인 값들의 목록을 받는 것이다. 따라서 서로 반대의 개념이다.

```javascript
// Rest 파라미터는 인수들의 목록을 배열로 전달받는다.
function foo(...rest) {
  console.log(rest); // 1, 2, 3 -> [ 1, 2, 3 ]
}

// 스프레드 문법은 배열과 같은 이터러블을 펼쳐서 개별적인 값들의 목록을 만든다.
// [1, 2, 3] -> 1, 2, 3
foo(...[1, 2, 3]);
```

## 배열 리터럴 내부에서 사용하는 경우

- 2개의 배열을 1개로 합치는 경우

  ```javascript
  // ES6
  const arr = [...[1, 2], ...[3, 4]];
  console.log(arr); // [1, 2, 3, 4]

  // ES5에서는 concat 메서드 활용
  var arr = [1, 2].concat([3, 4]);
  console.log(arr); // [1, 2, 3, 4]
  ```

- 배열 복사하는 경우

  ```javascript
  // ES6
  const origin = [1, 2];
  const copy = [...origin];

  console.log(copy); // [1, 2]
  console.log(copy === origin); // false

  // ES5 에서는 slice 메서드 활용
  var origin = [1, 2];
  var copy = origin.slice();

  console.log(copy); // [1, 2]
  console.log(copy === origin); // false
  ```

## 객체 리터럴 내부에서 사용하는 경우

스프레드 프로퍼티를 사용하면 객체 리터럴의 프로퍼티 목록에서도 스프레드 문법을 사용할 수 있다.
스프레드 문법의 대상은 이터러블이어야 하지만 스프레드 프로퍼티 제안은 일반 객체를 대상으로도 스프레드 문법의 사용을 허용한다.

```javascript
// 스프레드 프로퍼티
// 객체 복사(얕은 복사)
const obj = { x: 1, y: 2 };
const copy = { ...obj };
console.log(copy); // { x: 1, y: 2 }
console.log(obj === copy); // false

// 객체 병합
const merged = { x: 1, y: 2, ...{ a: 3, b: 4 } };
console.log(merged); // { x: 1, y: 2, a: 3, b: 4 }
```

스프레드 프로퍼티가 제안되기 이전에는 ES6에서 도입된 Object.assign 메서드를 사용하여 여러 개의 객체를 병합하거나 특정 프로퍼티를 변경 또는 추가했다.

```javascript
// 객체 병합. 프로퍼티가 중복되는 경우, 뒤에 위치한 프로퍼티가 우선권을 갖는다.
const merged = Object.assign({}, { x: 1, y: 2 }, { y: 10, z: 3 });
console.log(merged); // { x: 1, y: 10, z: 3 }

// 특정 프로퍼티 변경
const changed = Object.assign({}, { x: 1, y: 2 }, { y: 100 });
console.log(changed); // { x: 1, y: 100 }

// 프로퍼티 추가
const added = Object.assign({}, { x: 1, y: 2 }, { z: 0 });
console.log(added); // { x: 1, y: 2, z: 0 }
```

스프레드 프로퍼티는 Object.assign 메서드를 대체할 수 있는 간편한 문법이다.

```javascript
// 객체 병합. 프로퍼티가 중복되는 경우, 뒤에 위치한 프로퍼티가 우선권을 갖는다.
const merged = { ...{ x: 1, y: 2 }, ...{ y: 10, z: 3 } };
console.log(merged); // { x: 1, y: 10, z: 3 }

// 특정 프로퍼티 변경
const changed = { ...{ x: 1, y: 2 }, y: 100 };
// changed = { ...{ x: 1, y: 2 }, ...{ y: 100 } }
console.log(changed); // { x: 1, y: 100 }

// 프로퍼티 추가
const added = { ...{ x: 1, y: 2 }, z: 0 };
// added = { ...{ x: 1, y: 2 }, ...{ z: 0 } }
console.log(added); // { x: 1, y: 2, z: 0 }
```

출처 : 이웅모, 『모던 자바스크립트 deep dive』, 위키북스
