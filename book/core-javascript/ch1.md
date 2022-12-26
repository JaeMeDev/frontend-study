# 🌸 Chapter 1: 데이터 타입

## 💥 1. 데이터 타입의 종류

- 자바스크립트의 데이터 타입에는 크게 두 가지가 있다. 기본형(원시형)과 참조형(객체형)이 있다.
- 기본형에는 숫자, 문자열, 불리언, null, undefined와 ES6에서 등장한 심볼이 있다.
- 참조형에는 객체, 배열, 함수, 날짜, 정규표현식과 ES6에서 등장한 Map, Set, WeackSet이 있다.
- 기본형과 참조형의 차이점은 기본형은 값이 담긴 주솟값을 바로 복제하는 반면 참조형은 값이 담긴 주솟값들로 이루어진 묶음을 가리키는 주솟값을 복제한다.

## 💥 2. 데이터 타입에 관한 배경지식

### 🔥 2-1. 메모리와 데이터

- C/C++, Java 같은 정적 타입 언어는 메모리 낭비를 최소화하기 위해 데이터 타입별로 할당할 메모리 영역을 두었지만, JavaScript 는 동적 타입 언어로 메모리 관리에 대한 압박에서 자유로워 졌다.
- JavaScript는 메모리 공간을 더 넉넉하게 할당했으며 숫자의 경우 정수형인지, 부동소수형인지 구분하지 않고 8바이트로 확보하게 함으로써 장적타입언어에서의 형변환을 걱정해야 하는 상황이 훨씬 줄었다.
- 모든 데이터는 바이트 단위의 식별자, 정확하게는 **메모리 주솟값**을 통해 서로 구분하고 연결할 수 있다.

### 🔥 2-2. 식별자와 변수

- **변수**는 변할 수 있는 수로 값이 반드시 숫자여야 하는 것은 아니다. 변수는 변할 수 있는 무언가로 여기서 무언가는 **데이터**를 말한다.
- **식별자**는 어떤 데이터를 식별하는데 사용하는 이름, 즉 **변수명**이다.

## 💥 3. 변수 선언과 데이터 할당

### 🔥 3-1. 변수 선언

```javascript
var a;
```

- **변수**는 변경 가능한 데이터가 담길 수 있는 공간 또는 그릇이라고 생각하면 된다.
- 해당 예제는 변할 수 있는 데이터를 만든다. 이 데이터의 식별자는 `a`이다.
- `a`를 선언하게 되면 컴퓨터는 메모리에서 비어있는 공간 하나를 확보하고. 그 공간의 이름을 `a`라고 지정한다.
- `a`에게 접근하면 컴퓨터는 메모리에서 `a`라는 이름을 가진 주소를 검색해 해당 공간에 담긴 데이터를 반환한다.

### 🔥 3-2. 데이터 할당

```javascript
var a; // 변수 a 선언
a = 'abc'; // 변수 a에 데이터 할당

var a = 'abc'; // 변수 선언과 할당을 한 문장으로 표현
```

- 선언과 할당을 두 문장으로 나누어 명령하든 한 문장으로 명령하든, 자바스크립트 엔진은 같은 동작을 수행한다.
- 자바스크립트는 실제로 해당 위치에 값을 저장하지는 않는다. 데이터를 할당하게 되면 데이터를 저장하기 위한 별도의 메모리 공간을 다시 확보해서 데이터를 저장하고, 그 주소를 변수 영역에 저장하는 식으로 이루어진다.
- 변수 영역에 값을 직접 대입하지 않고 굳이 번거롭게 한 단계를 거치는 이유는 데이터 변환을 자유롭게 할 수 있게 함과 동시에 메모리를 더욱 효율적으로 관리하기 위해서이다.
- 만약 미리 확보한 공간 내에서만 데이터 변환을 할 수 있다면 변환된 데이터를 다시 저장하기 위해서는 확보된 공간을 변환된 데이터 크기에 맞게 늘리는 작업이 선행되어야 한다. 
- 이러한 작업은 컴퓨터가 처리해야 할 연산이 많아질 수 밖에 없어 비효율적이다. 결국 효율적으로 데이터 변환을 처리하려면 변수와 데이터를 별도의 공간에 나누어 저장하는 것이 최적이다.

## 💥 4. 기본형 데이터와 참조형 데이터

### 🔥 4-1. 불변값

- 변수와 상수를 구분하는 성질은 ‘변경 가능성’이다. 바꿀 수 있으면 변수 없으면 상수이다.
- 변수와 상수를 구분 짓는 변경 가능성의 대상은 **변수 영역 메모리**이다. 한 번 데이터 할당이 이뤄진 변수 공간에 다른 데이터를 재할당 할 수 있는지 여부가 관건이다.
- 불변성 여부를 구분할 때의 변경 가능성의 대상은 **데이터 영역 메모리**이다.

