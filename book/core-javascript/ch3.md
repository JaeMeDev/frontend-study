# 🌸 Chapter 3: this

## 🍄 1. 상황에 따라 달라지는 this

- 자바스크립트에서 `this` 는 기본적으로 실행 컨텍스트가 생성될 때 함께 결정된다.
- this는 함수를 호출할 때 결정된다. 함수를 어떤 방식으로 호출하느냐에 따라 값이 달라지는 것이다.

### 🌻 1-1. 전역공간에서의 this

- 전역 공간에서 this는 전역 객체를 가리킨다. 개념상 전역 컨텍스트를 생성하는 주체가 바로 전역 객체이기 때문이다.
- 전역 객체는 자바스크립트 런타임 환경에 따라 다른 이름과 정보를 가지고 있다.
- 브라우저 환경에서 전역객체는 `window` 이고 Node.js 환경에서는 `global` 이다.

```jsx
// 브라우저 환경
console.log(this); // { alert: f(), atob: f(), btoa: f(), ... }
console.log(window); // { alert: f(), atob: f(), btoa: f(), ... }
console.log(this === window); // true

// Node.js 환경
console.log(this); // { process : { title : 'node', version: 'v10.13.0', ... } }
console.log(global); // { process: { title: 'node', version: 'v10.13.0', ... } }
console.log(this === global); // true
```

- 전역변수를 선언하면 자바스크립트 엔진은 이를 전역객체의 프로퍼티로도 할당한다. 변수이면서 객체의 프로퍼티이기도 한 셈이다.

```jsx
var a = 1;
console.log(a); // 1
console.log(window.a); // 1
console.log(this.a); // 1
```

- 전역공간에서의 this는 전역객체를 의미하므로 두 값이 같은 값을 출력하는 것은 당연하지만, 그 값이 1인 것이 의아하다. 그 이유는 **자바스크립트의 모든 변수는 실은 특정 객체의 프로퍼티로서 동작하기 때문이다.**
- 사용자가 var 연산자를 이용해 변수를 선언하더라도 실제 자바스크립트 엔진은 어떤 특정 객체의 프로퍼티로 인식한다. 특정 객체란 바로 실행 컨텍스트의 LexicalEnvironment이다.
- 전역변수를 선언하면 자동으로 전역객체의 프로퍼티로도 할당한다는 이 문장은 틀린 것이다. 정확히 표현하면 **전역변수를 선언하면 자바스크립트 엔진은 이를 전역객체의 프로퍼티로 할당한다**가 맞는 표현이다.
- var로 변수를 선언하는 대신 window 프로퍼티에 직접할당하더라도 결과적으로는 똑같이 동작한다.

```jsx
var a = 1;
window.b = 2;
console.log(a, window.a, this.a); // 1 1 1
console.log(b, window.b, this.b); // 2 2 2

window.a = 3;
b = 4;
console.log(a, window.a, this.a); // 3 3 3
console.log(b, window.b, this.b); // 4 4 4
```

- 전역변수 선언과 전역객체의 프로퍼티 할당 사이에 전혀 다른 경우도 있는데, 바로 삭제 명령에 대해 그렇다.

```jsx
var a = 1;
delete window.a; // false
console.log(a, window.a, this.a); // 1 1 1

var b = 2;
delete b; // false
console.log(b, window.b, this.b); // 2 2 2

window.c = 3;
delete window.c; // true
console.log(c, window.c, this.c); // ReferenceError : c is not defined

window.d = 4;
delete d; // true
console.log(d, window.d, this.d); // ReferenceError : d is not defined
```

- 사용자가 의도치 않게 삭제하는 것을 방지하는 차원에서 마련한 나름의 방어 전략으로 전역변수를 선언하면 자바스크립트 엔진이 이를 자동으로 전역객체의 프로퍼티로 할당하면서 추가적으로 해당 프로퍼티의 configurable 속성(변경 및 삭제 가능성)을 false로 정의한다.
- var로 선언한 전역변수와 전역객체의 프로퍼티는 호이스팅 여부 및 configurable여부에서 차이를 보인다.

### 🌻 1-2. 메서드로서 호출할 때 그 메서드 내부에서의 this

