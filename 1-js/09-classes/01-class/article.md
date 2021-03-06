
# 클래스와 기본 문법

```quote author="위키백과"
클래스는 객체 지향 프로그래밍에서 특정 객체를 생성하기 위해 변수와 메소드를 정의하는 일종의 틀로, 객체를 정의하기 위한 상태(멤버 변수)와 메서드(함수)로 구성된다.
```

실무에선 사용자나 물건같이 동일한 종류의 객체를 여러 개 생성해야 하는 경우가 잦습니다.

이럴 때 <info:constructor-new>에서 배운 `new function`을 사용할 수 있습니다.

여기에 더하여 모던 자바스크립트에 도입된 `클래스(class)`라는 문법을 사용하면 객체 지향 프로그래밍에서 사용되는 다양한 기능을 자바스크립트에서도 사용할 수 있습니다.

## 기본 문법

클래스는 다음과 같은 기본 문법을 사용해 만들 수 있습니다.
```js
class MyClass {
  // 여러 메서드를 정의할 수 있습니다.
  constructor() { ... }
  method1() { ... }
  method2() { ... }
  method3() { ... }
  ...
}
```

이렇게 클래스를 만들고, `new MyClass()`를 호출하면 위에서 정의한 메서드가 들어 있는 객체가 생성됩니다.

객체의 기본 상태를 설정해주는 생성자 메서드 `constructor()`는 `new`에 의해 자동으로 호출되므로, 특별한 절차 없이 객체를 초기화 할 수 있습니다.

예시:

```js run
class User {

  constructor(name) {
    this.name = name;
  }

  sayHi() {
    alert(this.name);
  }

}

// 사용법:
let user = new User("John");
user.sayHi();
```

`new User("John")`를 호출하면 다음과 같은 일이 일어납니다.
1. 새로운 객체가 생성됩니다.
2. 넘겨받은 인수와 함께 `constructor`가 자동으로 실행됩니다. 이때 인수 `"John"`이 `this.name`에 할당됩니다.

이런 과정을 거친 후에 `user.sayHi()` 같은 객체 메서드를 호출할 수 있습니다.


```warn header="클래스 메서드 사이엔 쉼표가 없습니다."
초보 개발자는 클래스 메서드 사이에 쉼표를 넣는 실수를 저지르곤 합니다. 이렇게 쉼표를 넣으면 문법 에러가 발생합니다.

클래스와 관련된 표기법은 객체 리터럴 표기법과 차이가 있습니다. 클래스에선 메서드 사이에 쉼표를 넣지 않아도 됩니다.
```

## 클래스란

이 시점에서 "`클래스`가 정확히 뭔가요?"라는 의문이 생기실 겁니다. 클래스는 자바스크립트에서 새롭게 창안 한 개체(entity)가 아닙니다.

클래스가 보여주는 다양한 마법의 원리를 하나씩 파헤치면서 클래스가 정확히 무엇인지 알아봅시다. 이 과정을 거치고 나면 자바스크립트의 복잡한 기능을 이해할 수 있게 됩니다.

자바스크립트에서 클래스는 함수의 한 종류입니다.

코드로 확인해봅시다.

```js run
class User {
  constructor(name) { this.name = name; }
  sayHi() { alert(this.name); }
}

// User가 함수라는 증거
*!*
alert(typeof User); // function
*/!*
```

`class User {...}` 문법 구조가 진짜 하는 일은 다음과 같습니다.

1. `User`라는 이름을 가진 함수를 만듭니다. 함수 본문은 생성자 메서드 `constructor`에서 가져옵니다. 생성자 메서드가 없으면 본문이 비워진 채로 함수가 만들어집니다.
2. `sayHi`같은 클래스 내에서 정의한 메서드를 `User.prototype`에 저장합니다.

`new User`를 호출해 객체를 만들고, 객체의 메서드를 호출하면 <info:function-prototype>에서 설명한 것처럼 메서드를 프로토타입에서 가져옵니다. 이 과정이 있기 때문에 객체에서 클래스 메서드에 접근할 수 있습니다.

`class User` 선언 결과를 그림으로 나타내면 아래와 같습니다.

![](class-user.svg)

지금까지 했던 설명을 코드로 표현하면 다음과 같습니다. 

```js run
class User {
  constructor(name) { this.name = name; }
  sayHi() { alert(this.name); }
}

// 클래스는 함수입니다.
alert(typeof User); // function

// 정확히는 생성자 메서드와 동일합니다.
alert(User === User.prototype.constructor); // true

// 클래스 내부에서 정의한 메서드는 User.prototype에 저장됩니다.
alert(User.prototype.sayHi); // alert(this.name);

// 현재 프로토타입에는 메서드가 두 개입니다.
alert(Object.getOwnPropertyNames(User.prototype)); // constructor, sayHi
```

