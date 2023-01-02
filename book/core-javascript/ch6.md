# 🌸 Chapter 6: 프로토타입

- 자바크립트는 프로토타입 기반 언어이다.
- 클래스 기반 언어에서는 ‘상속’을 사용하지만 프로토타입 기반 언어에서는 어떤 객체를 원형으로 삼고 이를 복제함으로써 상속과 비슷한 효과를 얻는다.

## 🍄 1. 프로토타입의 개념 이해

### 🌻 6-1. constructor, prototype, instance

```jsx
var instance = new Constructor();
```

- 어떤 생성자 함수(Constructor)를 new 연산자와 함께 호출하면
- Constructor에서 정의된 내용을 바탕으로 새로운 인스턴스(instance)가 생성된다.
- 이때 instance에는 `__proto__` 라는 프로퍼티가 자동으로 부여되는데,
- 이 프로퍼티는 Constructor의 prototype이라는 프로퍼티를 참조한다.
- prototype은 객체이다. 이를 참조하는 `__proto__` 역시 당연히 객체이다. prototype 객체 내부에는 인스턴스가 사용할 메서드를 저장한다. 그러면 인스턴스에서도 숨겨진 프로퍼티인 `__proto__` 를 통해 이 메서드들에 접근할 수 있게 된다.

```jsx
var Person = function(name) {
	this._name = name;
};
Person.prototype.getName = function(){
	return this._name;
}
```

- 위 Person 인스턴스는 `__proto__` 프로퍼티를 통해 getName을 호출할 수 있다.
- instacne의 `__proto__` 가 Constructor의 prototype 프로퍼티를 참조하므로 결국 둘은 같은 객체를 바라본다.

```jsx
var jaejun = new Person('JaeJun');
jaejun.__proto__.getName(); // undefined

Person.prototype === jaejun.__proto__ // true
```

- 메서드 호출 결과로 왜 undefined가 나왔을까. 먼저 에러가 발생하지 않았다는 점을 보자. 어떤 변수를 실행해 undefined가 나왔다는 것은 이 변수가 호출할 수 있는 함수에 해당한다는 것을 의미한다. 만약 실행할 수 없는, 즉 함수가 아닌 다른 데이터 타입이었다면 TypeError가 발생했을 것이다. 그런데 값이 에러가 아닌 다른 값이 나왔으니까 getName이 실제로 실행됐음을 알 수 있고, 이로부터 getName이 함수라는 것이 입증되었다.
- this.name 값을 리턴하는 내용으로 구성돼있다. 그렇다면 this에 원래의 의도와는 다른 값이 할당된 것 아닐까 라는 의심을 가져볼 수 있다. 문제는 바로 this에 바인딩된 대상이 잘못 지정된 것이다.
- 어떤 함수를 메서드로서 호출할 때는 메서드명 바로 앞의 객체가 곧 this가 된다고 했다. 즉 `jaejun.__proto__.getName()` 에서 getName 함수 내부에서의 this는 `jaejun.__proto__` 라는 객체가 되는 것이다. 이 객체 내부에는 name 프로퍼티가 없으므로 찾고자 하는 식별자가 정의돼 있지 않을 때는 Error 대신 undefined가 반환된다라는 자바스크립트 규약에 의해 undefined가 반환된 것이다.

```jsx
var jaejun = new Person('JaeJun');
jaejun.__proto__._.name = 'LeeJaeJun';

jaejun.__proto__.getName(); // 'LeeJaeJun';
```

- 관건의 this이다. this를 인스턴스로 할 수 있다면 좋겠다. 그 방법은 `__proto__` 없이 인스턴스에서 곧바로 메서드를 쓰는 것이다.

```jsx
var jaejun = new Person('JaeJun');
jaejun.getName(); // JaeJun
```

- `__proto__` 를 빼면 this는 instance가 되는게 맞지만, 이대로 메서드가 호출되고 심지어 원하는 값이 나오는 건 좀 이상하다. 이는 `__proto__` 가 **생략 가능**한 프로퍼티이기 때문이다.
- new 연산자로 Constructor를 호출하면 Instance가 만들어지는데, 이 instance의 생략 가능한 프로퍼티인 `__proto__` 는 Constructor의 prototype을 참조한다.
- 자바스크립트는 함수에 자동으로 객체인 prototype 프로퍼티를 생성해 놓는다. 해당 함수를 생성자 함수로서 사용할 경우, 즉 new 연산자와 함께 함수를 호출할 경우, 그로부터 생성된 인스턴스에는 숨겨진 프로퍼티인 `__proto__` 가 자동으로 생성되며, 이 프로퍼티는 생성자 함수의 prototype 프로퍼티를 참조한다.
- `__proto__` 프로퍼티는 생략 가능하도록 구현돼 있기 때문에 생성자 함수의 prototype에 어떤 메서드나 프로퍼티가 있다면 인스턴스에서도 마치 자신의 것처럼 해당 메서드나 프로퍼티에 접근할 수 있게 된다.