```javascript
var a = 'abc';
a = a + 'def';

var b = 5;
var c = 5;
b = 7;
```

- 변수 a에 문자열 ‘abc’를 할당했다가 뒤에 ‘def’를 추가하면 기존의 ‘abc’가 ‘abcdef’가 되는 것이 아닌 새로운 문자열 ‘abcdef’를 만들어 그 주소를 변수 a에 저장한다.
- 컴퓨터는 변수에 값을 할당하게 되면 데이터 영역에서 먼저 찾고, 없으면 그제서야 데이터 공간을 하나 만들어 저장한다. 만약 있으면 이미 만들어놓은 값이 있으니 그 주소를 재활용한다.
- 문자열 값도 한 번 만든 값을 바꿀 수 없고, 숫자 값도 다른 값으로 변경할 수 없다. 변경은 새로 만드는 동작을 통해서만 이뤄진다. 이것이 불변값의 성질이다.
- 한 번 만들어진 값은 가비지 컬렉팅을 당하지 않는 한 영원히 변하지 않는다.

### 🔥 4-2. 가변값

- 참조형 데이터는 모두 가변값일 것 같은 느낌이 들지만 기본적인 성질은 가변값인 경우가 많지만 설정에 따라 변경 불가능한 경우도 있고, 아예 불변값으로 활용하는 방안도 있다.

```javascript
var obj1 = {
	a: 1,
	b: 'bbb'
};
```

- 기본형 데이터와의 차이는 '객체의 변수(프로퍼티) 영역'이 별도로 존재한다는 점이다.
- 객체가 별도로 할애할 영역은 변수 영역일 뿐 '데이터 영역'은 기존의 메모리 공간을 그대로 활용하고 있다. 데이터 영역에 저장된 값은 모두 불변값이다.
- 그러나 변수에는 다른 값을 얼마든지 대입할 수 있다. 이 부분 때문에 가변값이라고 하는 것이다.

### 🔥 4-3. 변수 복사 비교

- 기본형 데이터와 참조형 데이터의 차이는 복사를 할 때 변화로 확인할 수 있다.

```javascript
var a = 10;
var b = a;

var obj1 = { c: 10, d: 'ddd' };
var obj2 = obj1;

b = 15;
obj2.c = 20;

console.log(a !== b); // false
console.log(obj1 === obj2); // true
```

- 어떤 데이터 타입이든 변수에 할당하기 위해서는 주솟값을 복사해야 하기 때문에, 엄밀히 따지면 자바스크립트의 모든 데이터 타입은 참조형 데이터일 수 밖에 없다. 다만 기본형은 주솟값을 복사하는 과정이 한 번만 이뤄지고, 참조형은 한 단계를 더 거치게 된다는 차이점이 있다.

```javascript
var a = 10;
var b = a;

var obj1 = { c: 10, d: 'ddd' };
var obj2 = obj1;

b = 15;
obj2 = { c: 20, d: 'ddd' };

console.log(a !== b); // false
console.log(obj1 === obj2); // false
```

- obj2에 새로운 객체를 할당함으로써 값을 직접 변경하게 되면 메모리 데이터 영역의 새로운 공간에 새 객체가 저장되고 그 주소를 변수 영역의 obj2에 저장한다. 이렇게 저장하면 객체에 대한 변경임에도 값이 달라질 수 있다.
- 즉 참조형 데이터가 '가변값'이라고 설명할 때의 '가변'은 참조형 데이터 자체를 변경할 경우가 아니라 그 내부의 프로퍼티를 변경할 때만 성립한다.

## 💥 5. 불변 객체

### 🔥 5-1. 불변 객체를 만드는 간단한 방법

- 불변 객체는 최근 React, Vue, Angular 등의 라이브러리나 프레임워크에서 뿐만 아니라 함수형 프로그래밍, 디자인 패턴 등에서도 매우 중요한 기초가 되는 개념이다.
- 참조형 데이터의 가변은 데이터 자체가 아닌 프로퍼티를 변경할 때만 성립한다. 데이터 자체를 변경하고자 하면 기본형 데이터와 마찬가지로 기존 데이터는 변하지 않는다.
- 내부 프로퍼티를 변경할 필요가 있을 때마다 새로운 객체를 만들어 재할당하기로 규칙을 정하거나 자동으로 새로운 객체를 만드는 도구를 활용한다면 객체 역시 불변성을 확보할 수 있을 것이다.
- 불변 객체는 전달받은 객체에 변경을 가하더라도 원본 객체는 변하지 않아야 하는 경우에서 사용된다.

```javascript
var user = {
	name: "Jae Jun",
	gender: "male"
};

var changeName = function(user, newName){
	var newUser = user;
	newUser.name = newName;
	return newUser;
}

var user2 = changeName(user, "Lee");

if(user !== user2){
	console.log("유저 정보가 변경되었다.");
}

console.log(user.name, user2.name); // Lee Lee
console.log(user === user2); // true
```

