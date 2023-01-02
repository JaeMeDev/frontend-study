# 🌸 Chapter 7: 클래스

## 🍄 1. 클래스와 인스턴스의 개념 이해

- 클래스는 하위로 갈수록 상위 클래스의 속성을 상속하면서 더 구체적인 요건이 추가 또는 변경된다.
- 하위 클래스가 아무리 구체화되더라도 이들은 결국 추상적인 개념일 뿐이다.
- 어떤 클래스의 속성을 지니는 실존하는 개체를 일컬어 인스턴스(instance)라고 한다.
- 어떤 클래스에 속한 개체는 그 클래스의 조건을 모두 만족하므로 그 클래스의 구체적인 예시, 즉 인스턴스가 된다.
- 한 인스턴스는 하나의 클래스만을 바탕으로 만들어진다. 어떤 인스턴스가 다양한 클래스에 속할 수는 있지만 이 클래스들은 모두 인스턴스 입장에서는 ‘직계존속’이다. 다중상속을 지원하는 언어이든 그렇지 않은 언어이든 결국 인스턴스를 생성할 때 호출할 수 있는 클래스는 오직 하나뿐일 수밖에 없다.
- 현실세계의 클래스와 마찬가지로 공통 요소를 지니는 집단을 분류하기 위한 개념이라는 측면에서는 일치하지만 인스턴스들로부터 공통점을 발견해서 클래스를 정의하는 현실과 달리, 클래스가 먼저 정의돼야만 그로부터 공통적인 요소를 지니는 개체들을 생성할 수 있다.
- 현실세계에서의 클래스는 추상적인 개념이지만, 프로그래밍 언어에서의 클래스는 사용하기에 따라 추상적인 대상일 수도 있고 구체적인 개체가 될 수도 있다.

## 🍄 2. 자바스크립트의 클래스

- 프로토타입을 일반적인 의미에서의 클래스 관점에서 접근해보면 비슷하게 해석할 수 있는 요소가 없지 않다.
- 생성자 함수 Array를 new 연산자와 함께 호출하면 인스턴스가 생성된다. 이때 Array를 일종의 클래스라고 하면, Array의 prototype 객체 내부 요소들이 인스턴스에 ‘상속’된다라고 볼 수 있다. 엄밀히는 상속이 아닌 프로토타입 체이닝에 의한 참조지만 결과적으로는 동일하게 동작하므로 이렇게 이해해도 무방하다.
- 한편 Array 내부 프로퍼티들 중 prototype 프로퍼티를 제외한 나머지는 인스턴스에 상속되지 않는다.
- 인스턴스에 상속되는지(인스턴스가 참조하는지) 여부에 따라 스태틱 멤버와 인스턴스 멤버로 나뉜다.
- 클래스 기반 언어와 달리 자바스크립트에서는 인스턴스에서도 직접 메서드를 정의할 수 있기 때문에 인스턴스 메서드라는 명칭은 프로토타입에 정의한 메서드를 지칭하는 것인지 인스턴스에 정의한 메서드를 지칭하는 것인지에 대해 도리어 혼란을 야기한다. 따라서 이 명칭 대신 프로토타입 메서드라고 부르는 편이 더 좋다.

```jsx
// 생성자
var Rectangle = function(width, height) {
    this.width = width;
    this.height = height;
};

// (프로토타입) 메서드
Rectangle.prototype.getArea = function () {
    return this.width * this.height;
}

// 스태틱 메서드
Rectangle.isRectangle = function(instance) {
    return insatnce instanceof Rectangle &&
        instance.width > 0 && instance.height > 0;
};

var rect = new Rectangle(3, 4);
console.log(rect1.getArea()); // 12 (O)
console.log(rect1.isRectangle(rect1)); // Error (X)
console.log(Rectangle.isRectangle(rect1)); // true
```

- 일반적인 사용 방식, 즉 구체적인 인스턴스가 사용할 메서드를 정의한 ‘틀’의 역할을 담당하는 목적을 가질 때의 클래스는 추상적인 개념이지만, 클래스 자체를 this로 해서 직접 접근해야만 하는 스태틱 메서드를 호출할 때의 클래스는 그 자체가 하나의 개체로서 취급된다.

## 🍄 3. 클래스 상속

### 🌻 3-1. 기본 구현

```jsx
var Grade = function() {
    var args = Array.prototype.slice.call(arguments);
    for(var i = 0; i < args.length; i++) {
        this[i] = args[i];
    }
    this.length = args.length;
};

Grade.prototype = [];
var g = new Grade(100, 80);
```

- ES5까지의 자바스크립트에는 클래스가 없다. ES6에서 클래스가 도입됐지만 역시나 prototype을 기반으로 한 것이다. 자바스크립트에서 클래스 상속을 구현했다는 것은 결국 프로토타입 체이닝을 잘 연결한 것으로 이해하면 된다.