1. 함수 vs 메서드
- 어떤 함수를 실행하는 방법은 여러가지가 있는데, 가장 일반적인 방법 두 가지로는 함수로서 호출하는 경우와 메서드로서 호출하는 경우이다.
- 이 둘을 구분하는 유일한 차이는 **독립성**에 있다. 함수는 그 자체로 독립적인 기능을 수행하는 반면, 메서드는 자신을 호출한 대상 객체에 관한 동작을 수행한다. 자바스립트는 상황별로 this 키워드에 다른 값을 부여하게 함으로써 이를 구현했다.
- 어떤 함수를 객체의 프로퍼티에 할당한다고 해서 그 자체로 무조건 메서드가 되는 것이 아니라 객체의 메서드로서 호출할 경우에만 메서드로 동작하고, 그렇지 않으면 함수로 동작한다.

```jsx
var func = function (x) {
	console.log(this, x);
};

func(1); // Window { ... } 1

var obj = {
	method: func
};

obj.method(2); // { method: f } 2
```

- obj의 method 프로퍼티에 할당한 값과 func 변수에 할당한 값은 모두 1번째 줄에서 선언한 함수를 참조한다. 즉 원래의 익명함수는 그대로인데 이를 변수에 담아 호출한 경우와 obj 객체의 프로퍼티에 할당해서 호출한 경우에 this가 달라지는 것이다.
- 함수로서 호출과 메서드로서 호출은 함수 앞에 점(.)이 있는지 여부만으로 간단하게 구분할 수 있다.
1. 메서드 내부에서의 this
- this에는 호출한 주체에 대한 정보가 담긴다. 어떤 함수를 메서드로서 호출하는 경우 호출 주체는 바로 함수명(프로퍼티명)앞의 객체이다. 점 표기법의 경우 마지막 점 앞에 명시된 객체가 곧 this가 되는 것이다.

```jsx
var obj = {
	methodA: function() { console.log(this); },
	inner: {
		methodB: function() { console.log(this); }
	}
};

obj.methodA(); // { methodA: f, inner: { ... } } ( === obj)
obj['methodA'](); // { methodA: f, inner: { ... } } )( === obj)

obj.inner.methodB(); // { methodB: f } ( === obj.inner)
obj.inner['methodB'](); // { methodB: f } ( === obj.inner)
obj['inner'].methodB(); // { methodB: f } ( === obj.inner)
obj['inner']['methodB'](); // { methodB: f } ( === obj.inner)
```

### 🌻 1-3. 함수로서 호출할 때 그 함수 내부에서의 this

1. 함수 내부에서의 this
- 어떤 함수를 함수로서 호출할 경우 this가 저장되지 않는다.
- this에는 호출한 주체에 대한 정보가 담기고, 실행컨텍스트를 활성화할 당시에 this가 지정되지 않은 경우 this는 전역객체를 바라보기 때문에 함수에서의 this는 전역 객체를 가리킨다.
- 더글라스 크락포드는 이를 명백한 설계상의 오류라고 지적했다.
1. 메서드의 내부함수에서의 this

```jsx
var obj1 {
	outer: function () {
		console.log(this); // (1)obj1
		var innerFunc = function() {
			console.log(this); // (2) window (3) obj2
		}
		innerFunc();

		var obj2 = {
			innerMethod: innerFunc
		};
		obj.innerMethod();
	}	
};

obj1.outer();
```