- 위 코드는 객체의 가변성으로 인한 문제점을 보여주는 간단한 예시이다. 코드를 고쳐보자.

```javascript
var user = {
	name: "Jae Jun",
	gender: "male"
};

var changeName = function(user, newName){
	return {
		name: newName,
		gender: user.gender
	}
}

var user2 = changeName(user, "Lee");

if(user !== user2){
	console.log("유저 정보가 변경되었다."); // 출력됨!
}

console.log(user.name, user2.name); // JaeJun Lee
console.log(user === user2); // false
```

- changeName 함수는 새로운 객체를 만들면서 변경할 필요가 없는 기존 객체의 프로퍼티를 하드코딩으로 진행하였다. 이런 식으로는 대상 객체에 변경해야 할 정보가 많을수록 사용자가 입력하는 수고가 늘어날 것이다.
- 대상 객체의 프로퍼티 개수에 상관 없이 모든 프로퍼티를 복사하는 함수를 만들어보자.

```javascript
// 얕은 복사
var copyObject = function (target){
	var result = {};
	for(var prop in target){
		result[prop] = target[prop];
	}
	return result;
}

var user = {
	name: "Jae Jun",
	gender: "male"
};

var user2 = copyObject(user);
user2.name = "Lee";

if(user !== user2){
	console.log("유저 정보가 변경되었다."); // 출력됨!
}

console.log(user.name, user2.name); // JaeJun Lee
console.log(user === user2); // false
```

- 얕은 복사 함수를 통해 간단하게 객체를 복사하고 내용을 수정하는데 성공했다.
- immutable.js, baobab.js 등의 라이브러리는 자바스크립트 내장 객체가 아닌 라이브러리 자체에서 불변성을 지닌 별도의 데이터 타입과 그에 따른 메서드를 제공해서 인기를 끌고 있다.

### 🔥 5-2. 얕은 복사와 깊은 복사

- 얕은 복사는 바로 아래 단계의 값만 복사하는 방법이고, 깊은 복사는 내부의 모든 값들을 하나하나 찾아서 전부 복사하는 방법이다.
- 이 말을 얕은 복사는 중첩된 객체에서 참조형 데이터가 저장된 프로퍼티를 복사할 때 그 주솟값만 복사한다는 의미이다. 그러면 해당 프로퍼티에 대해 원본과 사본이 모두 동일한 참조형 데이터 주소를 가리키게 되고 이런 현상으로 인해 사본을 바꾸면 원본도 바뀌고, 반대인 현상도 생기게 된다.

```javascript
var user = {
    name: "JaeJun",
    urls: {
        github: 'http://github.com/jaejun',
        blog: 'http://blog.com'
    }
}

var user2 = copyObject(user);

user2.name = "Lee";
console.log(user.name === user2.name); // false

user.urls.github = 'http://gitlab.com/jaejun';
console.log(user.urls.github === user2.urls.github); // true
```

- user 객체에 직접 속한 프로퍼티에 대해서는 복사해서 완전히 새로운 데이터가 만들어진 반면 한 단계 더들어간 urls 내부 프로퍼티들은 기존 데이터를 그대로 참조한다. 

```javascript
var user2 = copyObject(user);
user2.urls = copyObject(user.urls);

user.urls.github = 'http://gitlab.com/jaejun';
console.log(user.urls.github === user2.urls.github); // false
```

- 어떤 객체를 복사할 때 객체 내부의 모든 값을 복사해서 완전히 새로운 데이터를 만들고자 할 때, 객체의 프로퍼티 중에서 그 값이 기본형 데이터일 겨우에는 그대로 복사하면 되지만 참조형 데이터는 다시 그 내부의 프로퍼티들을 복사해야 한다.
- 이 과정을 참조형 데이터가 있을 때마다 재귀적으로 수행해야만 비로소 깊은 복사가 되는 것이다.

```javascript
var copyObjectDeep = function(target){
	var result = {};
	if(typeof target === "object" && target !== null){
		for(var prop in target){
			result[prop] = copyObjectDeep(target[prop]);
		}
	}else {
		result = target;
	}
	return result;
}
```

- hasOwnProperty 메서드를 활용해 프로토타입 체이닝을 통해 상속된 프로퍼티를 복사하지 않게끔 할 수도 있다. ES5의 getter/setter를 복사하는 방법은 안타깝게도 ES6의 Object.getOwnPropertyDescriptor 또는 ES2017의 Object.getOwnPropertyDescriptors 외에는 마땅한 방법이 없다.
- 간단하게 깊은 복사를 처리할 수 있는 방법이 하나 더 있다. 객체를 JSON 문법으로 표현된 문자열로 전환했다가 다시 JSON 객체로 바꾸는 것이다. 다만  메서드(함수)나 숨겨진 `__proto__` 나 `getter/setter` 등과 같이 JSON으로 변경할 수 없는 프로퍼티들은 모두 무시한다. httpRequest로 받은 데이터를 저장한 객체를 복사할 때 등 순수한 정보만 다룰 때 활용하기 좋은 방법이다.