```jsx
g.push(90);
console.log(g); // Grade { 0: 100, 1: 80, 2: 90, length: 3 }

delete g.length;
g.push(70);
console.log(g); // Grade { 0: 70, 1: 80, 2: 90, length: 1 }
```

- length 프로퍼티를 삭제하고 다시 push를 했더니 push 한 값이 0번째 인덱스에 들어갔고, length가 1이 됐다. 내장 객체인 배열 인스턴스의 length 프로퍼티는 configurable 속성이 false 라서 삭제가 불가능하지만, Grade 클래스의 인스턴스는 배열 메서드를 상속하지만 기본적으로는 일반 객체의 성질을 그대로 지니므로 삭제가 가능해서 문제가 된다.
- 한편 push를 했을 때 0번째 인덱스에 70이 들어가고 length가 1이 될 수 있었던 까닭은 바로 g.__proto__, 즉 Grade.prototype이 빈 배열을 가리키고 있기 때문이다. push 명령에 의해 자바스크립트 엔진이 g.length를 읽고자 하는데 g.length가 없으니까 프로토타입 체이닝을 타고 g.__proto__.length를 읽어온 것이다. 빈 배열의 length가 0이므로 여기에 값을 할당하고 length는 1만큼 증가시키라는 명령이 문제 없이 동작할 수 있었던 것이다.

```jsx
Grade.prototype = ['a', 'b', 'c', 'd'];
var g = new Grade(100, 80);

g.push(90);
console.log(g); // Grade { 0: 100, 1: 80, 2: 90, length: 3 }
delete g.length;

g.push(70);
console.log(g); // Grade { 0: 100, 1: 80, 2: 90, ___ 4: 70, length: 5}
```

- g.length가 없으니 g.__proto__.length를 찾고, 값이 4이므로 인덱스 4에 70을 넣고 다시 g.length에 5를 부여하는 순서로 동작한다.
- 클래스에 있는 값이 인스턴스의 동작에 영향을 줘서는 안된다. 사실 이런 영향을 줄 수 있다는 사실 자체가 클래스의 추상성을 해치는 것이다.
- 인스턴스와의 관계에서는 구체적인 데이터를 지니지 않고 오직 인스턴스가 사용할 메서드만을 지니는 추상적인 ‘틀’로서만 작용하게끔 작성하지 않는다면 언젠가 예기치 않은 오류가 발생할 가능성을 안고가야 하는 것이다.

```jsx
var Rectangle = function (width, height) {
    this.width = width;
    this.height = height;
};

Rectangle.prototype.getArea = function () {
    return this.width * this.height;
};

var rect = new Rectangle(3, 4);
console.log(rect.getArea()); // 12

var Square = function (width) {
    this.width = width;
};

Square.prototype.getArea = function() {
    return this.width * this.width;
};

var sq = newSquare(5);
console.log(sq.getArea()); // 25
```

- 이렇게 작성하면 Rectangle, Square 클래스에 공통 요소가 보인다. width라는 프로퍼티가 공통이고 getArea 내용이 다르지만 비슷하다. 만약 Square에서 width 프로퍼티만 쓰지 않고 height 프로퍼티에 width 값을 부여하는 형태가 된다면 getArea도 동일하게 고칠 수 있다.

```jsx
var Square = function (width) {
    this.width = width;
    this.height = width;
};

Square.prototype.getArea = function () {
    return this.width * this.height;
};
```

- 이제는 소스상으로도 Square를 Rectangle의 하위 클래스로 삼을 수 있을 것 같다. getArea라는 메서드는 동일한 동작을 하므로 상위 클래스에서만 정의하고 하위 클래스에서는 해당 메서드를 상속하면서 height 대신 width를 넣어주면 될 것이다.

```jsx
var Square = function(width) {
    Rectangel.call(this, width, width);
}

Square.prototype = new Rectangle();
```

- 위 코드만으로 완벽한 클래스 체계가 구축됐다고 볼 수는 없다. 클래스에 있는 값이 인스턴스에 영향을 줄 수 있기 때문이다.
- 첫번째 문제는 Square.prototype에 값이 존재하는 것이 문제이다. 만약 이후에 임의로 Square.prototype.width에 값을 부여하고 sq.width의 값을 지워버린다면 프로토타입 체이닝에 의해 엉뚱한 결과가 나오게 될 것이다.
- 두번째 문제는 constructor가 여전히 Rectangle을 바라보고 있다는 것이다. sq.constructor로 접근하면 프로토타입 체이닝을 따라 sq.__proto__.__proto__ 즉 Rectangle.prototype에서 찾게 되며 이는 Rectangle을 가리키고 있기 대문이다.