- 1번째 줄 : 객체를 생성하는데, 이때 객체 내부에는 outer라는 프로퍼티가 있으며, 여기에는 익명함수가 연결된다. 이렇게 생성한 객체를 obj1에 할당한다.
- 15번째 줄 : obj1.outer를 호출한다.
- 2번째 줄 : obj1.outer 함수의 실행 컨텍스트가 생성되면서 호이스팅하고, 스코프 체인 정보를 수집하고 this를 바인딩한다. 이 함수는 호출할 때 함수명인 outer 앞에 점(.)이 있었으므로 메서드로서 호출한 것이다. 따라서 this에는 마지막 점 앞의 객체인 obj1이 바인딩된다.
- 3번째 줄 : obj1 객체 정보가 출력된다.
- 4번째 줄 : 호이스팅된 변수 innerFunc는 outer 스코프 내에서만 접근할 수 있는 지역변수이다. 이 지역변수에 익명 함수를 할당한다.
- 7번째 줄 : innerFunc를 호출한다.
- 4번째 줄 : innerFunc 함수의 실행 컨텍스트가 생성되면서 호이스팅, 스코프 체인 수집, this 바인딩 등을 수행한다. 이 함수를 호출할 때 함수명 앞에는 점(.)이 없다. 즉 함수로서 호출한 것이므로 this가 지정되지 않았고, 따라서 자동으로 스코프 체인상의 최상위 객체인 전역객체(Window)가 바인딩된다.
- 5번째 줄 : Window객체 정보가 출력된다.
- 9번째 줄 : 호이스팅된 변수 obj2 역시 outer 스코프 내에서만 접근할 수 있는 지역변수이다. 여기에 다시 객체를 할당하는데, 그 객체에는 innerMethod라는 프로퍼티가 있으며, 여기에는 앞서 정으된 변수 innerFunc와 연결된 익명 함수가 연결된다.
- 12번째 줄 : obj2.innerMethod를 호출한다.
- 9번째 줄 : obj2.innerMethod 함수의 실행 컨텍스트가 생성된다. 이 함수는 호출할 때 함수명인 innerMethod 앞에 점(.)이 있었으므로 메서드로서 호출한 것이다. 따라서 this에는 마지막 점 앞의 객체인 obj2가 바인딩된다.
- 10번째 줄 : obj2 객체 정보가 출력된다.
- this 바인딩에 관해서는 함수를 실행하는 당시의 주변 환경(메서드 내부인지, 함수 내부인지 등)은 중요하지 않고, 오직 함수를 호출하는 구문 앞에 점 또는 대괄호 표기가 있는지 없는지가 관건이다.
1. 메서드의 내부 함수에서의 this를 우회하는 방법
- ES5까지는 자체적으로 내부함수에 this를 상속할 방법이 없지만 다행히 이를 우회할 방법이 없지는 않았다. 대표적인 방법은 변수를 활용하는 것이다.

```jsx
var obj = {
	outer: function(){
		console.log(this); // (1) { outer: f }
		var innerFunc1 = function() {
			console.log(this); // (2) Window { ... }
		};
		innerFunc1();

		var self = this;
		var innerFunc2 = function () {
			console.log(self); // (3) { outer: f }
		}
		innerFunc2();
	}
};

obj.outer();
```

- 우회라고 할 수도 없을 만큼 허무한 방법이지만 기대에는 충실히 부합한다. 그저 상위 스코프의 this를 저장해서 내부 함수에서 활용하려는 수단일뿐이므로 의미만 통한다면 변수명을 무엇으로 정해도 무관한다. 다만 원활한 협업이나 의사소통을 위해서는 널리 쓰이는 단어를 활용하는 것이 바람직하다.
1. this를 바인딩하지 않는 함수
- ES6에서는 함수 내부에서 this가 전역객체를 바라보는 문제를 보완하고자, this를 바인딩하지 않는 화살표 함수(arrow function)를 새로 도입했다. 화살표 함수는 실행 컨텍스트를 생성할 때 this 바인딩 과정 자체가 빠지게 되어, 상위 스코프의 this를 그대로 활용할 수 있다.

```jsx
var obj = {
	outer: function() {
		console.log(this); // (1) { outer: f }
		var innerFunc = () => {
			console.log(this); // (2) { outer: f }
		}
		innerFunc();
	}
}

obj.outer();
```

- 그 밖에도 call, apply 등의 메서드를 활용해 함수를 호출할 때 명시적으로 this를 지정하는 방법이 있다.

### 🌻 1-4. 콜백 함수 호출 시 그 함수 내부에서의 this

- 함수 A의 제어권을 다른 함수(또는 메서드) B에게 넘겨주는 경우 함수 A를 콜백 함수라 한다. 이때 함수 A는 함수 B의 내부 로직에 따라 실행되며, this 역시 함수 B 내부로직에서 정한 규칙에 따라 값이 결정된다. 콜백 함수도 함수이기 때문에 기본적으로 this가 전역객체를 참조하지만, 제어권을 받은 함수에서 콜백 함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조하게 된다.

```jsx
setTimeout(function () { console.log(this); }, 300); // (1)

[1,2,3,4,5].forEach(function(x){
	console.log(this, x); // (2)
});

document.body.innerHTML += '<button id="a">클릭</button>';
document.body.querySelector('#a')
		.addEventListener('click', function (e) {
			console.log(this, e); // (3)
} 
```