```javascript
var copyObjectViaJSON = function(target){
	return JSON.parse(JSON.stringify(target));
}
```

## 💥 6. undefined와 null

- 자바스크립트에서 ‘없음’을 나타내는 값이 두 가지가 있다. 바로 undefined와 null이다.
- undefined는 사용자가 명시적으로 지정할 수 있지만 값이 존재하지 않을 때 자바스크립트 엔진이 자동으로 부여하는 경우도 있다. 자바스크립트 엔진은 사용자가 응당 어떤 값을 지정할 것이라고 예상되는 상황임에도 실제로는 그렇게 하지 않았을 때 undefined를 반환한다.
- 값을 대입하지 않은 변수, 즉 데이터 영역의 메모리 주소를 지정하지 않은 식별자에 접근할 때. 객체 내부의 존재하지 않는 프로퍼티에 접근하려고 할 때. return 문이 없거나 호출되지 않는 함수의 실행결과.

```javascript
var a;
console.log(a); // undefined. 값을 대입하지 않는 변수에 접근

var obj = {a: 1};
console.log(obj.a); // 1
console.log(obj.b); // undefined. 존재하지 않는 프로퍼티에 접근

var func = function() {};
var c = func(); // 반환 값이 없으면 undefined를 반환한 것으로 간주.
console.log(c); // undefined
```

- 값을 대입하지 않은 경우에 대해 배열의 경우 조금 특이한 동작을 확인할 수 있다.

```javascript
var arr1 = [];
arr1.length = 3;
console.log(arr1); // [empty x 3]

var arr2 = new Array(3);
console.log(arr2); // [empty x 3]

var arr3 = [undefined, undefined, undefined];
console.log(arr3);
```

- arr1, arr2는 배열에 3개의 빈 요소를 확보했지만 확보된 각 요소에는 문자 그대로 어떤 값도, 심지어 undefined 조차도 할당돼 있지 않음을 의미한다.
- 이처럼 비어있는 요소와 undefined를 할당한 요소는 출력 결과부터 다르다.
- 비어있는 요소는 순회와 관련된 많은 배열 메서드들의 순회 대상에서 제외된다.

```javascript
var arr1 = [undefined, 1];
var arr2 = [];
arr2[1] = 1;

arr1.forEach(function(v, i) {console.log(v, i)}); // undefined 0 / 1 1
arr2.forEach(function(v, i) {console.log(v, i)}); // 1 1

arr1.map(function(v, i) {return v + i}); // [NaN, 2]
arr2.map(function(v, i) {return v + i}); // [empty, 2]

arr1.filter(function (v) {return !v; }); // [undefined]
arr2.filter(function (v) {return !v; }); // []

arr1.reduce(function(p,c,i) {return p + c + i;}, ''); // undefined011
arr2.reduce(function(p,c,i) {return p + c + i;}, ''); // 11
```

- undefined를 할당한 arr1은 배열의 모든 요소를 순회해서 결과를 출력하지만 arr2는 각 메서드들이 비어 있는 요소에 대해서 어떠한 처리도 하지 않고 건너뛴다.
- 이러한 동작이 배열에서만 발견할 수 있는 특별한 현상인 것 같지만 사실은 배열도 객체임을 생각해보면 지극히 자연스러운 현상이다. 존재하지 않는 프로퍼티에 대해 순회할 수 없는 것은 당연하기 때문이다. 배열은 무조건 length 프로퍼티의 개수만큼 빈 공간을 확보하고 각 공간에 인덱스를 이름으로 지정할 것 같지만 실제로는 객체와 마찬가지로 특정 인덱스에 값을 지정할 때 비로소 빈 공간을 확보하고 인덱스를 이름으로 지정하고 데이터의 주솟값을 저장하는 등의 동작을 한다.
- var 변수는 LE가 활성될 때 생성되면서 동시에 undefined로 초기화 되는 반면 ES6에서 등장한 let, const는 undefined를 할당하지 않은 채로 초기화를 마치며, 이후 특정 값을 할당하기 전까지는 해당 변수에 접근할 수 없다.
- 위의 배열 예제로 혼란 스러울 수 있지만 이를 해결하는 방법은 undefined를 직접 할당하지 않으면 된다.
- 같은 의미를 가진 null이 있는데 굳이 undefined를 써야할 이유가 없다.
- null의 주의점으로는 typeof가 object라는 점이다. 이는 자바스크립트 버그이다.
