# 🌸 Chapter 5: 클로저

## 🍄 1. 클로저의 의미 및 원리 이해

- 클로(Closure)는 여러 함수형 프로그래밍 언어에서 등장하는 보편적인 특성이다.
- MDN에서는 클로저에 대해 “클로저는 함수와 그 함수가 선언될 당시의 lexical environment의 상호관계에 따른 현상”이라고 말한다.
- 선언될 당시의 lexical environment는 실행 컨텍스트의 구성 요소 중 하나인 outerEnvironmentReference에 의해 변수의 유효범위인 스코프가 결정되고 스코프 체인이 가능해진다고 했다. 어떤 컨텍스트 A에서 선언한 내부함수 B의 실행 컨텍스트가 활성화된 시점에는 B의 outerEnvironmentReference가 참조하는 대상인 A의 LexicalEnvironment에도 접근이 가능할 것이다. A에서는 B에서 선언한 변수에 접근할 수 없지만 B에서는 A에서 선언한 변수에 접근이 가능하다.
- 내부함수 B가 A의 LexicalEnvironment를 언제나 사용하는 것은 아니다. 내부함수에서 외부 변수를 참조하지 않는 경우라면 combination이라고 할 수 없을 것이다. 내부함수에서 외부 변수를 참조하는 경우에 한해서만 combination, 즉 선언될 당시의 LexicalEnvironment와의 상호관계가 의미가 있을 것이다.
- 클로저란 어떤 함수에서 선언한 변수를 참조하는 내부함수에서만 발생하는 현상이라고 볼 수 있다.

```jsx
var outer = function () {
	var a = 1;
	var inner = function () {
		return ++a;
	};
	return inner();
};

var outer2 = outer();
console.log(outer2); // 2
```

- inner 함수 내부에서 외부변수인 a를 사용했다. 그런데 6번째 줄에서는 inner 함수를 실행한 결과를 리턴하고 있으므로 결과적으로 outer 함수의 실행 컨텍스트가 종료된 시점에는 a 변수를 참조하는 대상이 없어진다.

```jsx
var outer = function () {
	var a = 1;
	var inner = function () {
		return ++a;
	};
	
	return inner;
};

var outer2 = outer();
console.log(outer2()); // 2
console.log(outer2()); // 3
```

- outer 함수의 실행 컨텍스트가 종료될 때 outer2 변수는 outer의 실행 결과인 inner 함수를 참조하게 될 것이다. 이후 9번째에서 outer2를 호출하면 앞서 반환된 함수인 inner가 실행될 것이다.
- inner 함수의 실행 컨텍스트의 environmentRecord는 수집할 정보가 없다. outer-EnvironmentReference에는 inner 함수의 선언된 위치의 LexicalEnvironment가 참조복사된다. inner 함수는 outer 함수 내부에서 선언됐으므로, outer 함수의 LexicalEnvironment가 담길 것이다.
- 스코프 체이닝에 따라 outer에서 선언한 변수 a에 접근해서 1만큼 증가시킨 후 그 값인 2를 반환하고, inner 함수의 실행 컨텍스트가 종료된다. 10번째 줄에서 다시 outer2를 호출하면 같은 방식으로 a의 값을 2에서 3으로 1증가시킨 후 3을 반환한다.
- inner 함수의 실행시점에 outer 함수는 이미 실행이 종료된 상태인데 outer 함수의 LexicalEnvironment에 어떻게 접근할 수 있는걸까? 이는 가비지 컬렉터의 동작 방식 때문이다. 가비지 컬렉터는 어떤 값을 참조하는 변수가 하나라도 있다면 그 값은 수집 대상에 포함시키지 않는다.
- outer 함수는 실행 종료 시점에 inner 함수를 반환한다. 외부함수인 outer의 실행이 종료되더라도 내부 함수인 inner 함수는 언젠가 outer2를 실행함으로써 호출될 가능성이 열린것이다. 언제가 inner 함수의 실행 컨텍스트가 활성화되면 outerEnvironmentRefence가 outer 함수의 LexicalEnvironment를 필요로 할 것이므로 수집 대상에서 제외된다. 그 덕으로 이 변수에 접근할 수 있는 것이다.
- 클로저는 어떤 함수에서 선언한 변수를 참조하는 내부함수에서만 발생하는 현상으로 함수의 실행 컨텍스트가 종료된 후에도 LexicalEnvironment가 가비지 컬렉터의 수집 대상에서 제외되는 경우는 지역변수를 참조하는 내부함수가 외부로 전달된 경우가 유일하다. 어떤 함수에서 선언한 변수를 참조하는 내부함수에서만 발생하는 현상이란 외부 함수의 LexicalEnvironment가 가비지 컬렉팅되지 않는 현상을 말하는 것이다.
- 클로저란 어떤 함수 A에서 선언한 변수 a를 참조하는 내부함수 B를 외부로 전달할 경우 A의 실행 컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상을 말한다. 여기서 주의할 점은 바로 외부로 전달이 곧 return 만을 의미하는 것은 아니라는 점이다.