- (1) : setTimeout 함수는 300ms 만큼 시간 지연을 한 뒤 콜백 함수를 실행하라는 명령이다. 0.3초 뒤에 전역객체가 출력된다.
- (2) : forEach 메서드는 배열의 각 요소를 앞에서부터 차례로 하나씩 꺼내어 그 값을 콜백 함수의 첫 번째 인자로 삼아 함수를 실행하라는 명령이다. 전역객체와 배열의 각 요소가 총 5회 출력된다.
- (3) : addEventListener는 지정한 HTML 엘리먼트에 ‘click’ 이벤트가 발생할 때마다 그 이벤트 정보를 콜백 함수의 첫 번째 인자로 삼아 함수를 실행하라는 명령이다. 버튼을 클릭하면 앞서 지정한 엘리먼트와 클릭 이벤트에 관한 정보가 담긴 객체가 출력된다.
- (1)과 (2) 메서드는 그 내부에서 콜백 함수를 호출할 때 대상이 될 this를 지정하지 않았다. 따라서 콜백 함수 내부에서의 this는 전역객체를 참조한다.
- (3)의 addEventListener 메서드는 콜백 함수를 호출할 때 자신의 this를 상속하도록 정의돼 있다.
- 이처럼 콜백 함수에서의 this는 무조건 이거다라고 정의할 수 없다. 콜백 함수의 제어권을 가지는 함수가 콜백 함수에서의 this를 무엇으로 할지를 결정하며, 특별히 정의하지 않은 경우에는 기본적으로 전역객체를 바라본다.

### 🌻 1-5. 생성자 함수 내부에서의 this

- 생성자 함수는 어떤 공통된 성질을 지니는 객체들을 생성하는 데 사용하는 함수이다.
- 객체지향 언어에서는 생성자를 클래스, 클래스를 통해 만든 객체를 인스턴스라고 한다.
- 프로그래밍적으로 생성자는 구체적인 인스턴스를 만들기 위한 일종의 틀이다. 이 틀에는 해당 클래스의 공통 속성들이 미리 준비돼 있고, 여기에 구체적인 인스턴스의 개성을 더해 개별 인스턴스를 만들 수 있는 것이다.
- 자바스크립트는 함수에 생성자로서의 역할을 함께 부여했다. new 명령어와 함께 함수를 호출하면 해당 함수가 생성자로서 동작하게 된다. 그리고 어떤 함수가 생성자 함수로서 호출된 경우 내부에서의 this는 곧 새로 만들 구체적인 인스턴스 자신이 된다.
- 생성자 함수를 호출하면 우선 생성자의 prototype 프로퍼티를 참조하는 `__proto__` 라는 프로퍼티가 있는 객체를 만들고, 미리 준비된 공통 속성 및 개성을 해당 객체에 부여한다.

```jsx
var Cat = function(name, age){
	this.bark = '야옹';
	this.name = name;
	this.age = age;
}

var choco = new Cat('초코', 7);
var nabi = new Cat('나비', 5);
console.log(choco, nabi);
```

## 🍄 2. 명시적으로 this를 바인딩하는 방법

### 🌻 2-1. call 메서드

```jsx
Function.prototype.call(thisArg[, arg1[, arg2[, ...]]])
```

- call 메서드는 메서드의 호출 주체인 함수를 즉시 실행하도록 하는 명령이다.
- 이때 call 메서드의 첫 번째 인자를 this로 바인딩하고, 이후의 인자들을 호출할 함수의 매개변수로 한다. 함수를 그냥 실행하면 this는 전역 객체를 참조하지만 call 메서드를 이용하면 임의의 객체를 this로 지정할 수 있다.

```jsx
var func = function(a,b,c) {
	console.log(this, a, b, c);
}

func(1,2,3); // Window{...} 1 2 3
func.call({x:1}, 1,2,3) // {x: 1} 4 5 6
```

- 메서드에 대해서도 마찬가지로 객체의 메서드를 그냥 호출하면 this는 객체를 참조하지만 call 메서드를 이용하면 임의의 객체를 this로 지정할 수 있다.

