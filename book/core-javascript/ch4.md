# 🌸 Chapter 4: 콜백 함수

## 🍄 1. 콜백 함수란?

- 콜백 함수(callback function)는 다른 코드의 인자로 넘겨주는 함수이다. 콜백 함수를 넘겨받은 코드는 이 콜백 함수를 필요에 따라 적절한 시점에 실행한다.
- 콜백 함수는 **제어권**과 관련이 깊다.
- callback은 ‘부르다’, ‘호출(실행)하다’는 의미인 call과, ‘뒤돌아오다’, ‘되돌다’는 의미인 back의 합성어로 ‘되돌아 호출해달라’는 명령이다.
- 콜백 함수는 다른 코드(함수 또는 메서드)에게 인자로 넘겨줌으로써 그 제어권도 함께 위임한 함수이다. 콜백 함수를 위임받은 코드는 자체적인 내부 로직에 의해 이 콜백 함수를 적절한 시점에 실행할 것이다.

## 🍄 2. 제어권

### 🌻 2-1. 호출 시점

```jsx
var count = 0;
var timer = setInterval(function () {
	console.log(count);
	if(++count > 4) clearInterval(timer);
});
```

- 1번째 줄에서 count 변수를 선언하고 0을 할당했다. 2번째 줄에서는 timer 변수를 선언하고 여기에 setInterval을 실행한 결과를 할당했다. setInterval을 호출할 때 두개의 매개변수를 전달했는데, 그중 첫 번째는 익명 함수이고 두 번째는 300이라는 숫자이다.

```jsx
var intervalID = scope.setInterval(func, delay[, param1, param2, ...]);
```

- 우선 scope에는 Window 객체 또는 Worker의 인스턴스가 들어올 수 있다. 두 객체 모두 setInterval 메서드를 제공하기 때문이다. 일반적인 브라우저 환경에서는 window를 생략해서 함수처럼 사용 가능할 것이다.
- 매개변수로는 func, delay 값을 받으시 전달해야 하고, 세 번째 매개변수부터는 선택적이다. func는 함수이고, delay는 밀리초(ms) 단위의 숫자이며, 나머지는 func 함수를 실행할 때 매개변수로 전달할 인자이다. func에 넘겨준 함수는 매 delay 마다 실행되며, 그 결과 어떠한 값도 리턴하지 않는다.
- setInterval를 실행하면 반복적으로 실행되는 내용 자체를 특정할 수 있는 고유한 ID 값이 반환된다. 이를 변수에 담는 이유는 반복 실행되는 중간에 종료(clearInterval)할 수 있게 하기 위해서이다.

```jsx
var count = 0;
var cbFunc = function(){
	console.log(count);
	if(++count > 4) clearInterval(timer);
}

var timer = setInterval(cbFunc, 300);

// -- 실행 결과 --
// 0 (0.3초)
// 1 (0.6초)
// 2 (0.9초)
// 3 (1.2초)
// 4 (1.5초)
```

- timer 변수에는 setInterval의 ID 값이 담긴다. setInterval에 전달한 첫 번째 인자인 cbFunc 함수(콜백 함수)는 0.3초마다 자동으로 실행될 것이다.
- setInterval이라고 하는 ‘다른 코드’에 첫 번째 인자로서 cbFunc 함수를 넘겨주자 제어권을 넘겨받은 setInterval이 스스로의 판단에 따라 적절한 시점에(0.3초마다) 이 익명 함수를 실행했다. 이처럼 콜백 함수의 제어권을 넘겨받은 코드는 콜백 함수 호출 시점에 대한 제어권을 가진다.

### 🌻 2-2. 인자

```jsx
var newArr = [10, 20, 30].map(function (currentValue, index) {
	console.log(currentValue, index);
	return currentValue + 5;
});

console.log(newArr);

// -- 실행 결과 --
// 10 0
// 20 1
// 30 2
// [15, 25, 35]
```

```jsx
Array.prototype.map(callback[, thisArg])
callback: function(currentValue, index, array)
```

- map 메서드는 첫 번째 인자로 callback 함수를 받고, 생략 가능한 두 번째 인자로 콜백 함수 내부에서 this로 인식할 대상을 특정할 수 있다. thisArg를 생략할 경우에는 일반적인 함수와 마찬가지로 전역객체가 바인딩된다.
- map 메서드는 메서드의 대상이 되는 배열의 모든 요소들을 처음부터 끝까지 하나씩 꺼내어 콜백 함수를 반복 호출하고, 콜백 함수의 실행 결과들을 모아 새로운 배열을 만든다. 콜백 함수의 첫 번째 인자에는 배열의 요소 중 현재값이, 두 번째 인자에는 현재값의 인덱스가, 세 번째 인자에는 map 메서드의 대상이 되는 배열 자체가 담긴다.
- 제이쿼리의 메서드들은 기본적으로 첫 번째 인자에 index가, 두 번째 인자에 currentValue가 온다. 제이쿼리의 방식처럼 순서를 바꾸어 사용해보면 어떤일이 생길까?