## 클래스는 단순한 편의 문법이 아닙니다.

어떤 사람들은 `class`라는 키워드 없이도 클래스 역할을 하는 함수를 선언할 수 있기 때문에 `클래스`는 '편의 문법'에 불과하다고 이야기합니다. 참고로 기능은 동일하나 기존 문법을 쉽게 읽을 수 있게 만든 문법을 편의 문법(syntactic sugar, 문법 설탕)이라고 합니다. 

```js run
// class User와 동일한 기능을 하는 순수 함수를 만들어보겠습니다.

// 1. 생성자 함수를 만듭니다.
function User(name) {
  this.name = name;
}
// 모든 함수의 프로토타입은 'constructor' 프로퍼티를 기본으로 갖고 있기 때문에
// constructor 프로퍼티를 명시적으로 만들 필요가 없습니다.

// 2. prototype에 메서드를 추가합니다.
User.prototype.sayHi = function() {
  alert(this.name);
};

// 사용법:
let user = new User("John");
user.sayHi();
```

위 예시처럼 순수 함수로 클래스 역할을 하는 함수를 선언하는 방법과 `class` 키워드를 사용하는 방법의 결과는 거의 같습니다. `class`가 단순한 편의 문법이라고 생각하는 이유가 여기에 있습니다.

그런데 두 방법에는 중요한 차이가 몇 가지 있습니다.

1. `class`로 만든 함수엔 특수 내부 프로퍼티인 `[[FunctionKind]]:"classConstructor"`가 이름표처럼 붙습니다. 이것만으로도 두 방법엔 분명한 차이가 있음을 알 수 있습니다. 

    자바스크립트는 다양한 방법을 사용해 함수에 `[[FunctionKind]]:"classConstructor"`가 있는지를 확인합니다. 이런 검증 과정이 있기 때문에 클래스 생성자를 `new`와 함께 호출하지 않으면 에러가 발생합니다.

    ```js run
    class User {
      constructor() {}
    }

    alert(typeof User); // function
    User(); // Error: Class constructor User cannot be invoked without 'new'
    ```

    대부분의 자바스크립트 엔진이 클래스 생성자를 문자열로 표현할 때 'class...'로 시작하는 문자열로 표현한다는 점 역시 다릅니다.

    ```js run
    class User {
      constructor() {}
    }

    alert(User); // class User { ... }
    ```
    또 다른 차이점들에 대해선 더 살펴볼 예정입니다.

2. 클래스 메서드는 열거할 수 없습니다(non-enumerable).
    클래스의 `"prototype"` 프로퍼티에 추가된 메서드 전체의 `enumerable` 플래그는 `false`입니다.

    `for..in`으로 객체를 순회할 때, 메서드는 순회 대상에서 제외하고자 하는 경우가 많음므로 이 특징은 도움이 됩니다.

3. 클래스는 항상 `엄격 모드`로 실행됩니다(`use strict`).
    클래스 생성자 안 코드 전체엔 자동으로 엄격 모드가 적용됩니다.

이 외에도 `class`를 사용하면 다양한 기능이 따라오는데, 자세한 내용은 곧 다루겠습니다.

## 클래스 표현식

함수처럼 클래스도 다른 표현식 내부에서 정의, 전달, 반환, 할당될 수 있습니다.

클래스 표현식을 만들어보겠습니다.

```js
let User = class {
  sayHi() {
    alert("Hello");
  }
};
```

기명 함수 표현식(Named Function Expression)과 유사하게 클래스 표현식에도 이름을 붙일 수 있습니다.

클래스 표현식에 이름을 붙이면, 이 이름은 오직 클래스 내부에서만 사용할 수 있습니다.

```js run
// 기명 클래스 표현식(Named Class Expression)
// (명세서엔 없는 용어이지만, 기명 함수 표현식과 유사하게 동작합니다.)
let User = class *!*MyClass*/!* {
  sayHi() {
    alert(MyClass); // MyClass라는 이름은 오직 클래스 안에서만 사용할 수 있습니다.
  }
};

new User().sayHi(); // 제대로 동작합니다(MyClass의 정의를 보여줌).

alert(MyClass); // Error: MyClass is not defined, MyClass는 클래스 밖에서 사용할 수 없습니다.
```

아래와 같이 '필요에 따라' 클래스를 동적으로 생성하는 것도 가능합니다.

```js run
function makeClass(phrase) {
  // 클래스를 선언하고 이를 반환함
  return class {
    sayHi() {
      alert(phrase);
    };
  };
}

// 새로운 클래스를 만듦
let User = makeClass("Hello");

new User().sayHi(); // Hello
```


## getter·setter