```jsx
// (1)
(function() {
	var a = 0;
	var intervalId = null;
	var inner = function (){
		if(++a >= 10){
			clearInterval(intervalId);
		}
		console.log(a);
	};
	intervalId = setInterval(inner, 1000);
})();

// (2)
(function () {
	var count = 0;
	var button = documlent.createElement('button');
	button.innerText = 'click';
	button.addEventListener('click', function(){
		console.log(++count, 'times clicked');
	});
	document.body.appendChild(button);
})();
```

- (1)은 별도의 외부객체인 window의 메서드에 전달할 콜백 함수 내부에서 지역변수를 참조한고, (2)는 별도의 외부객체인 DOM의 메서드에 등록할 handler 함수 내부에서 지역변수를 참조한다. 두 상황 모두 지역변수를 참조하는 내부함수를 외부에 전달했기 때문에 클로저이다.

## 🍄 2. 클로저와 메모리 관리

- 클로저는 객체지향과 함수형 모두를 아우르는 매우 중요한 개념이다.
- 메모리 누수의 위험을 이유로 클로저 사용을 조심해야 한다거나 심지어 지양해야 한다고 주장하는 사람들도 있지만 메모리 소모는 클로저의 본질적인 특성일 뿐이다. 오히려 이러한 특성을 정확히 이해하고 잘 활용하도록 노력해야 한다.
- 메모리 관리 방법은 간단하다. 클로저는 어떤 필요에 의해 의도적으로 함수의 지역변수를 메모리를 소모하도록 함으로써 발생한다. 그렇다면 그 필요성이 사라진 시점에는 더는 메모리를 소모하지 않게 해주면 된다. 참조 카운트를 0으로 만들면 언젠가 GC(Garbage Collector)가 수거해갈 것이고, 이때 소모됐던 메모리가 회수될 것이다.
- 참조 카운트를 0으로 만드는 방법은 식별자에 참조형이 아닌 기본형 데이터(보통 null, undefined)를 할당하면 된다.

```jsx
// (1) return에 의한 클로저의 메모리 해제
var outer = (function() {
	var a = 1;
	var inner = function() {
		return ++a;
	}

	return inner;
})();

console.log(outer());
console.log(outer());
outer = null; // outer 식별자의 inner 함수 참조를 끊는다.
```

```jsx
// (2) setInterval에 의한 클로저의 메모리 해제
(function() {
	var a = 0;
	var intervalId = null;
	var inner = function (){
		if(++a >= 10){
			clearInterval(intervalId);
			inner = null; // inner 식별자의 함수 참조를 끊음
		}
		console.log(a);
	};
	intervalId = setInterval(inner, 1000);
})();
```

```jsx
// (3) eventListener에 의한 클로저의 메모리 해제
(function () {
	var count = 0;
	var button = documlent.createElement('button');
	button.innerText = 'click';
	
	var clickHandler = function () {
		console.log(++count, 'times clicked');
		if(count >= 10){
			button.removeEventListener('click', clickHandler);
			clickHandler = null; // clickHandler 식별자의 함수 참조를 끊음
		}
	}

	button.addEventListener('click', clickHandler);
	document.body.appendChild(button);
})();
```

## 🍄 3. 클로저 활용 사례

### 🌻 3-1. 콜백 함수 내부에서 외부 데이터를 사용하고자 할 때

```jsx
var fruits = ['apple', 'banana', 'peach'];
var $ul = document.createElement('ul'); // (공통 코드)

fruits.forEach(fucntion (fruit){ // (A)
	var $li = document.createElement('li');
	$li.innerText = fruit;
	$li.addEventListener('click' function () { // (B)
		alert('your chice is ' + fruit);
	});
	$ul.appendChild($li);
});

document.body.appendChild($ul);
```