```jsx
var newArr = [10, 20, 30].map(function (index, currentValue) {
	console.log(index, currentValue);
	return currentValue + 5;
});

console.log(newArr);

// -- 실행 결과 --
// 10 0
// 20 1
// 30 2
// [5, 6, 7]
```

- 사람은 단어로 접근하기 때문에 순서를 바꾸더라도 각 단어의 의미가 바뀌지 않으니까 문제 없을 것이라고 생각하기 쉽지만, 저 단어들은 사용자가 명명한 것 뿐이다. 컴퓨터는 그저 첫 번째, 두 번째의 순서에 의해서만 각각을 구분하고 인식한다.
- 원하는 시각에 알람이 울리는 결과를 얻기 위해서는 시계가 정한 규칙을 지켜야 하듯이 map 메서드를 호출해서 원하는 배열을 얻으려면 map 메서드에 정의된 규칙에 따라 함수를 작성해야 한다.
- 이처럼 콜백 함수의 제어권을 넘겨받은 코드는 콜백 함수를 호출할 때 인자에 어떤 값들을 어떤 순서로 넘길 것인지에 대한 제어권을 가진다.

### 🌻 2-3. this

- 콜백 함수도 함수이기 때문에 기본적으로 this가 전역객체를 참조하지만, 제어권을 넘겨받을 코드에서 콜백 함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조하게 된다.

```jsx
Array.prototype.map = function(callback, thisArg) {
	var mappedArr = [];
	for(var i = 0; i < this.length; i++){
		var mappedValue = callback.call(thisArg || window, this[i], i, this);
		mappedArr[i] = mappedValue;
	}
	return mappedArr;
}
```

- 메서드 구현의 핵심은 call/apply 메서드에 있다. this에는 thisArg 값이 있을 경우에는 그 값을, 없을 경우에는 전역객체를 지정하고, 첫 번째 인자에는 메서드의 this가 배열을 가리킬 것이므로 배열의 i번째 요소 값을, 두 번째 인자에는 i 값을, 세 번째 인자에는 배열 자체를 지정해 호출한다. 그 결과가 변수 mappedValue에 담겨 mappedArr의 i번째 인자에 할당된다.
- this에 다른 값이 담기는 이유는 바로 제어권을 넘겨받을 코드에서 call/apply 메서드의 첫 번째 인자에 콜백 함수 내부에서의 this가 될 대상을 명시적으로 바인딩하기 때문이다.

```jsx
setTimeout(function () { console.log(this); }, 300); // (1) Window { ... }

[1,2,3,4,5].forEach(function(x){
	console.log(this); // (2) Window { ... }
});

document.body.innerHTML += '<button id="a">클릭</button>';
document.body.querySelector('#a')
	.addEventListener('click', function(e) {
		console.log(this, e); // (3) <button id="a">클릭</button> MouseEvent ...
});
```

## 🍄 3. 콜백 함수는 함수다.

- 콜백 함수는 어떤 객체의 메서드를 전달하더라도 그 메서드는 메서드가 아닌 함수로서 호출된다.

```jsx
var obj = {
	vals: [1, 2, 3],
	logValues: function(v, i){
		console.log(this, v, i);
	}
};

obj.logValues(1, 2); // { vals: [1,2,3], logValues: f } 1 2
[4,5,6].forEach(obj.logValues); // Window { ... } 4 0
// Window { ... } 5 1
// Window { ... } 6 2
```

- obj를 this로 하는 메서드를 그대로 전달한 것이 아니라, obj.logValues가 가리키는 함수만 전달했다. 이 함수는 메서드로서 호출할 때가 아닌 한 obj와의 직접적인 연관이 없어진다.
- forEach에 의해 콜백이 함수로서 호출되고, 별도로 this를 지정하는 인자를 지정하지 않았으므로 함수 내부에서의 this는 전역객체를 바라보게 된다.
- 어떤 함수의 인자에 객체의 메서드를 전달하더라도 이는 결국 메서드가 아닌 함수일 뿐이다.

## 🍄 4. 콜백 함수 내부의 this에 다른 값 바인딩하기

- 전통적으로는 this를 다른 변수에 담아 콜백 함수로 활용할 함수에서는 this 대신 그 변수를 사용하게 하고, 이를 클로저로 만드는 방식이 많이 사용되었다.