```jsx
var Constructor = function(name) {
	this.name = name;
};

Constructor.prototype.method1 = function() {};
Constructor.prototype.property1 = 'Constructor Prototype Property';

var instance = new Constructor('Instance');
console.dir(Constructor);
console.dir(instance);

var arr = [1,2];
console.dir(arr);
console.dir(Array);
```

- Array를 new 연산자와 함께 호출해서 인스턴스를 생성하든, 그냥 배열 리터럴을 생성하든, 어쨋든 instance인 [1,2]가 만들어진다. 이 인스턴스의 `__proto__` 은 Array.prototype을 참조하는데, `__proto__` 가 생략 가능하도록 설계돼 있기 때문에 인스턴스가 push, pop, forEach등의 메서드를 마치 자신의 것처럼 호출할 수 있다.
- 한편 Array의 prototype 프로퍼티 내부에 있지 않은 from, isArray 등의 메서드들은 인스턴스가 직접 호출할 수 없을 것으로 이들은 Array 생성자 함수에서 직접 접근해야 실행이 가능하다.

```jsx
var arr = [1, 2];
arr.forEach(function () {}); // (O)
Array.isArray(arr); // (O) true
arr.isArray(); // (X) TypeError: arr.isArray is not a function
```

### 🌻 1-2. constructor 프로퍼티

- 생성자 함수의 프로퍼티인 prototype 객체 내부에는 constructor라는 프로퍼티가 있다. 인스턴스의 `__proto__` 객체 내부에도 마찬가지이다. 이 프로퍼티는 단어 그대로 원래의 생성자 함수(자기 자신)를 참조한다.

```jsx
var arr = [1, 2];
Array.prototype.constructor === Array // true
arr.__proto__.constructor === Array // true
arr.constructor === Array // true

var arr2 = new arr.constructor(3, 4);
console.log(arr2); // [3, 4];
```

- 인스턴스의 `__proto__` 가 생성자 함수의 prototype 프로퍼티를 참조하며 `__proto__` 가 생략 가능하기 때문에 인스턴스에서 직접 constructor에 접근할 수 있는 수단이 생긴 것이다.
- constructor는 읽기 전용 속성이 부여된 예외적인 경우(기본형 리터럴 변수 - number, string, boolean)를 제외하고는 값을 바꿀 수 있다.

```jsx
var NewConstructor = function() {
	console.log('this is new constructor');
}

var dataTypes = [
	1, // Number & false
	'test', // String & false
	true, // Boolean & false
	{}, // NewConstructor & false
	[], // NewConstructor & false
	function () {}, // NewConstructor & false
	/test/, // NewConstructor & false
	new Number(), // NewConstructor & false
	new String(), // NewConstructor & false
	new Boolean, // NewConstructor & false
	new Object(), // NewConstructor & false
	new Array(), // NewConstructor & false
	new Function(), // NewConstructor & false
	new RegExp(), // NewConstructor & false
	new Date(), // NewConstructor & false
	new Error() // NewConstructor & false
];

dataTypes.forEach(function (d) {
	d.constructor = NewConstructor;
	console.log(d.constructor.name, '&', d instanceof NewConstructor);
});
```

- 모든 데이터가 d instanceof NewConstructor 명령에 대해 false를 반환한다. 이로부터 constructor를 변경하더라도 참조하는 대상이 변경될 뿐 이미 만들어진 인스턴스의 원형이 바뀐다거나 데이터 타입이 변하는 것은 아님을 알 수 있다. 어떤 인스턴스의 생성자 정보를 알아내기 위해 constructor 프로퍼티에 의존하는 게 항상 안전하지는 않은 것이다.

```jsx
var Person = function(name) {
	this.name = name;
};

var p1 = new Person('사람1'); // { name: "사람1" } true
var p1Proto = Object.getPrototypeOf(p1);
var p2 = new Person.prototype.constructor('사람2'); // { name: "사람2" } true
var p3 = new p1Proto.constructor('사람3'); // { name: "사람3" } true
var p4 = new p1.__proto__.constructor('사람4'); // { name: "사람4" } true
var p5 = new p1.constructor('사람5'); // { name: "사람5" } true

[p1, p2, p3, p4, p5].forEach(function (p) {
	console.log(p, p instanceof Person);
});
```

- 각 줄은 모두 동일한 대상을 가리킨다.