- 7번째 줄의 addEventListener에 넘겨준 콜백 함수에 fruit이라는 외부 변수를 참조하고 있으므로 클로저가 있다. (A)는 fruits의 개수만큼 실행되며, 그때마다 새로운 실행컨텍스트가 활성화될 것이다. (A)의 실행 종료 여부와 무관하게 클릭 이벤트에 의해 각 컨텍스트의 (B)가 실행될 떄는 (B)의 outerEnvironmentReference가 (A)의 LexicalEnvironment를 참조하게 될 것이다. 최소한 (B) 함수가 참조할 예정인 변수 fruit에 대해서는 (A)가 종료된 후에도 GC 대상에서 제외되어 계속 참조 가능할 것이다.
- 하지만 (B) 함수의 쓰임새가 콜백 함수에 국한되지 않는 경우라면 반복을 줄이기 위해 (B)를 외부로 분리하는 편이 나을 수 있을 것이다. 즉 fruit를 인자로 받아 출력하는 형태로 말이다.

```jsx
 var alertFruit = function(fruit) {
	alert('your chice is ' + fruit);
};

fruits.forEach(fucntion (fruit){
	var $li = document.createElement('li');
	$li.innerText = fruit;
	$li.addEventListener('click' alertFruit);
	$ul.appendChild($li);
});

document.body.appendChild($ul);
alertFruit(fruits[1]);
```

- 공통 함수로 쓰고자 콜백 함수를 외부로 꺼내어 alertFruit라는 변수에 담았다. 그런데 각 li를 클릭하면 클릭한 대상의 과일명이 아닌 [object mouseEvent]라는 값이 출력된다. 콜백 함수의 인자에 대한 제어권을 addEventListener가 가진 상태이며, addEventListener는 콜백 함수를 호출할 때 첫번째 인자에 이벤트 객체를 주입하기 때문이다. 이 문제는 bind 메서드를 활용하면 손쉽게 해결할 수 있다.

```jsx
fruits.forEach(fucntion (fruit){
	var $li = document.createElement('li');
	$li.innerText = fruit;
	$li.addEventListener('click' alertFruit.bind(null, fruit));
	$ul.appendChild($li);
});
```

- 다만 이렇게 하면 이벤트 객체가 인자로 넘어오는 순서가 바뀌는 점 및 함수 내부에서의 this가 원래의 그것과 달라지는 점은 감안해야 한다. 이런 변경사항이 발생하지 않게끔 하면서 이슈를 해결하기 위해서는 bind 메서드가 아닌 다른 방식으로 풀어내야 한다. 여기서 다른 방식은 고차함수 활용을 말한다.
- 고차함수란 함수를 인자로 받거나 함수를 리턴하는 함수를 말한다.

```jsx
 var alertFruitBuilder = function(fruit) {
	return function(){
		alert('your chice is ' + fruit);	
	}
};

fruits.forEach(fucntion (fruit){
	var $li = document.createElement('li');
	$li.innerText = fruit;
	$li.addEventListener('click' alertFruitBuilder(fruit));
	$ul.appendChild($li);
});

document.body.appendChild($ul);
```

- alertFruitBuilder의 실행 결과로 반환된 함수에는 클로저가 존재한다.

### 🌻 3-2. 접근 권한 제어(정보 은닉)

- 정보 은닉은 어떤 모듈의 내부 로직에 대해 외부로의 노출을 최소화해서 모듈간의 결합도를 낮추고 유연성을 높이고자 하는 현대 프로그래밍 언어의 중요한 개념 중 하나이다.
- 흔히 접근 권한에는 public, private, protected의 세종류가 있다. public은 외부에서 접근 가능한 것이고, private은 내부에서만 사용되며 외부에 노출되지 않는 것을 의미한다.
- 자바스크립트는 기본적으로 변수 자체에 이러한 접근 권한을 직접 부여하도록 설계돼있지 않다. 그렇다고 접근 권한 제어가 불가능한 것은 아니다.

```jsx
var outer = function () {
	var a = 1;
	var inner = function (){
		return ++a;
	}
	return inner;
}

var outer2 = outer();
console.log(outer2());
console.log(outer2());
```