```jsx
var obj1 = {
	name: 'obj1',
	func: function () {
		var self = this;
		return function(){
			console.log(self.name)(;
		};
	}
};

var callback = obj1.func();
setTimeout(callback, 1000);
```

- 이 방식은 실제로 this를 사용하지도 않을 뿐더러 번거롭기 그지 없다. 차라리 this를 아예 안쓰는 편이 더 낫겠다는 생각이 든다.

```jsx
var obj1 = {
	name: 'obj1',
	func: function(){
		console.log(obj1.name);
	}
};

setTimeout(obj1.func, 1000);
```

- 훨씬 갈결하고 직관적이지만 아쉬운 부분도 있다. 작성한 함수를 this를 이용해 다양한 상황에 재활용할 수 없게 되었다.

```jsx
var obj2 = {
	name: 'obj2',
	func: obj1.func
}

var callback2 = obj2.func();
setTimeout(callback2, 1500);

var obj3 = { name: 'obj3' };
var callback3 = obj1.func.call(obj3);
setTimeout(callback3, 2000);
```

- 다행히 이제는 전통적인 방식의 아쉬움을 보완하는 훌륭한 방법이 있다. 바로 ES5에서 등장한 bind 메서드를 이용하는 방법이다.

```jsx
var obj1 = {
	name: 'obj1',
	func: function() {
		console.log(this.name);
	}
};

setTimeout(obj1.func.bind(obj1), 1000);

var obj2 = {name: 'obj2'};
setTimeout(obj1.func.bind(obj2), 1500);
```

## 🍄 5. 콜백 지옥과 비동기 제어

- 콜백 지옥은 콜백 함수를 익명 함수로 전달하는 과정이 반복되어 코드의 들여쓰기 수준이 감당하기 힘들 정도로 깊어지는 현상으로 자바스크립트에서 흔히 발생하는 문제이다.
- 주로 이벤트 처리나 서버 통신과 같이 비동기적인 작업을 수행하기 위해 이런 형태가 자주 등장하곤 하는데, 가독성이 떨어질뿐더러 코드를 수정하기도 어렵다.
- **비동기**는 동기의 반대말이다. 동기적인 코드는 현재 실행 중인 코드가 완료된 후에야 다음 코드를 실행하는 방식이지만 비동기적인 코드는 현재 실행 중인 코드의 완료 여부와 무관하게 즉시 다음 코드로 넘어간다. CPU의 계산에 의해 즉시 처리가 가능한 대부분의 코드는 동기적인 코드이다.
- 사용자의 요청에 의해 특정 시간이 경과되기 전까지 어떤 함수의 실행을 보류한다거나(setTimeout), 사용자의 직접적인 개입이 있을 때 비로소 어떤 함수를 실행하도록 대기한다거나(addEventListener), 웹브라우저 자체가 아닌 별도의 대상에 무언가를 요청하고 그에 대한 응답이 왔을 때 비로소 어떤 함수를 실행하도록 대기하는 등(XMLHttpRequest), 별도의 **요청, 실행 대기, 보류** 등과 관련된 코드는 비동기적인 코드이다.

```jsx
setTimeout(function (name){
	var coffeeList = name;
	console.log(coffeList);

	setTimeout(function (name){
		coffeList += ', ' + name;
		console.log(coffeeList);

		setTimeout(function (name){
			coffeList += ', ' + name;
			console.log(coffeeList);
			
			setTimeout(function (name){
				coffeList += ', ' + name;
				console.log(coffeeList);
			}, 500, '카페라떼');
		}, 500, '카페모카');
	}, 500, '아메리카노');
}, 500, '에스프레소');
```

- 목적 달성에는 지장이 없지만 들여쓰기 수준이 과도하게 깊어졌을뿐더러 값이 전달되는 순서가 아래에서 위로 향하고 있어 어색하게 느껴진다. 가독성 문제와 어색함을 동시에 해결하는 가장 간단한(?) 방법은 익명의 콜백 함수를 모두 기명함수로 전환하는 것이다.

```jsx
var coffeeList = '';

var addEspresso = function (name) {
	coffeeList = name;
	console.log(coffeeList);
	setTimeout(addAmericano, 500, '아메리카노');
};
var addAmericano = function (name) {
	coffeeList += ', ' + name;
	console.log(coffeeList);
	setTimeout(addMocha, 500, '카페모카');
};
var addMocha = function (name) {
	coffeeList += ', ' + name;
	console.log(coffeeList);
	setTimeout(addLatte, 500, '카페라떼');
};
var addLatte = function (name) {
	coffeeList += ', ' + name;
	console.log(coffeeList);
};

setTimeout(addEspresso, 500, '에스프레소');
```