리터럴을 사용해 만든 객체처럼 클래스도 getter나 setter, 계산된 프로퍼티(computed property)를 포함할 수 있습니다.

`get`과`set`을 이용해 `user.name`을 조작할 수 있게 해봅시다.

```js run
class User {

  constructor(name) {
    // setter를 활성화합니다.
    this.name = name;
  }

*!*
  get name() {
*/!*
    return this._name;
  }

*!*
  set name(value) {
*/!*
    if (value.length < 4) {
      alert("이름이 너무 짧습니다.");
      return;
    }
    this._name = value;
  }

}

let user = new User("John");
alert(user.name); // John

user = new User(""); // 이름이 너무 짧습니다.
```

이런 방법으로 클래스를 선언하면 `User.prototype`에 getter와 setter가 만들어지므로 get과 set을 사용할 수 있습니다.

## 계산된 메서드 이름 [...]

대괄호 `[...]`를 이용해 계산된 메서드 이름(computed method name)을 만드는 예시를 살펴봅시다.

```js run
class User {

*!*
  ['say' + 'Hi']() {
*/!*
    alert("Hello");
  }

}

new User().sayHi();
```

계산된 메서드 이름은 리터럴 객체와 유사한 형태를 띠기 때문에 사용법을 외우기 쉽다는 장점이 있습니다.

## 클래스 필드

```warn header="구식 브라우저에선 폴리필이 필요할 수 있습니다."
클래스 필드는 근래에 더해진 기능입니다.
```

지금까지 살펴본 예시엔 메서드가 하나만 있었습니다.

'클래스 필드(class field)'라는 문법을 사용하면 어떤 종류의 프로퍼티도 클래스에 추가할 수 있습니다.

`class User`에 `name` 프로퍼티를 추가해봅시다.
 
```js run
class User {
*!*
  name = "John";
*/!*

  sayHi() {
    alert(`Hello, ${this.name}!`);
  }
}

new User().sayHi(); // Hello, John!
```

So, we just write "<property name> = <value>" in the declaration, and that's it.

The important difference of class fields is that they are set on individual objects, not `User.prototype`:

```js run
class User {
*!*
  name = "John";
*/!*
}

let user = new User();
alert(user.name); // John
alert(User.prototype.name); // undefined
```

Technically, they are processed after the constructor has done it's job, and we can use for them complex expressions and function calls:

```js run
class User {
*!*
  name = prompt("Name, please?", "John");
*/!*
}

let user = new User();
alert(user.name); // John
```

### Making bound methods with class fields

As demonstrated in the chapter <info:bind> functions in JavaScript have a dynamic `this`. It depends on the context of the call.

So if an object method is passed around and called in another context, `this` won't be a reference to its object any more.

For instance, this code will show `undefined`:

```js run
class Button {
  constructor(value) {
    this.value = value;
  }

  click() {
    alert(this.value);
  }
}

let button = new Button("hello");

*!*
setTimeout(button.click, 1000); // undefined
*/!*
```

The problem is called "losing `this`".

There are two approaches to fixing it, as discussed in the chapter <info:bind>:

1. Pass a wrapper-function, such as `setTimeout(() => button.click(), 1000)`.
2. Bind the method to object, e.g. in the constructor:

```js run
class Button {
  constructor(value) {
    this.value = value;
*!*
    this.click = this.click.bind(this);
*/!*
  }

  click() {
    alert(this.value);
  }
}

let button = new Button("hello");

*!*
setTimeout(button.click, 1000); // hello
*/!*
```

Class fields provide a more elegant syntax for the latter solution:

```js run
class Button {
  constructor(value) {
    this.value = value;
  }
*!*
  click = () => {
    alert(this.value);
  }
*/!*
}

let button = new Button("hello");

setTimeout(button.click, 1000); // hello
```

The class field `click = () => {...}` creates an independent function on each `Button` object, with `this` bound to the object. Then we can pass `button.click` around anywhere, and it will be called with the right `this`.

That's especially useful in browser environment, when we need to setup a method as an event listener.

## 요약

아래와 같은 기본문법을 사용해 클래스를 만들 수 있습니다.

```js
class MyClass {
  prop = value; // 프로퍼티

  constructor(...) { // 생성자 메서드
    // ...
  }

  method(...) {} // 메서드

  get something(...) {} // getter 메서드
  set something(...) {} // setter 메서드

  [Symbol.iterator]() {} // 계산된 이름(computed name)을 사용해 만드는 메서드 (심볼)
  // ...
}
```

`MyClass`는 `constructor`의 코드를 본문으로 갖는 함수입니다. `MyClass`에서 정의한 일반 메서드나 getter, setter는 `MyClass.prototype`에 쓰여집니다.

이어지는 챕터에선 상속을 비롯한 클래스의 다양한 기능에 대해 알아보겠습니다.