- outer 함수를 종료할 때 inner 함수를 반환함으로써 outer 함수의 지역변수인 a의 값을 외부에서도 읽을 수 있게 되었다. 이처럼 클로저를 활용하면 외부 스코프에서 함수 내부의 변수들 중 선택적으로 일부의 변수에 대한 접근 권한을 부여할 수 있다. 바로 return을 활용해서 말이다.
- closure라는 영어 단어는 사전적으로 닫혀있음, 폐쇄성, 완결성 정도의 의미를 가진다. 이 폐쇄성에 주목해보면 위 예제를 조금 다르게 받아들일 수 있다.
- outer 함수는 외부로부터 철저하게 격리된 닫힌 공간이다. 외부에서는 외부 공간에 노출돼 있는 outer라는 변수를 통해 outer 함수를 실행할 수는 잇지만, outer 함수 내부에는 어떠한 개입도 할 수 없다. 외부에서는 오직 outer 함수가 return한 정보에만 접근할 수 있다. return 값이 외부에 정보를 제공하는 유일한 수단인 것이다.
- 외부에 제공하고자 하는 정보들을 모아서 return 하고, 내부에서만 사용할 정보들은 return 하지 않는 것으로 접근 권한 제어가 가능한 것이다. return한 변수들은 공개 멤버가 되고, 그렇지 않는 변수들은 비공개 멤버가 되는 것이다.

```jsx
var car = {
 fuel: Math.ceil(Math.random() * 10 + 10), // 연료(L)
 power: Math.ceil(Math.random() * 3 + 2), // 연비(km/L)
 moved: 0, // 총 이동거리
 run: function () {
	 var km = Math.ceil(Math.random() * 6);
	 var wasteFuel = km / this.power;
	 if (this.fuel < wasteFuel) {
		 console.log('이동불가');
		 return;
	 }
	 this.fuel -= wasteFuel;
	 this.moved += km;
	 console.log(km + 'km 이동 (총 ' + this.moved + 'km)');
 }
};
```

- car 변수에 객체를 직접 할당했다. fuel과 power는 무작위로 생성하고, moved라는 프로퍼티에 총 이동거리를 부여했으며, run 메서드를 실행할 때마다 car 객체의 fuel, moved 값이 변하게 했다.
- 모두가 run 메서드만 호출한다는 가정하에는 이정도만으로도 충분하다. 그러나 자바스크립트를 어느 정도라도 다룰 줄 알고 승부욕 강한 사람이 참여한다면 얘기가 달라진다. 무작정 값을 변경할 수 있기 때문이다.

```jsx
car.fuel = 10000;
car.power = 100;
car.moved = 1000;
```

- 이렇게 마음껏 값을 바꾸면 일방적인 게임이 된다. 값을 바꾸지 못하도록 방어할 필요가 있는데 방법은 바로 클로저를 활용하는 것이다. 즉 객체가 아닌 함수로 만들고, 필요한 멤버만을 return 하는 것이다.

```jsx
var createCar = function () {
	var fuel = Math.ceil(Math.random() * 10 + 10); // 연료(L)
	var power = Math.ceil(Math.random() * 3 + 2); // 연비(km / L)
	var moved = 0; // 총 이동거리
	return {
		get moved () {
			return moved;
		},
	 run: function () {
		 var km = Math.ceil(Math.random() * 6);
		 var wasteFuel = km / power;
		 if (fuel < wasteFuel) {
			 console.log('이동불가');
			 return;
			}
		 fuel -= wasteFuel;
		 moved += km;
		 console.log(km + 'km 이동 (총 ' + moved + 'km). 남은 연료: ' +
		fuel);
	 }
 };
};

var car = createCar();
```

- 이번에는 createCar 라는 함수를 실행함으로써 객체를 생성하게 했다. fuel,power 변수는 비공개 멤버로 지정해 외부에서의 접근을 제한했고, moved 변수는 getter만을 부여함으로써 읽기 전용 속성을 부여했다. 이제 외부에서는 오직 run 메서드를 실행하는 것과 현재의 moved 값을 확인하는 두 가지 동작만 할 수 있다.

```jsx
car.run(); // 3km 이동(총 3km). 남은 연료: 17.4
console.log(car.moved); // 3
console.log(car.fuel); // undefined
console.log(car.power); // undefined
car.fuel = 1000;
console.log(car.fuel); // 1000
car.run(); // 1km 이동(총 4km). 남은 연료: 17.2
car.power = 100;
console.log(car.power); // 100
car.run(); // 4km 이동(총 8km). 남은 연료: 16.4
car.moved = 1000;
console.log(car.moved); // 8
car.run(); // 2km 이동(총 10km). 남은 연료: 16
```