```jsx
var obj = {
	a: 1,
	method: fucntion(x, y) {
		console.log(this.a, x, y);
	}
};

obj.method(2, 3); // 1 2 3
obj.method.call({a:4}, 5, 6); // 4 5 6
```

### 🌻 2-2. apply 메서드

```jsx
Function.prototype.apply(thisArg[, argsArray])
```

- apply 메서드는 call 메서드와 기능적으로 완전히 동일하다. call 메서드는 첫 번째 인자를 제외한 나머지 모든 인자들을 호출할 함수의 매개변수로 지정하는 반면, apply 메서드는 두 번째 인자를 배열로 받아 그 배열의 요소들을 호출할 함수의 매개변수로 지정한다는 점에서만 차이가 있다.

```jsx
var func = function(a,b,c) {
	console.log(this, a, b, c);
}

func(1,2,3); // Window{...} 1 2 3
func.apply({x:1}, [1,2,3]) // {x: 1} 4 5 6

var obj = {
	a: 1,
	method: fucntion(x, y) {
		console.log(this.a, x, y);
	}
};

obj.method(2, 3); // 1 2 3
obj.method.apply({a:4}, [5, 6]); // 4 5 6
```

### 🌻 2-3 call / apply 메서드의 활용

- call이나 apply 메서드를 잘 활용하면 자바스크립트를 더욱 다채롭게 사용할 수 있다.
1. 유사 배열객체에 배열 메서드를 적용

```jsx
var obj = {
	0: 'a',
	1: 'b',
	2: 'c',
	length: 3
}

Array.prototype.push.call(obj, 'd');
console.log(obj); // { 0: 'a', 1: 'b', 2: 'c', 3: 'd', length: 4 }

var arr = Array.prototype.slice.call(obj);
console.log(arr); // ['a', 'b', 'c', 'd']
```

- 객체에는 배열 메서드를 직접 적용할 수 없다. 그러나 키가 0 또는 양의 정수인 프로퍼티가 존재하고 length 프로퍼티의 값이 0 또는 양의 정수인 객체, 즉 배열의 구조와 유사한 객체의 경우 call 또는 apply 메서드를 이용해 배열 메서드를 차용할 수 있다.
- slice 메서드는 원래 시작 인덱스값과 마지막 인덱스 값을 받아 시작값부터 마지막값의 앞 부분까지의 배열 요소를 추출하는 메서드인데, 매개변수를 아무것도 넘기지 않을 경우에는 그냥 원본 배열의 얕은 복사본을 반환한다.
- 함수 내부에서 접근할 수 있는 arguments 객체도 유사배열객체이므로 위의 방법으로 배열로 전환해서 활용할 수 있다.

```jsx
function a () {
	var argv = Array.prototype.slice.call(arguments);
	argv.forEach(function (arg) {
	console.log(arg);
	});
}

a(1, 2, 3);

document.body.innerHTML = '<div>a</div><div>b</div><div>c</div>';
var nodeList = document.querySelectorAll('div');
var nodeArr = Array.prototype.slice.call(nodeList);
nodeArr.forEach(function (node) {
	console.log(node);
});
```

- 문자열의 경우 length 프로퍼티가 읽기 전용이기 때문에 원본 문자열에 변경을 가하는 메서드(push, pop, shift, unshift, splice 등)는 에러를 던지며, concat처럼 대상이 반드시 배열이어야 하는 경우에는 에러는 나지만 제대로 된 결과를 얻을 수 없다.

```jsx
var str = 'abc def';

Array.prototype.push.call(str, ', pushed string'); // Error

Array.prototype.concat.call(str, 'string'); // [String {"abc def"}, "string"]

Array.prototype.every.call(str, function(char) { return char !== ' '; }); // false

Array.prototype.some.call(str, function(char) { return char === ' '; }); // true

var newArr = Array.prototype.map.call(str, function(char) { return char + '!'; });
console.log(newArr); // ['a!', 'b!', 'c!', ' !', 'd!', 'e!', 'f!']

var newStr = Array.prototype.reduce.apply(str, [
	function(string, char, i) { return string + char + i; },
	''
]);
console.log(newStr); // "a0b1c2 3d4e5f6"
```