```jsx
var rect2 = new sq.constructor(2, 3);
console.log(rect2); // Rectangle { width: 2, height: 3 }
```

- 이처럼 하위 클래스로 삼을 생성자 함수의 prototype에 상위 클래스의 인스턴스를 부여하는 것만으로도 기본적인 메서드 상속은 가능하지만 다양한 문제가 발생할 여지가 있어 구조적으로 안정성이 떨어진다.

### 🌻 3-2. 클래스가 구체적인 데이터를 지니지 않게 하는 방법

```jsx
delete Square.prototype.width;
delete Square.prototype.height;
Object.freeze(Square.prototype);
```

```jsx
var extendClass1 = function (SuperClass, SubClass, subMethods) {
    SubClass.prototype = new SuperClass();
    for(var prop in SubClass.prototype) {
        if(SubClass.prototype.hasOwnProperty(prop)) {
            delete SubClass.prototype[prop];
        }
    }
    if(subMethods) {
        for(var method in subMethods) {
            SubClass.prototype[method] = subMethods[method];
        }
    }
    Object.freeze(SubClass.prototype);
    return SubClass;
}

var Square = extendClass1(Rectangle, function (width) {
    Rectangle.call(this, width, width);
});
```

- extendClass1 함수는 SuperClass와 SubClass, SubClass에 추가할 메서드들이 정의된 객체를 받아서 SubClass의 prototype 내용을 정리하고 freeze 하는 내용으루 구성되어 있다. SubClass의 프로토타입을 정리하는 내용이 다소 복잡해졌지만 범용성 측면에서는 꽤 괜찮은 방법이다.
- 또 다른 방법으로 더글라스 크락포드가 제시해서 대중적으로 널리 알려진 방법으로 아이디어는 이렇다. SubClass의 prototype에 직접 SuperClass의 인스턴스를 할당하는 대신 아무런 프로퍼티를 생성하지 않는 빈 생성 함수(Bridge)를 하나 더 만들어서 그 prototype이 SuperClass의 prototype을 바라보게끔 한 다음, SubClass의 prototype에는 Bridge의 인스턴스를 할당하게 하는 것이다.

```jsx
var Rectangle = function(width, height) {
    this.width = width;
    this.height = height;
}

Rectangle.prototype.getArea = function () {
    return this.width * this.height;
};

var Square = function (width) {
    Rectangle.call(this, width, width);
};

var Bridge = function () {};
Bridge.prototype = Rectangle.prototype;
Square.prototype = new Bridge();
Object.freeze(Square.prototype);
```

- Bridge라는 빈 함수를 만들고 Bridge.prototype이 Rectangle.prototype을 참조하게 한 다음, Square.prototype에 new Bridge()로 할당하면, Rectangle 자리에 Bridge가 대체하게 될 것이다. 이로써 인스턴스를 제외한 프로토타입 체인 경로상에는 더 구체적인 데이터가 남아있지 않게 된다.

```jsx
var extendClass2 = (function (){
    var Bridge = function() {};
    return function (SuperClass, SubClass, subMethods) {
        Bridge.prototype = SuperClass.prototype;
        SubClass.prototype = new Bridge();
        if(subMethods) {
            for(var method in subMethods) {
                SubClass.prototype[method] = subMethods[method];
            }
        }
        Object.freeze(SubClass.prototype);
        return SubClass;
    };
})();
```

- 다른 방법으로는 ES5에 도입된 Object.create를 이용한 방법이 있다. 이 방법은 SubClass의 prototype의 `__proto__` 가 SuperClass의 prototype을 바라보되, SuperClass의 인스턴스가 되지는 않으므로 앞서 소개한 방법들보다 간단하다.

```jsx
// (...생략)
Square.prototype = Object.create(Rectangle.prototype);
Object.freeze(Square.prototype);
// (...생략)
```

- 클래스 상속 및 추상화를 흉내 내기 위한 라이브러리가 많이 있지만 기본적인 접근 방법은 위 세가지의 방법에 크게 벗어나지 않는다. 결국 SubClass.prototype의 `__proto__` 가 SuperClass.prototype를 참조하고, SubClass.prototype에는 불필요한 인스턴스 프로퍼티가 남아있지 않으면 된다.

### 🌻 3-3. constructor 복구하기

- 위 세가지 방법 모두 기본적인 상속에는 성공했지만 SubClass 인스턴스의 constructor는 여전히 SuperClass를 가리키는 상태이다. 엄밀히 SubClass 인스턴스에는 constructor가 없고, SubClass.prototype에도 없는 상태이다. 프로토타입 체인상에 가장 먼저 등장하는 SuperClass.prototype의 constructor에서 가리키는 대상 즉 SuperClass가 출력될 뿐이다.

