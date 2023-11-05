---
title: "[Deep Dive] strict mode"
categories: Js
published: true
typora-root-url: ../
author_profile: true
sidebar_main: true
---

## strict mode란 ?

```javascript
function foo() {
  x = 10;
}
foo();

console.log(x); //10
```

위 예제를 실행하면 먼저 자바스크립트 엔진은 foo함수 스코프에서 x식별자를 검색한다.
foo함수 스코프에 없으면 스코프 체인을 통해 상위 스코프에서 x 변수를 검색한다.
전역 스코프에 없으면 RefferenceError가 발생해야 하는데 자바스크립트 엔진은 암묵적으로 전역 객체에 x프로퍼티를 동적으로 생성한다. 이를 암묵적 전역(implicit global)이라 한다.

이런 현상은 개발자의 의도와 다르게 동작할 우려가 있어 좋지 않다.
따라서 반드시 var, let, const키워드를 사용하여 변수를 선언한 다음 사용해야 한다.

이를 지원하기 위해 strict mode가 추가되었다. strict mode는 **자바스크립트 언어의 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대한 명시적인 에러를 발생시킨다.**

ESLint와 같은 린트 도구를 사용해도 strict mode와 유사한 효과를 얻을 수 있다.
린트 도구는 정적 분석 기능을 통해 소스 코드를 실행하기 전에 소스코드를 스캔하여 문법적 오류 뿐만 아니라 잠재적 오류까지 찾아내 오류의 원인을 리포팅해주는 유용한 도구이다.

참고 : <a href="https://poiemaweb.com/eslint">ESLint 사용하는 방법</a>

## strict mode의 적용

전역의 선두 또는 함수 몸체의 선두에 'use strict';를 추가한다.

전역의 선두에 추가하면 스크립트 전체에 strict mode가 적용된다.

```javascript
"use strict";

function foo() {
  x = 10; //ReferenceError : x is not defined
}
foo();
```

함수 몸체의 선두에 추가하면 해당 함수와 중첩 함수에 strict mode가 적용된다.

```javascript
function foo() {
  "use strict";

  x = 10; //ReferenceError : x is not defined
}
foo();
```

코드의 선두에 'use strict';를 위치시키지 않으면 strict mode가 제대로 동작하지 않는다.

## 전역에 strict mode를 적용하는 것은 피하자

전역에 적용한 script mode는 스크립트 단위로 적용된다. 스크립트 단위로 적용된 strict mode는 다른 스크립트에 영향을 주지 않고 해당 스크립트에 한정되어 적용된다.

strict mode 와 non-strict mode를 혼용해서 사용하면 오류를 발생시킬 수 있다. 특히 외부 서드파티 라이브러리를 사용하는 경우 라이브러리가 non-strict mode인 경우도 있기 때문에 전역에 strict mode를 적용하는 것은 바람직하지 않다.

즉시 실행 함수로 스크립트 전체를 감싸서 스코프를 구분하고 즉시 실행 함수의 선두에 strict mode를 적용한다.

```javascript
// 즉시 실행 함수의 선두에 strict mode 적용
(function () {
  "use strict";

  // Do something...
})();
```

## 함수 단위로 strict mode를 적용하는 것도 피하자

어떤 함수는 strict mode를 적용하고 어떤 함수는 strict mode를 적용하지 않는 것은 바람직하지 않다.
그리고 strict mode가 적용된 함수가 참조할 함수 외부의 컨텍스트에 strict mode를 적용하지 않는다면 문제가 발생할 수 있다.

따라서 strict mode는 즉시 실행 함수로 감싼 스크립트 단위로 적용하는 것이 바람직하다.

## strict mode가 발생시키는 에러

- 암묵적 전역

  선언하지 않은 변수를 참조하면 ReferenceError가 발생한다.

  ```javascript
  (function () {
    "use strict";

    x = 1;
    console.log(x); // ReferenceError: x is not defined
  })();
  ```

- 변수, 함수, 매개변수의 삭제

  delete연산자로 변수, 함수, 매개변수를 삭제하면 SyntaxError가 발생한다.

  ```javascript
  (function () {
    "use strict";

    var x = 1;
    delete x;
    // SyntaxError: Delete of an unqualified identifier in strict mode.

    function foo(a) {
      delete a;
      // SyntaxError: Delete of an unqualified identifier in strict mode.
    }
    delete foo;
    // SyntaxError: Delete of an unqualified identifier in strict mode.
  })();
  ```

- 매개변수 이름의 중복

  중복된 매개변수를 사용하면 SyntaxError가 발생한다.

  ```javascript
  (function () {
    "use strict";

    //SyntaxError: Duplicate parameter name not allowed in this context
    function foo(x, x) {
      return x + x;
    }
    console.log(foo(1, 2));
  })();
  ```

- with 문의 사용

  with문을 사용하면 SyntaxError가 발생한다.

  with문은 전달된 객체를 스코프 체인에 추가한다. with문은 동일한 객체의 프로퍼티를 반복해서 사용할 때 객체 이름을 생략할 수 있어서 코드가 간단해지는 효과가 있지만 성능과 가독성이 나빠져 사용하지 않는것이 좋다.

  ```javascript
  (function () {
    "use strict";

    // SyntaxError: Strict mode code may not include a with statement
    with ({ x: 1 }) {
      console.log(x);
    }
  })();
  ```

## strict mode 적용에 의한 변화

- 일반 함수의 this

  strict mode에서 함수를 일반함수로 호출하면 this에 undefined가 바인딩된다.

  생성자 함수가 아닌 일반 함수 내부에서는 this를 사용할 필요가 없기 때문이다.

  ```javascript
  (function () {
    "use strict";

    function foo() {
      console.log(this); // undefined
    }
    foo();

    function Foo() {
      console.log(this); // Foo
    }
    new Foo();
  })();
  ```

- arguments 객체

  strict mode에서 매개변수에 전달된 인수를 재할당하여 변경해도 arguments 객체에 반영되지 않는다.

  ```javascript
  (function (a) {
    "use strict";
    // 매개변수에 전달된 인수를 재할당하여 변경
    a = 2;

    // 변경된 인수가 arguments 객체에 반영되지 않는다.
    console.log(arguments); // { 0: 1, length: 1 }
  })(1);
  ```

출처 : 이웅모, 『모던 자바스크립트 deep dive』, 위키북스