```jsx
[Constructor]
[instance].__proto__.constructor
[instance].constructor
Object.getPrototypeOf([instance]).constructor
[Constructor].prototype.constructor
```

- 각 줄은 모두 동일한 객체(prototype)에 접근할 수 있다.

```jsx
[Constructor].prototype
[instance].__proto__
[instance]
Object.getPrototypeOf([instance])
```

## 🍄 2. 프로토타입 체인

### 🌻 2-1. 메서드 오버라이드

```jsx
var Person = function(name) {
	this.name = name;
};

Person.prototype.getName = function() {
	return this.name;
};

var iu = new Person('지금');
iu.getName = function() {
	return '바로' + this.name;
};

console.log(iu.getNmae()); // 바로 지금
```

- 여기서 일어난 현상을 메서드 오버라이드라고 한다. 메서드 위에 메서드를 덮어씌웠다는 표현이다. 원본을 제거하고 다른 대상으로 교체하는 것이 아니라 원본이 그대로 있는 상태에서 다른 대상을 그 위에 얹는 이미지를 떠올리면 정확하다.
- 자바스크립트 엔진이 getName이라는 메서드를 찾는 방식은 가장 가까운 대상인 자신의 프로퍼티를 검색하고, 없으면 그 다음으로 가까운 대상인 `__proto__` 를 검색하는 순서로 진행된다.

```jsx
console.log(iu.__proto__.getName()); // undefined
```

- this가 prototype 객체를 가리키는데 prototype 상에는 name 프로퍼티가 없기 때문에 undefined가 나온다.

```jsx
Person.prototype.name = "이지금";
console.log(iu.__proto__.getName()); // 이지금
```

- this가 prototype을 바라보고 있는데 이걸 인스턴스를 바라보도록 바꿔주면 된다. call이나 apply로 해결 가능하다.

```jsx
console.log(iu.__proto__.getName.call(iu)); // 지금
```

- 일반적으로 메서드가 오버라이드된 경우에는 자신으로부터 가장 가까운 메서드에만 접근할 수 있지만, 그 다음으로 가까운 `__proto__` 의 메서드도 우회적인 방법을 통해서이긴 하지만 접근이 불가능한 것은 아니다.

### 🌻 2-2. 프로토타입 체인

```jsx
console.dir({a:1});
```

- 첫 줄을 통해 Object의 인스터인스임을 알 수 있고, 프로퍼티 a의 값 1이 보인다. `__proto__` 내부에는 hasOwnProperty, isPrototypeOf, toLocaleString, toString, valueOf 등의 메서드가 보인다.

```jsx
console.dir([1,2]);
```

- 배열 리터럴의 `__proto__` 에는 pop, push 등의 익숙한 배열 메서드 및 constructor가 있다. 추가로 이 `__proto__` 안에는 또다시 `__proto__` 가 등장한다. 이유는 바로 prototype 객체가 ‘객체’이기 때문이다. 기본적으로 모든 객체의 `__proto__` 에는 Object.prototype이 연결된다. prototype 객체도 예외가 아니다.

```jsx
var arr = [1, 2];
arr(.__proto__).push(3);
arr(.__proto__)(.__proto__).hasOwnProperty(2); // true
```

- 어떤 데이터의 `__proto__` 프로퍼티 내부에 다시 `__proto__` 프로퍼티가 연쇄적으로 이어진 것을 **프로토타입 체인**이라 하고, 이 체인을 따라가며 검색하는 것을 **프로토타입 체이닝**이라고 한다.
- 프로토타입 체이닝은 메서드 오버라이드와 동일한 맥락이다. 어떤 메서드를 호출하면 자바스크립트 엔진은 데이터 자신의 프로퍼티들을 검색해서 원하는 메서드가 있으면 그 메서드를 실행하고, 없으면 `__proto__` 를 검색해서 있으면 그 메서드를 실행하고, 없으면 다시 `__proto__` 를 검색해서 실행하는 식으로 진행한다.

```jsx
var arr = [1, 2];
Array.prototype.toString.call(arr); // 1, 2
Object.prototype.toString.call(arr); // [object Array]
arr.toString(); // 1, 2

arr.toString = function() {
	return this.join('_');
};
arr.toString(); // 1_2
```

- arr 변수는 배열이므로 arr.__proto__는 Array.prototype을 참조하고, Array.prototype은 객체이므로 Array.prototype.__proto__는 Object.prototype을 참조할 것이다.
- toString이라는 이름을 가진 메서드는 Array.prototype뿐 아니라 Object.prototype에도 있다.

## 🌻 2-3. 객체 전용 메서드의 예외사항