- call/apply를 이용해 형변환하는 것은 this를 원하는 값으로 지정해서 호출한다라는 본래의 메서드의 의도와는 다소 동떨어진 활용법이라 할 수 있다.
- ES6에서는 유사배열객체 또는 순회 가능한 모든 종류의 데이터 타입을 배열로 전환하는 Array.from 메서드를 새로 도입했다.

```jsx
var obj = {
	0: 'a',
	1: 'b',
	2: 'c',
	length: 3
};

var arr = Array.from(obj);
console.log(arr); // ['a', 'b', 'c']
```

1. 생성자 내부에서 다른 생성자를 호출
- 생성자 내부에 다른 생성자와 공통된 내용이 있을 경우 call 또는 apply를 이용해 다른 생성자를 호출하면 간단하게 반복을 줄일 수 있다.

```jsx
function Person(name, gender) {
	this.name = name;
	this.gender = gender;
}

function Student(name, gender, school){
	Person.call(this, name, gender);
	this.school = school;
}

function Employee(name, gender, company){
	Person.apply(this, [name, gender]);;
	this.company = company;
}

var jj = new Student('재준', '남자', '학교안다님');
var mj = new Employee('미정', '여자', 'google');
```

1. 여러 인수를 묶어 하나의 배열로 전달하고 싶을 때 - apply 활용
- 여러 개의 인수를 받는 메서드에게 하나의 배열로 인수들을 전달하고 싶을 때 apply 메서드를 사용하면 좋다.

```jsx
var numbers = [10, 20, 3, 16, 45];
var max = min = numbers[0];
numbers.forEach(function(number){
	if(number > max){
		max = number;
	}
	if(number < min){
		min = number;
	}
});

console.log(max, min); // 45 3
```

- 코드가 불필요하게 길고 가독성도 떨어진다. 이보다는 Math.max/Math.min 메서드에 apply를 적용하면 훨씬 간단해진다.

```jsx
var numbers = [10, 20, 3, 16, 45];
var max = Math.max.apply(null, numbers);
var min = Math.min.apply(null, numbers);
```

- 참고로 ES6에서의 펼치기 연산자(spread 연산자)를 이용하면 apply를 적용하는 것보다 더욱 간편하게 작성할 수 있다.

```jsx
const numbers = [10, 20, 3, 16, 45];
const max = Math.max(...numbers);
const min = Math.min(...numbers);
console.log(max, min); // 45 3
```

- call/apply 메서드는 명시적으로 별도의 this를 바인딩하면서 함수 또는 메서드를 실행하는 훌륭한 방법이지만 이로 인해 this를 예측하기 어렵게 만들어 코드 해석을 방해한다는 단점이 있다. ES5이하의 환경에서는 마땅한 대안이 없기 때문에 실무에서 매우 광범위하게 활용되고 있다.

### 🌻 2-4. bind 메서드

```jsx
Function.prototype.bind(thisArg[, arg1[, arg2[, ...]]])
```

- bind 메서드는 ES5에서 추가된 기능으로, call과 비슷하지만 즉시 호출하지는 않고 넘겨받은 this 및 인수들을 바탕으로 새로운 함수를 반환하기만 하는 메서드이다.
- 다시 새로운 함수를 호출할 때 인수를 넘기면 그 인수들은 기존 bind 메서드를 호출할 때 전달했던 인수들의 뒤에 이어서 등록된다.
- bind 메서드는 함수에 this를 미리 적용하는 것과 부분 적용 함수를 구현하는 두 가지 목적을 모두 지닌다.

```jsx
var func = function(a, b, c, d){
	console.log(this, a, b, c, d);
};

func(1, 2, 3, 4); // Window{...} 1 2 3 4

var bindFunc1 = func.bind({x:1}); 
bindFunc1(5,6,7,8); // {x:1} 5 6 7 8

var bindFunc2 = func.bind({x:1}, 4, 5);
bindFunc(6,7); // {x:1} 4 5 6 7
```

1. name 프로퍼티
- bind 메서드를 적용해서 새로 만든 함수는 한 가지 독특한 성질이 있다. 바로 name 프로퍼티에 동사 bind의 수동태인 bound라는 접두어가 붙는다는 점이다.
- 어떤 함수의 name 프로퍼티가 bound xxx 라면 이는 곧 함수명이 xxx인 원본 함수에 bind 메서드를 적용한 새로운 함수라는 의미가 되므로 기존의 call이나 apply보다 코드를 추적하기에 더 수월해진 면이 있다.