- 비록 run 메서드를 다른 내용으로 덮어씌우는 어뷰징은 여전히 가능한 상태이긴 하지만 앞 코드보다는 훨씬 안전한 코드가 됐다. 이런 어뷰징을 막기 위해서는 객체를 return 하기 전 미리 변경할 수 없게끔 조치를 취해야 한다.

```jsx
var createCar = function () {
	var publicMembers = {
		// ...
	};
	Object.freeze(publicMembers);
	return publicMembers;
};
```

### 🌻 3-3. 부분 적용 함수

- 부분 적용 함수란 n개의 인자를 받는 함수에 미리 m개의 인자만 넘겨 기억시켰다가, 나중에 (n-m)개의 인자를 넘기면 비로소 원래 함수의 실행 결과를 얻을 수 있게끔 하는 함수이다.

```jsx
var add = function () {
	var result = 0;
	for (var i = 0; i < arguments.length; i++){
		result += arguments[i];
	}
	return result;
};

var addPartial = add.bind(null, 1, 2, 3, 4, 5);
console.log(addPartial(6, 7, 8, 9, 10)); // 55
```

- addPartial 함수는 인자 5개를 미리 적용하고, 추후 추가적으로 인자들을 전달하면 모든 인자를 모아 원래의 함수가 실행되는 부분 적용 함수이다. add 함수는 this를 사용하지 않으므로 bind 메서드만으로도 문제 없이 구현되었다. 그러나 this값을 변경할 수 밖에 없기 때문에 메서드에서는 사용할 수 없을 것 같다.

```jsx
var partial = function () {
	var originalPartialArgs = arguments;
	var func = originalPartialArgs[0];
	if (typeof func !== 'function') {
		throw new Error('첫 번째 인자가 함수가 아닙니다.');
	}
	return function () {
		var partialArgs = Array.prototype.slice.call(originalPartialArgs, 1);
		var restArgs = Array.prototype.slice.call(arguments);
		return func.apply(this, partialArgs.concat(restArgs));
	};
};

var add = function () {
	var result = 0;
	for (var i = 0; i < arguments.length; i++) {
		result += arguments[i];
	}
	return result;
};
var addPartial = partial(add, 1, 2, 3, 4, 5);
console.log(addPartial(6, 7, 8, 9, 10)); // 55

var dog = {
	name: '강아지',
	greet: partial(function(prefix, suffix) {
		return prefix + this.name + suffix;
	}, '왈왈, ')
};
dog.greet('입니다!'); // 왈왈, 강아지입니다.
```

- 첫 번째 인자에는 원본 함수를, 두 번째 인자 이후부터는 미리 적용할 인자들을 전달하고, 반환할 함수에서는 다시 나머지 인자들을 받아 이들을 한데 모아(concat) 원본 함수를 호출(apply)한다. 또한 실행 시점의 this를 그대로 반영함으로써 this에는 아무런 영향을 주지 않게 됐다.
- 보통 이정도로 충분하다. 원하는 만큼의 인자를 미리 넘겨놓고, 나중에 추가할 인자를 전달해서 실행하는 목적에 부합하기 때문이다. 다만 부분 적용 함수에 넘길 인자를 받으시 앞에서부터 차례로 전달할 수밖에 없다는 점은 아쉽다.

```jsx
Object.defineProperty(window, '_', {
	value: 'EMPTY_SPACE',
	writable: false,
	configurable: false,
	enumerable: false
});

var partial2 = function () {
	var originalPartialArgs = arguments;
	var func = originalPartialArgs[0];
	if (typeof func !== 'function') {
		throw new Error('첫 번째 인자가 함수가 아닙니다.');
	}
	return function () {
		var partialArgs = Array.prototype.slice.call(originalPartialArgs, 1);
		var restArgs = Array.prototype.slice.call(arguments);
		for (var i = 0; i < partialArgs.length; i++) {
			if (partialArgs[i] === _) {
			partialArgs[i] = restArgs.shift();
		}
	}
	return func.apply(this, partialArgs.concat(restArgs));
	};
};

var add = function () {
	var result = 0;
	for (var i = 0; i < arguments.length; i++) {
		result += arguments[i];
	}
	return result;
};
var addPartial = partial2(add, 1, 2, _, 4, 5, _, _, 8, 9);
console.log(addPartial(3, 6, 7, 10)); // 55

var dog = {
	name: '강아지',
	greet: partial2(function(prefix, suffix) {
		return prefix + this.name + suffix;
	}, '왈왈, ')
};
dog.greet(' 배고파요!');
```