- 이 방식은 코드의 가독성을 높일뿐 아니라 함수 선언과 함수 호출만 구분할 수 있다면 위에서부터 아래로 순서대로 읽어내려가는 데 어려움이 없다. 또한 변수를 최상단으로 끌어올림으로써 외부에 노출되게 됐지만 전체를 즉시 실행 함수 등으로 감싸면 간단히 해결될 문제이다.
- 일회성 함수를 전부 변수에 할당하는 것은 코드명을 일일이 따라다녀야 하므로 오히려 헷갈릴 소지가 있다.
- 자바스크립트는 비동기적인 일련의 작업을 동기적으로, 혹은 동기적인 것처럼 보이게끔 처리해주는 장치를 마련하고자 끊임없이 노력했다. ES6에서는 Promise, Generator 등이 도입됐고, ES2017에서는 async/await가 도입됬다.

```jsx
new Promise(function (resolve){
	setTimeout(function(){
		var name = "에스프레스";
		console.log(name);
		resolve(name);
	}, 500);
}).then(function (prevName){
	return new Promise(function (resolve) {
		setTimeout(function(){
			var name = prevName + ', 아메리카노';
			console.log(name);
			resolve(name);
		}, 500);
	});
});

// ... 중복된 코드
```

- new 연산자와 함께 호출한 Promise의 인자로 넘겨주는 콜백 함수는 호출할 때 바로 실행되지만 그 내부에 resolve 또는 reject 함수를 호출하는 구문이 있을 경우 둘 중 하나가 실행되기 전까지는 다음(then) 또는 오류 구문(catch)으로 넘어가지 않는다. 따라서 비동기 작업이 완료될 때 비로소 resolve 또는 reject를 호출하는 방법으로 비동기 작업의 동기적 표현이 가능하다.

```jsx
var addCoffee = function (name) {
	return function (prevName) {
		return new Promise(function (resolve) {
			setTimeout(function () {
				var newName = prevName ? (prevName + ', ' + name) : name;
				console.log(newName);
				resolve(newName);
			}, 500);
		});
	};
};

addCoffee('에스프레소')()
	.then(addCoffee('아메리카노'))
	.then(addCoffee('카페모카'))
	.then(addCoffee('카페라떼'));
```

- 반복적인 내용을 함수화해서 더욱 짧게 표현할 수 있다.

```jsx
var addCoffee = function (prevName, name) {
	setTimeout(function () {
		coffeeMaker.next(prevName ? prevName + ', ' + name : name);
	}, 500);
};
var coffeeGenerator = function* () {
	var espresso = yield addCoffee('', '에스프레소');
	console.log(espresso);
	var americano = yield addCoffee(espresso, '아메리카노');
	console.log(americano);
	var mocha = yield addCoffee(americano, '카페모카');
	console.log(mocha);
	var latte = yield addCoffee(mocha, '카페라떼');
	console.log(latte);
};
var coffeeMaker = coffeeGenerator();
coffeeMaker.next();
```

- 6번째 줄의 `*` 이 붙은 함수가 바로 Generator 함수이다. Generator 함수를 실행하면 Iterator가 반환되는데 Iterator는 next라는 메서드를 가지고 있다. 이 next 메서드를 호출하면 Generator 함수 내부에서 가장 먼저 등장하는 yield에서 함수의 실행이 멈춘다. 이후 다시 next 메서드를 호출하면 앞서 멈췄던 부분부터 시작해서 그다음에 등장하는 yield에서 함수의 실행이 멈춘다. 비동기 작업이 완료되는 시점마다 next 메서드를 호출해준다면 Generator 함수 내부의 소스가 위에서부터 아래로 순차적으로 진행될 것이다.

```jsx
var addCoffee = function (name) {
	return new Promise(function (resolve) {
		setTimeout(function () {
			resolve(name);
		}, 500);
	});
};
var coffeeMaker = async function () {
	var coffeeList = '';
	var _addCoffee = async function (name) {
		coffeeList += (coffeeList ? ',' : '') + await addCoffee(name);
	};
	await _addCoffee('에스프레소');
	console.log(coffeeList);
	await _addCoffee('아메리카노');
	console.log(coffeeList);
	await _addCoffee('카페모카');
	console.log(coffeeList);
	await _addCoffee('카페라떼');
	console.log(coffeeList);
};
coffeeMaker();
```

- 비동기 작업을 수행하고자 하는 함수 앞에 async를 표기하고, 함수 내부에서 시릴적인 비동기 작업이 필요한 위치마다 await를 표기하는 것만으로 뒤의 내용을 Promise로 자동 전환하고, 해당 내용이 resolve된 이후에야 다음으로 진행한다.