- 어떤 생성자 함수이든 prototype은 반드시 객체이기 때문에 Object.prototype이 언제나 프로토타입 체인의 최상단에 존재하게 된다. 따라서 객체에서만 사용할 메서드는 다른 여느 데이터 타입처럼 프로토타입 객체 안에 정의할 수가 없다. 객체에서만 사용할 메서드를 Object.prototype 내부에 정의한다면 다른 데이터 타입도 해당 메서드를 사용할 수 있게 되기 때문이다.

```jsx
Object.prototype.getEntries = function() {
	var res = [];
	for(var prop in this){
		if(this.hasOwnProperty(prop)) {
			res.push([prop, this[prop]]);
		}
	}
	return res;
}

var data = [
	['object', { a: 1, b: 2, c: 3 }], // [["a",1], ["b", 2], ["c",3]]
	['number', 345], // []
	['string', 'abc'], // [["0","a"], ["1","b"], ["2","c"]]
	['boolean', false], // []
	['func', function () {}], // []
	['array', [1,2,3]] // [["0", 1], ["1", 2], ["2", 3]]
];

data.forEach(function (datum) {
	console.log(datum[1].getEntries());
});
```

- 1번째 줄에서는 객체에서만 사용할 의도로 getEntries라는 메서드를 만들었다. 각 데이터마다 getEntries를 실행해보니, 모든 데이터가 오류 없이 결과를 반환하고 있다. 원래 의도라면 객체가 아닌 다른 데이터 타입에 대해서는 오류를 던지게끔 돼야 할 텐데, 어느 데이터 타입이건 거의 무조건 프로토타입 체이닝을 통해 getEntries 메서드에 접근할 수 있으니 그렇게 동작하지 않는 것이다.
- 이 같은 이유로 객체만을 대상으로 동작하는 객체 전용 메서드들은 부득이 Object.prototype이 아닌 Object에 스태틱 메서드로 부여할 수 밖에 없었다. 또한 생성자 함수인 Object와 인스턴스인 객체 리터럴 사이에는 this를 통한 연결이 불가능하기 때문에 여느 전용 메서드처럼 메서드명 앞의 대상이 곧 this가 되는 방식 대신 this의 사용을 포기하고 대상 인스턴스를 인자로 직접 주입해야 하는 방식으로 구현되어 있다.
- 만약 객체 메서드에 대해서도 다른 데이터 타입과 마찬가지의 규칙을 적용할 수 있었다면, 예를 들어 Object.freeze(instance)의 경우 instance.freeze() 처럼 표현할 수 있었을 것이다.
- 객체 한정 메서드들을 Object.prototype이 아닌 Object에 직접 부여할 수밖에 없었던 이유는 Object.prototype이 여타의 참조형 데이터뿐 아니라 기본형 데이터조차 `__proto__` 에 반복 접근함으로써 도달할 수 있는 최상위 존재이기 때문이다.
- 반대로 같은 이유에서 Object.prototype에는 어떤 데이터에서도 호라용할 수 있는 범용적인 메서드들만 있다. toString, hasOwnProperty, valueOf, isPrototypeOf 등은 모든 변수가 마치 자신의 메서드인 것처럼 호출할 수 있다.

### 🌻 2-4. 다중 프로토타입 체인

- 자바스크립트의 기본 내장 데이터 타입들은 모두 프로토타입 체인이 1단계(객체)이거나 2단계(나머지)로 끝나는 경우만 있었지만 사용자가 새롭게 만드는 경우에는 그 이상도 얼마든지 가능하다.

```jsx
var Grade = function () {
	var args = Array.prototype.slice.call(arguments);
	for(var i = 0; i < args.length; i++) {
		this[i] = args[i];
	}
	this.length = args.length;
};

var g = new Grade(100, 80);
```

- 변수 g는 Grade의 인스턴스를 바라본다. Grade의 인스턴스는 여러 개의 인자를 받아 각각 순서대로 인덱싱해서 저장하고 length 프로퍼티가 존재하는 등으로 배열의 형태를 지니지만, 배열 메서드를 사용할 수는 없는 유사 배열 객체이다. 인스턴스에서 배열 메서드를 직접 쓸 수 있게끔 하기 위해서는 g.__proto__ 즉 Grade.prototype이 배열의 인스턴스를 바라보게 하면 된다.

```jsx
Grade.prototype = [];
```

```jsx
console.log(g); // Grade(2) [100, 80]
g.pop();
console.log(g); // Grade(1) [100]
g.push(90);
console.log(g); // Grade(2) [100, 90]
```

- g 인스턴스의 입장에서는 프로토타입 체인에 따라 g 객체 자신이 지니는 멤버, Grade의 prototype에 있는 멤버, Array.prototype에 있는 멤버, 끝으로 Object.prototype에 있는 멤버에까지 접근할 수 있게 되었다.