- 이번에는 ‘비워놓음’을 표시하기 위해 미리 전역객체에 _라는 프로퍼티를 준비하면서 삭제 변경 등의 접근에 대한 방어 차원에서 여러 가지 프로퍼티 속성을 설정했다.
- 처음에 넘겨준 인자들 중 _로 비워놓은 공간마다 나중에 넘어온 인자들이 차례대로 끼워넣도록 구현했다.
- 실무에서 부분 함수를 사용하기 적합한 예로 디바운스(debounce)가 있다. 디바운스는 짧은 시간 동안 동일한 이벤트가 많이 발생할 경우 이를 전부 처리하지 않고 처음 또는 마지막에 발생한 이벤트에 대해 한 번만 처리하는 것으로 프런트엔드 성능 최적화에 큰 도움을 주는 기능 중 하나이다. scroll, wheel, mousemove, resize 등에 적용하기 좋다.
- Lodash 등의 라이브러리에서는 디바운스를 꽤 복잡하게 구현해 놓았지만, 최소한의 기능(마지막에 발생한 이벤트만 처리해도 괜찮고, 어느 정도의 시간 지연이 크게 문제되지 않은 경우)에 대한 구현은 생각보다 간단하다.

```jsx
var debounce = function (eventName, func, wait){
	var timeroutId = null;
	return function (event) {
		var self = this;
		console.log(eventName, 'event 발생');
		clearTimeout(timeoutId);
		timeoutId = setTimeout(func.bind(self, event), wait);
	}
}

var moveHandler = function (e) {
 console.log('move event 처리');
}

var wheelHandler = function (e) {
	console.log('wheel event 처리');
}

document.body.addEventListener('mousemove', debounce('move', moveHandler, 500));
document.body.addEventListener('mousewheel', debounce('wheel', wheelHandler, 700));
```

- 디바운스 함수는 출력 용도로 지정한 eventName과 실행할 함수 (func), 마지막으로 발생한 이벤트인지 여부를 판단하기 위한 대기시간(wait(ms))을 받는다. 내부에서는 timeroutId 변수를 생성하고, 클로저로 EventListener에 의해 호출될 함수를 반환한다.
- 반환될 함수 내부에서는 4번째 줄에서 setTimeout을 사용하기 위해 this를 별도의 변수에 담고 6번째 줄에서 무조건 대기큐를 초기화하게 했다. 마지막 7번째 줄에서 setTimeout으로 wait 시간만큼 지연시킨 다음, 원래의 func를 호출하는 형태이다.
- 최초 event가 발생하면 timeout의 대기열 ‘wait 시간 뒤에 func를 실행할 것’이라는 내용이 담긴다. 그런데 wait 시간이 경과하기 이전 다시 동일한 event가 발생하면 이번에느 6번째 줄에 의해 앞서 저장했던 대기열을 초기화 하고 다시 새로운 대기열을 등록한다. 결국 각 이벤트가 바로 이전 이벤트로부터 wait 시간 이내에 발생하는 한 마지막에 발생한 이벤트만이 초기화되지 않고 무사히 실행될 것이다.

### 🌻 3-4. 커링 함수

- 커링 함수(curring function)란 여러 개의 인자를 받는 함수를 하나의 인자만 받는 함수로 나눠서 순차적으로 호출될 수 있게 체인 형태로 구성한 것을 말한다. 부분 적용 함수와 기본적인 맥락은 일치하지만 몇 가지 다른 점이 있다.
- 커링은 한 번에 하나의 인자만 전달하는 것을 원칙으로 한다. 또한 중간 과정상의 함수를 실행한 결과는 그 다음 인자를 받기 위해 대기만 할 뿐으로, 마지막 인자가 전달되기 전까지는 원본 함수가 실행되지 않는다.

```jsx
var curry3 = fucntion(func) {
	return function (a) {
		return function (b) {
			return func(a,b);
		};
	};
};

var getMaxWith10 = curry3(Math.max)(10);
console.log(getMaxWith10(8)); // 10
console.log(getMaxWith10(25)); // 25

var getMinWith10 = curry3(Math.min)(10);
console.log(getMinWith10(8)); // 8
console.log(getMinWith10(25)); // 10
```