```jsx
var func = function(a,b,c,d) {
	console.log(this, a, b, c, d);
}

var bindFunc = func.bind({x: 1}, 4, 5);
console.log(func.name); // func
console.log(bindFunc.name); // bound func
```

1. 상위 컨텍스트의 this를 내부함수나 콜백함수에 전달하기
- 메서드의 내부함수에서 메서드의 this를 그대로 바라보게 하기 위한 방법으로 self 등의 변수를 활용한 우회법이 있었는데, call, apply 또는 bind 메서드를 이용하면 더 깔끔하게 처리할 수 있다.

```jsx
var obj = {
	outer: function() {
		console.log(this);
		var innerFunc = function(){
			console.log(this);
		}
		innerFunc.call(this);
	}
};

obj.outer();
```

```jsx
var obj = {
	outer: function() {
		console.log(this);
		var innerFunc = function(){
			console.log(this);
		}.bind(this);
		innerFunc();
	}
}

obj.outer();
```

- 또한 콜백 함수를 인자로 받는 함수나 메서드 중에서 기본적으로 콜백 함수 내에서의 this에 관여하는 함수 또는 메서드에 대해서도 bind 메서드를 이용하면 this 값을 사용자의 입맛이 맞게 바꿀 수 있다.

```jsx
var obj = {
	logThis: function(){
		console.log(this);
	},
	logThisLater1: function(){
		setTimeout(this.logThis, 500);
	}
	logThisLater2: function(){
		setTimeout(this.logThis.bind(this), 1000);
	}
}

obj.logThisLater1(); // Window { ... }
obj.logThisLater2(); // obj {logThis: f, ... }
```

### 🌻 2-5. 화살표 함수의 예외사항

- ES6에 새롭게 도입된 화살표 함수는 실행 컨텍스트 생성시 this를 바인딩하는 과정이 제외되었다. 즉 이 함수 내부에는 this가 아예 없으며, 접근하고자 하면 스코프체인상 가장 가까운 this에 접근하게 된다.

```jsx
var obj = {
	outer: function(){
		console.log(this);
		var innerFunc = () => {
			console.log(this);
		}
		innerFunc();
	}
}

obj.outer();
```

- 이렇게 하면 별도의 변수로 this를 우회하거나 call, apply, bind를 적용할 필요가 없어 더욱 간결하고 편리하다.

### 🌻 2-6. 별도의 인자로 this를 받는 경우(콜백 함수 내에서의 this)

- 콜백 함수를 인자로 받는 메서드 중 일부는 추가로 this로 지정할 객체를 인자로 지정할 수 있는 경우가 있다. 이러한 메서드의 thisArg 값을 지정하면 콜백 함수 내부에서 this 값을 원하는 대로 변경할 수 있다.
- 이런 형태는 여러 내부 요소에 대해 같은 동작을 반복 수행해야 하는 배열 메서드에 많이 포진돼 있으며, 같은 이유로 ES6에서 새로 등장한 Set, Map 등의 메서드에도 일부 존재한다.

```jsx
var report = {
	sum: 0,
	count: 0,
	add: function() {
		var args = Array.prototype.slice.call(arguments);
		args.forEach(function(entry){
			this.sum += entry;
			++this.count;
		}, this)
	},
	average: function(){
		return this.sum / this.count;
	}
}

report.add(60, 85, 95);
console.log(report.sum, report.count, report.average()); // 240 3 80
```

- forEach 함수의 두 번째 인자로 전달해준 this가 바인딩된다.
- thisArg를 인자로 받는 메서드는 꽤 많이 있다.

```jsx
Array.prototype.forEach(callback[, thisArg])
Array.prototype.map(callback[, thisArg])
Array.prototype.filter(callback[, thisArg])
Array.prototype.some(callback[, thisArg])
Array.prototype.every(callback[, thisArg])
Array.prototype.find(callback[, thisArg])
Array.prototype.findIndex(callback[, thisArg])
Array.prototype.flatMap(callback[, thisArg])
Array.prototype.from(arrayLike[, callback[, thisArg]])
Set.prototype.forEach(callback[, thisArg])
Map.prototype.forEach(callback[, thisArg])
```