```jsx
var extendClass1 = function (SuperClass, SubClass, subMethods) {
    SubClass.prototype = new SuperClass();
    for(var prop in SubClass.prototype) {
        if(SubClass.prototype.hasOwnProperty(prop)) {
            delete SubClass.prototype[prop];
        }
    }
    SubClass.prototype.constructor = SubClass;
    if(subMethods) {
        for(var method in subMethods) {
            SubClass.prototype[method] = subMethods[method];
        }
    }
    Object.freeze(SubClass.prototype);
    return SubClass;
}
```

```jsx
var extendClass2 = (function (){
    var Bridge = function() {};
    return function (SuperClass, SubClass, subMethods) {
        Bridge.prototype = SuperClass.prototype;
        SubClass.prototype = new Bridge();
        SubClass.prototype.constructor = SubClass;
        if(subMethods) {
            for(var method in subMethods) {
                SubClass.prototype[method] = subMethods[method];
            }
        }
        Object.freeze(SubClass.prototype);
        return SubClass;
    };
})();
```

```jsx
var extendClass3 = function (SuperClass, SubClass, subMethods) {
    SubClass.prototype = Object.create(SuperClass.prototype);
    SubClass.prototype.constructor = SubClass;
    if (subMethods) {
        for (var method in subMethods) {
            SubClass.prototype[method] = subMethods[method];
        }
    }
    Object.freeze(SubClass.prototype);
    return SubClass;
};
```

### 🌻 3-4. 상위 클래스에의 접근 수단 제공

- 때론 하위 클래스의 메서드에서 상위 클래스의 메서드 실행 결과를 바탕으로 추가적인 작업을 수행하고 싶을 때가 있다. 이럴 때 매번 “SuperClass.prototype.method.apply(this, arguments)”로 해결하는 것은 상당히 번거롭고 가독성이 떨어지는 방식이다.
- 하위 클래스에서 상위 클래스의 프로토타입 메서드에 접근하기 위한 별도의 수단이 있으면 더욱 편리할 것이다. 이런 별도의 수단, 즉 다른 객체지향 언어들의 클래스 문법 중 하나인 ‘super’를 흉내내보자.

```jsx
var extendClass = function (SuperClass, SubClass, subMethods) {
    SubClass.prototype = Object.create(SuperClass.prototype);
    SubClass.prototype.constructor = SubClass;
    SubClass.prototype.super = function(propName) {
        var self = this;
        if(!propName) return function () {
            SuperClass.apply(self, arguments);
        }
        var prop = SuperClass.prototype[propName];
        if(typeof prop !== 'function') return prop;
        return function () {
            return prop.apply(self, arguments);
        }
    }
    if (subMethods) {
        for (var method in subMethods) {
            SubClass.prototype[method] = subMethods[method];
        }
    }
    Object.freeze(SubClass.prototype);
    return SubClass;
};

var Rectangle = function (width, height) {
    this.width = width;
    this.height = height;
};
Rectangle.prototype.getArea = function () {
    return this.width * this.height;
};
var Square = extendClass(
    Rectangle,
    function (width) {
        this.super()(width, width); // super 사용 (1)
    }, {
        getArea: function () {
            console.log('size is :', this.super('getArea')()); // super 사용 (2)
        }
    }
);
var sq = new Square(10);
sq.getArea(); // size is : 100
console.log(sq.super('getArea')()); // 100
```

- super 메서드의 사용법은 SuperClass의 생성자 함수에 접근하고자 할 때는 this.super(), SuperClass의 프로토타입 메서드에 접근하고자 할 때는 this.super(propName)와 같이 사용하면 된다.

## 🍄 4. ES6의 클래스 및 클래스 상속

- ES6에서는 본격적으로 클래스 문법이 도입되었다.

```jsx
var ES5 = function(name) {
    this.name = name;
};

ES5.staticMethod = function () {
    return this.name + ' staticMethod';
};

ES5.prototype.method = function () {
    return this.name + ' method';
};

var es5Instance = new ES5('es5');
console.log(ES5.staticMethod()); // es5 staticMethod
console.log(es5Instance.method()); // es5 method

var ES6 = class {
    constructor(name) {
        this.name = name;
    }
    static staticMethod() {
        return this.name + ' staticMethod';
    }
    method() {
        return this.name + ' method';
    }
};

var es6Instance = new ES6('es6');
console.log(ES6.staticMethod()); // es6 staticMethod
console.log(es6Instance.method()); // es6 method
```

- ES6의 클래스 상속은 이렇다.

```jsx
var Rectangle = class {
	constructor(width, height) {
		this.width = width;
		this.height = height;
	}
	getArea() {
		return this.width * this.height;
	}
};

var Square = class extends Rectangle {
	constructor(width) {
		super(width, width);
	}
	getArea() {
		console.log('size is :', super.getArea());
	}
}
```