- 부분 적용 함수와 달리 커링 함수는 필요한 상황에 직접 만들어 쓰기 용이하다. 필요한 인자 개수만큼 함수를 만들어 계속 리턴해주다가 마지막에만 짠하고 조합해서 리턴해주면 되기 때문이다. 다만 인자가 많을 수록 가독성이 떨어진다.

```jsx
var curry5 = fucntion(func) {
	return function (a) {
		return function (b) {
			return function (c) {
				return function (d) {
					return function (e) {
						return func(a, b, c, d, e);
					};
				};
			};
		};
	};
};
```

- 5개만 받아서 처리했음에도 이를 표현하기 위해 자그마치 13줄이나 소모했다. 다행히 ES6에서는 화살표 함수를 사용하여 같은 내용을 단 함줄에 표기할 수 있다.

```jsx
var curry5 = func => a => b => c => d => e => func(a,b,c,d,e);
```

- 화살표 함수로 구현하면 커링 함수를 이해하기에 훨씬 수월하다. 화살표 순서에 따라 함수에 값을 차례로 넘겨주면 마지막에 func가 호출될 거라는 흐름이 한눈에 파악된다. 각 단계에서 받은 인자들을 모두 마지막 단계에서 참조할 것이므로 GC되지 않고 메모리에 차곡차곡 쌓였다가, 마지막 호출로 실행 컨텍스트가 종료된 후에야 비로소 한꺼번에 GC의 수거 대상이 된다.
- 커링 함수가 유용한 경우가 있다. 당장 필요한 정보만 받아서 전달하고 또 필요한 정보가 들어오면 전달하는 식으로 하면 결국 마지막 인자가 넘어갈 때까지 할수 실행을 미루는 셈이다. 이를 함수형 프로그래밍에서는 지연실행이라고 칭한다.
- 원하는 시점까지 지연시켰다가 실행하는 것이 요긴한 상황이라면 커링을 쓰기에 적합할 것이다. 혹은 프로젝트 내에서 자주 쓰이는 함수의 매개변수가 항상 비슷하고 일부만 바뀌는 경우에도 적절한 후보가 될 것이다.

```jsx
var getInformation = fucntion(baseUrl) { // 서버에 요청할 주소의 기본 URL
	return function (path) { // path 값
		return function (id) { // id 값
			return fetch(baseUrl + path + '/' + id); // 실제 서버에 정보를 요청
		}	
	}
} 

var getInformation = baseUrl => path => id => fetch(baseUrl + path + '/' + id);
```

- HTML5의 fetch 함수는 url을 받아 해당 url에 HTTP 요청을 한다. 보통 REST API를 이용할 경우 baseUrl은 몇 개로 고정되지만 나머지 path나 id 값은 매우 많을 수 있다. 이런 상황에서 서버에 정보를 요청할 필요가 있을 때마다 매번 baseUrl 부터 전부 기입해주기보다는 공통적인 요소는 먼저 기억시켜두고 특정한 값(id)만으로 서버 요청을 수행하는 함수를 만들어두는 편이 개발 효율성이나 가독성 측면에서 더 좋을 것이다.
- 이런 이유로 최근의 여러 프레임워크나 라이브러리 등에서 커링을 상당히 광범위하게 사용하고 있다. Flux 아키텍처의 구현체 중 하나인 Redux의 미들웨어(middleware)를 예로 들면 다음과 같다.

```jsx
// redux middleware 'logger'
const logger = store => next => action => {
	console.log('dispatching', action);
	console.log('next state', store.getState());
	return next(action);
}

// redux middleware 'thunk'
const thunk = store => next => action => {
	return typeof action === "function"
		? action(dispatch, store.getState)
		: next(action);
}
```

- 두 미들웨어는 공통적으로 store, next, action 순서로 인자를 받는다. store는 프로젝트 내에서 한 번 생성된 이후로는 바뀌지 않는 속성이고 dispatch의 의미를 가지는 next 역시 마찬가지지만, action의 경우 매번 달라진다. 즉 store와 next 값이 결정되면 redux 내부에서 logger 또는 thunk에 store, next를 미리 넘겨서 반환된 함수를 저장시켜놓고, 이후에는 action만 받아서 처리할 수 있게끔 한 것이다.