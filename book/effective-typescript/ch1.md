# 🌸 Chapter 1: 타입스크립트 알아보기

## 🍄 1. 타입스크립트와 자바스크립트의 관계 이해하기

- 타입스크립트는 문법적으로도 자바스크립트의 상위집합이다. 자바스크립트 프로그램에 문법 오류가 없다면, 유효한 타입스크립트 프로그램이라고 할 수 있다. 그런데 자바스크립트 프로그램에 어떤 이슈가 존재한다면 문법 오류가 아니라도 타입 체커에게 지적당할 가능성이 높다.
- 그러나 문법의 유효성과 동작의 이슈는 독립적인 문제이다. 타입스크립트는 여전히 작성된 코드를 파싱하고 자바스크립트로 변환할 수 있다.
- 자바스크립트 파일이 .js or .jsx 확장자를 사용하는 반면, 타입스크립트 파일은 .ts or .tsx 확장자를 사용한다. 그렇다고 자바스크립트와 타입스크립트가 완전히 다른 언어라는 의미는 아니다.
- 타입스크립트는 자바스크립트의 상위집합이기 때문에 .js 파일에 있는 코드는 이미 타입스크립트라고 할 수 있다. main.js 파일을 main.ts로 바꾼다고 해도 달라질 것 없기 때문이다.
- 이 특성은 자바스크립트 코드를 타입스크립트로 마이그레이션하는데 엄청난 이점이 된다. 기존 코드를 그대로 유지하면서 일부분에만 타입스크립트 적용이 가능하기 때문이다.
- 타입스크립트 프로그램이지만 자바스크립트가 아닌 프로그램은 존재한다. 이는 타입스크립트가 타입을 명시하는 추가적인 문법을 가지기 때문이다.

```tsx
function greet(who: string) {
	console.log('Hello', who);
}
```

- 유효한 타입스크립트 프로그램이지만 자바스크립트를 구동하는 노드(node)같은 프로그램으로 앞의 코드를 실행하면 오류를 출력한다.
- `: string` 은 타입스크립트에서 쓰이는 타입 구문으로 타입 구문을 사용하는 순간부터 자바스크립트는 타입스크립트 영역으로 들어 가게 된다.
- 타입스크립트 컴파일러는 타입스크립트뿐만 아니라 일반 자바스크립트 프로그램에도 유용하다.

```jsx
let city = 'new york city';
console.log(city.toUppercase()); // TypeError : city.toUppercase is not a function
```

- 위 코드는 타입 구문이 없지만, 타입스크립트의 타입 체커는 문제점을 찾아준다.

```tsx
let city = 'new york city';
console.log(city.toUppercase()); // ~~~ 'toUppercase' 속성이 string 형식에 없습니다.
																 // ~~~ 'toUpperCase'을 (를) 사용하시겠습니까?
```

- city 변수가 문자열이라는 것을 알려 주지 않아도 타입스크립트는 초깃값으로부터 타입을 추론한다.
- 타입 시스템의 목표 중 하나는 런타임에 오류를 발생시킬 코드를 미리 찾아 내는 것이다. 타입스크립트가 ‘정적’타입 시스템이라는 것은 바로 이런 특징을 말하는 것이다. 그러나 타입 체커가 모든 오류를 찾아내지는 않는다.

```jsx
const states = [
	{ name: 'Alabama', capital: 'Montgomery' },
	{ name: 'Alaska', capital: 'Juneau' },
	{ name: 'Arizona' capital: 'Phoenix' }
];

for (const state of states) {
	console.log(state.capitol); // undefined undefined undefined
}
```

- 위 코드는 유효한 자바스크립트이며 어떠한 오류도 없이 실행된다. 그러나 루프 내의 state.capitol은 의도한 코드가 아닌 게 분명하다. 이런 경우에 타입스크립트 타입 체커는 추가적인 타입 구문 없이도 오류를 찾아낸다.

```tsx
for (const state of states) {
	console.log(state.capitol); // ~~~ 'capitol' 속성이 ... 형식에 없습니다.
															// ~~~ 'capital'을(를) 사용하시겠습니까?
}
```

- 타입스크립트는 타입 구문 없이도 오류를 잡을 수 있지만, 타입 구문을 추가한다면 훨씬 더 많은 오류를 찾아낼 수 있다.
- 코드의 ‘의도’가 무엇인지 타입 구문을 통해 타입스크립트에게 알려줄 수 있기 때문에 코드의 동작과 의도가 다른 부분을 찾을 수 있다.

```tsx
// capital 과 capitol을 바꿔보자
const states = [
	{ name: 'Alabama', capitol: 'Montgomery' },
	{ name: 'Alaska', capitol: 'Juneau' },
	{ name: 'Arizona' capitol: 'Phoenix' }
];

for (const state of states) {
	console.log(state.capital); // ~~~ 'capital' 속성이 ... 형식에 없습니다.
															// ~~~ 'capitol'을(를) 사용하시겠습니까?
}
```

- 위에서 제시한 해결책은 잘못되었다. 한곳에서는 capital로 다른 한곳에서는 capitol로 다르게 타이핑했지만 타입스크립트는 어느 쪽이 오타인지 판단하지 못한다. 오류의 원인을 추측할 수 있겠지만 항상 정확하지는 않는다. 따라서 명시적으로 states를 선언하여 의도를 분명하게 하는 것이 좋다.

```tsx
interface State {
	name: string;
	capital: string;
}

const states: State[] = [
	{ name: 'Alabama', capitol: 'Montgomery' },
	{ name: 'Alaska', capitol: 'Juneau' },
	{ name: 'Arizona' capitol: 'Phoenix' }
										// ~~~~~~~~~~~~~~~~~ 개체 리터럴은 알려진 속성만 지정할 수 있지만 
										// ~~~~~~~~~~~~~~~~~ 'State' 형식에 'capitol'이(가) 없습니다.
										// ~~~~~~~~~~~~~~~~~ 'capital'을(를) 쓰려고 했습니까?
];

for (const state of states) {
	console.log(state.capital);
}
```

- 이제 오류도 어디에서 발생했는지 찾을 수 있고, 제시된 해결책도 올바르다. 의도를 명확히 해서 타입스크립트가 잠재적 문제점을 찾을 수 있게 한 것이다.
- 타입스크립트 타입 시스템은 자바스크립트의 런타임 동작을 ‘모델링’한다.

```tsx
const x = 2 + '3'; // 정상, string 타입이다.
const y = '2' + 3; // 정상, string 타입이다.
```

- 다른 언어였다면 런타임 오류가 될 만한 코드이다. 하지만 타입스크립트 타입 체커는 정상으로 인식한다. 두 줄 모두 문자열 “23”이 되는 자바스크립트 런타임 동작으로 모델링된다.
- 반대로 정상 동작하는 코드에 오류를 표시하기도 한다. 런타임 오류가 발생하지 않는 코드인데, 타입 체커는 문제점을 표시한다.

```tsx
const a = null + 7; // 자바스크립트에서는 a 값이 7이 된다.
										// ~~~ '+' 연산자를 ... 형식에 적용할 수 없습니다.
const b = [] + 12;  // 자바스크립트에서는 b 값이 '12'가 된다.
										// ~~~ '+' 연산자를 ... 형식에 적용할 수 없습니다.
alert('Hello', 'TypeScript'); // "Hello"에 경고를 표 시한다.
															// ~~~ 0-1개의 인수가 필요한데 2개를 가져왔습니다.
```

- 자바스크립트의 런타임 동작을 모델링하는 것은 타입스크립트 타입 시스템의 기본 원칙이다. 그러나 위에서 봤던 경우처럼 단순히 런타임 동작을 모델링 하는 것 뿐만 아니라 의도치 않은 이상한 코드가 오류로 이어질 수도 있다는 점까지 고려해야 한다.
- 작성된 프로그램이 타입 체크를 통과하더라도 여전히 런타임에 오류가 발생할 수 있다.

```tsx
const names = ['Alice', 'Bob'];
console.log(names[2].toUpperCase()); // TypeError: Cannot read property 'toUpperCase' of undefined
```

- 타입스크립트는 앞의 배열이 범위 내에서 사용될 것이라 가정했지만 실제로는 그렇지 않았고, 오류가 발생한다.
- 앞서 등장한 오류들이 발생하는 근본 원인은 타입스크립트가 이해하는 값의 타입과 실제 값에 차이가 있기 때문이다. 타입 시스템이 정적 타입의 정확성을 보장해 줄 것 같지만 그렇지 않다. 애초에 타입 시스템은 그런 목적으로 만들어지지도 않았다.
- 정확성을 보장하는 것이 중요하다면 Reason이나 Elm 같은 언어를 선택하는 것이 좋다. 그러나 이러한 언어에도 단점이 존재한다. 이 언어들은 런타임 안정성을 보장하는 대신 자바스크립트의 상위집합이 아니기 때문에 마이그레이션 과정이 훨씬 복잡하다.

### 🌻 요약

- 타입스크립트는 자바스크립트의 상위집합이다. 모든 자바스크립트 프로그램은 이미 타입스크립트 프로그램이다. 반대로 타입스크립트는 별도의 문법을 가지고 있기 때문에 일반적으로는 유효한 자바스크립트 프로그램이 아니다.
- 타입스크립트는 자바스크립트 런타임 동작을 모델링하는 타입 시스템을 가지고 있기 때문에 런타임 오류를 발생시키는 코드를 찾아내려 한다. 그러나 모든 오류를 찾아내리라 기대하면 안된다. 타입 체커를 통과하면서도 런타임 오류를 발생시키는 코드는 충분히 존재할 수 있다.
- 타입스크립트 타입 시스템은 전반적으로 자바스크립트 동작을 모델링한다. 그러나 잘못된 매개변수 개수로 함수를 호출하는 경우처럼, 자바스크립트에서 허용되지만 타입스크립트에서는 문제가 되는 경우도 있다.

## 🍄 2. 타입스크립트 설정 이해하기

```tsx
function add(a, b) {
	return a + b;
}
add(10, null);
```

- 위 코드는 오류 없이 타입 체커를 통과할 수 있을까? 설정이 어떻게 되어 있는지 모른다면 대답할 수 없는 질문이다. 타입스크립트 컴파일러는 매우 많은 설정을 가지고 있다. 설정이 거의 100개에 이른다. 이 설정들은 커맨드 라인에서 사용할 수 있다.

```bash
$ tsc --noImplicitAny program.ts
```

- tsconfig.json 설정 파일을 통해서도 가능하다.

```json
{
	"compilerOptions" : {
		"noImplicitAny": true
	}
}
```

- 가급적 설정 파일을 사용하는 것이 좋다. 그래야 타입스크립트를 어떻게 사용할 계획인지 동료들이나 다른 도구들이 알 수 있다. 설정 파일은 `tsc --init` 만 실행하면 간단히 생성된다.
- 타입스크립트의 설정들은 어디서 소스 파일을 찾을지, 어떤 종류의 출력을 생성할지 제어하는 내용이 대부분이다. 그러나 언어 자체의 핵심 요소들을 제어하는 설정도 있다.
- 타입스크립트는 어떻게 설정하느냐에 따라 완전히 다른 언어처럼 느껴질 수 있다. 설정을 제대로 사용하려면, noImplicitAny와 strictNullChecks를 이해해야 한다.
- noImplicitAny는 변수들이 미리 정의된 타입을 가져야 하는지 여부를 제어한다. 다음 코드는 noImplicitAny가 해제되어 있을 때에는 유효하다.

```tsx
function add(a,b) {
	return a+b;
}
```

- 편집기에서 add 부분에 마우스를 올려 보면 타입스크립트가 추론한 함수의 타입을 알 수 있다.

```tsx
function add(a: any, b:any): any
```

- any 타입을 매개변수에 사용하면 타입 체커는 속절없이 무력해진다. any는 유용하지만 매우 주의해서 사용해야 한다.

```tsx
function add(a, b) {
		// ~ 'a' 매개변수에는 암시적으로 'any' 형식이 포함됩니다.
		// ~ 'b' 매개변수에는 암시적으로 'any' 형식이 포함됩니다.
		return a+b;
}
```

- any를 코드에 넣지 않았지만, any 타입으로 간주되기 때문에 이를 암시적 any라 부른다. 그런데 같은 코드임에도 `noImplicitAny` 가 설정되었다면 오류가 된다. 이 오류들은 명시적으로 : any라고 선언해 주거나 더 분명한 타입을 사용하면 해결할 수 있다.

```tsx
function add(a: number, b: number){
	return a + b;
}
```

- 타입스크립트는 타입 정보를 가질 때 가장 효과적이기 때문에, 되도록이면 `noImplicitAny` 를 설정해야 한다.
- 새 프로젝트를 시작한다면 처음부터 `noImplicitAny` 를 설정하여 코드를 작성할 때마다 타입을 명시하도록 해야 한다. 그러면 타입스크립트가 문제를 발견하기 수월해지고, 코드의 가독성이 좋아지며, 개발자의 생산성이 향산된다.
- `noImplicitAny` 설정 해제는, 자바스크립트로 되어 있는 기존 프로젝트를 타입스크립트로 전환하는 상황에만 필요하다.
- `strictNullCheck` 는 null과 undefined가 모든 타입에서 허용되는지 확인하는 설정이다.

```tsx
// strictNullChecks 해제
const x: number = null; // 정상, null은 유효한 값이다.

// strictNullChecks 설정
const x: number = null; // ~ 'null' 형식은 'number' 형식에 할당할 수 없습니다.
```

- null 대신 undefined를 써도 같은 오류가 나며, 만약 null을 허용하려고 한다면, 의도를 명시적으로 드러냄으로써 오류를 고칠 수 있다.

```tsx
const x: number | null = null;
```

- 만약 null을 허용하지 않으려면, 이 값이 어디서부터 왔는지 찾아야 하고, null을 체크하는 코드나 단어문을 추가해야 한다.

```tsx
const el = document.getElementById('status');
el.textContent = 'Ready';
// ~~~ 개체가 'null'인 것 같습니다.

if(el){
	el.textContent = 'Ready'; // 정상, null은 제외된다.
}
el!.textContent = 'Ready'; // 정상, el이 null이 아님을 단언한다.
```

- strictNullChecks는 null 과 undefined 관련된 오류를 잡아 내는 데 많은 도움이 되지만, 코드 작성을 어렵게 한다. 새 프로젝트를 시작하는 거라면 `strictNullChecks` 를 설정하는 것이 좋지만, 타입스크립트가 처음이거나 마이그레이션 중이라면 설정하지 않아도 된다.
- `strictNullChecks` 를 설정하려면 `noImplicitAny` 를 먼저 설정해야 한다.
- 언어에 의미적으로 영향을 미치는 설정들이 많지만, `noImplicitAny` 와 `strictNullChecks` 만큼 중요한 것은 없다. 이 모든 체크를 설정하고 싶다면 strict 설정을 하면 된다.
- 공동 프로젝트를 진행하는 도중 타입스크립트 예제를 공유했는데 오류가 재현되지 않는다면, 컴파일러 설정이 동일한지 먼저 확인해야 한다.

### 🌻 요약

- 타입스크립트 컴파일러는 언어의 핵심 요소에 영향을 미치는 몇 가지 설정을 포함하고 있다.
- 타입스크립트 설정은 커맨드 라인을 이용하기보다는 tsconfig.json을 사용하는 것이 좋다.
- 자바스크립트 프로젝트를 타입스크립트로 마이그레이션 하는게 아니라면 `noImplicitAny` 를 설정하는 것이 좋다.
- “undefined는 객체가 아닙니다”같은 런타임 오류를 방지하기 위해 `strictNullChecks` 를 설정하는 것이 좋다.
- 타입스크립트에서 엄격한 체크를 하고 싶다면 strict 설정을 고려해야 한다.

## 🍄 3. 코드 생성과 타입이 관계없음을 이해하기

- 타입스크립트 컴파일러는 두 가지 역할을 수행한다.
    - 최신 타입스크립트/자바스크립트를 브라우저에서 동작할 수 있도록 구버전의 자바스크립트로 트랜스파일(transpile)한다.
    - 코드의 타입 오류를 체크한다.
- 타입스크립트가 자바스크립트로 변환될 때 코드 내의 타입에는 영향을 주지 않는다. 또한 자바스크립트의 실행 시점에도 타입에 영향을 미치지 않는다.

### 🌻 3-1. 타입 오류가 있는 코드도 컴파일이 가능하다.

- 컴파일은 타입 체크와 독립적으로 동작하기 때문에, 타입 오류가 있는 코드도 컴파일이 가능하다.

```tsx
// $ cat test.ts
let x = 'hello';
x = 1234;

// $ tsc test.ts
// test.ts:2:1 - error TS2322: '1234'형식은 'string'형식에 할당할 수 없습니다.

// $ cat test.js
var x = 'hello';
x = 1234;
```

- 타입스크립트 오류는 C나 자바 같은 언어들의 경고와 비슷하다. 문제가 될 만한 부분을 알려주지만, 그렇다고 빌드를 멈추지 않는다.
- 타입 오류가 있는 데도 컴파일된다는 사실 때문에 타입스크립트가 엉성한 언어처럼 보일 수 있다. 그러나 코드에 오류가 있더라도 컴파일된 산출물이 나오는 것이 실제로 도움이 된다.
- 웹 애플리케이션을 만들면서 어떤 문제가 발생했다고 가정했을 때, 타입스크립트는 여전히 컴파일된 산출물을 생성하기 때문에, 문제가 된 오류를 수정하지 않더라도 애플리케이션의 다른 부분을 테스트 할 수 있다.
- 만약 오류가 있을 때 컴파일하지 않으려면 tsconfig.json에 `noEmitError` 를 설정하거나 빌드 도구에 동일하게 적용하면 된다.

### 🌻 3-2. 런타임에는 타입 체크가 불가능하다.

```tsx
interface Square {
	width: number;
}
interface Rectangle extends Square {
	height: number;
}
type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
	if(shape instanceof Rectangle) {
		// ~~~ Rectangle은 형식만 참조하지만, 여기서는 값으로 사용되고 있다.
		return shape.width * shape.height; // ~~~ Shape 형식에 height 속성이 없다.
	}else{
		return shape.width * shape.width
	}
}
```

- instanceof 체크는 런타임에 일어나지만, Rectangle은 타입이기 때문에 런타임 시점에 아무런 역할을 할 수 없다. 타입스크립트의 타입은 ‘제거 가능’하다. 실제로 자바스크립트로 컴파일되는 과정에서 모든 인터페이스, 타입, 타입 구문은 그냥 제거되어 버린다.
- shape 타입을 명확하게 하려면, 런타임에 타입 정보를 유지하는 방법이 필요하다. 하나의 방법은 height 속성이 존재하는지 체크해 보는 것이다.

```tsx
function calculateArea(shape: Shape){
	if('height' in shape){
		return shape.width * shape.height;
	}else {
		return shape.width * shape.width;
	}
}
```

- 속성 체크는 런타임에 접근 가능한 값에만 관련되지만, 타입 체커 역시도 shape 타입을 Rectangle로 보정해주기 때문에 오류가 사라진다.
- 타입 정보를 유지하는 또 다른 방법으로는 런타임에 접근 가능한 타입 정보를 명시적으로 저장하는 ‘태그’기법이 있다.

```tsx
interface Square {
	kind: 'square';
	width: number;
}
interface Rectangle {
	kind: 'rectangle';
	height: number;
	width: number;
}
type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
	if(shape.kind === 'rectangle'){
		return shape.width * shape.height;
	}else{
		return shape.width * shape.width;
	}
}
```

- 여기서 Shape 타입은 태그된 유니온의 한 예이다. 이 기법은 런타임에 타입 정보를 손쉽게 유지할 수 있기 때문에 타입스크립트에서 흔하게 볼 수 있다.
- 타입과 값을 둘 다 사용하는 기법도 있다. 타입을 클래스로 만들면 된다.

```tsx
class Square {
	constructor(public width: number) {}
}

class Rectangle extends Square {
	constructor(public width: number, public height: number) {
		super(width);
	}
}

type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
	if(shape instanceof Rectangle){
		return shape.width * shape.height;
	}else{
		return shape.width * shape.width;
	}
}
```

- 인터페이스는 타입으로만 사용 가능하지만, Rectangle을 클래스로 선언하면 타입과 값으로 모두 사용할 수 있으므로 오류가 없다.

### 🌻 3-3. 타입 연산은 런타임에 영향을 주지 않는다.

```tsx
function asNumber(val: number|string): number {
	return val as number;
}
```

```jsx
function asNumber(val){
	return val
}
```

- 변환된 코드를 보면 코드에 아무런 정제 과정이 없다. as number는 타입 연산이고 런타임 동작에는 아무런 영향을 미치지 않는다. 값을 정재하기 위해서는 런타임의 타입을 체크해야 하고 자바스크립트 연산을 통해 변환을 수행해야 한다.

```tsx
function asNumber(val: number| string):number {
	return typeof(val) === 'string' ? Number(val) : val;
}
```

### 🌻 3-4. 런타임 타입은 선언된 타입과 다를 수 있다.

```tsx
function setLightSwitch(value: boolean){
	switch(value) {
		case true:
			turnLightOn();
			break;
		case false:
			turnLightOff();
			break;
		default:
			console.log('실행되지 않을까 봐 걱정입니다.');
	}
}
```

- 타입스크립트는 일반적으로 실행되지 못하는 죽은(dead) 코드를 찾아내지만, 여기서는 strict를 설정하더라도 찾아내지 못한다. 그러면 마지막 부분을 실행할 수 있는 경우는 무엇일까?
- : boolean 타입 선언문이라는 것에 주목하자. 타입스크립트의 타입이기 때문에 :boolean은 런타임에 제거된다. 자바스크립트였다면 실수로 setLightSwicth를 “ON”으로 호출할 수도 있었을 것이다.
- 순수 타입스크립트에서도 마지막 코드를 실행하는 방법이 존재한다. 예를 들어, 네트워크 호출로부터 받아온 값으로 함수를 실행하는 경우가 있다.

```tsx
interface LightApiResponse {
	lightSwitchValue: boolean;
}
async function setLight() {
	const response = await fetch('/light');
	const result: LightApiResponse = await response.json();
	setLightSwitch(result.lightSwitchValue);
}
```

- /light를 요청하면 그 결과로 LightApiResponse를 반환하라고 선언했지만, 실제로 그렇게 되리라는 보장은 없다. API를 잘못파악해서 value값이 실제로는 문자열이었다면, 런타임에는 setLightSwitch 함수까지 전달 될 것이다. 또 배포된 후 API가 변경되어 lightSwitchValue가 문자열이 되는 경우도 있을 것이다.
- 타입스크립트에서는 런타임 타입과 선언된 타입이 맞지 않을 수 있다. 타입이 달라지는 혼란스러운 상황을 가능한 한 피해야 한다. 선언된 타입이 언제든지 달라질 수 있다는 것을 명심하자.

### 🌻 3-5. 타입스크립트 타입으로는 함수를 오버로드 할 수 없다.

- 동일한 이름에 매개변수만 다른 여러 버전의 함수를 허용한다. 이를 ‘함수 오버로딩’이라 한다. 그러나 타입스크립트에서는 타입과 런타임의 동작이 무관하기 때문에, 함수 오버로딩은 불가능하다.

```tsx
function add(a: number, b: number) { return a+b; }
// ~~~ 중복된 함수 구현
function add(a: string, b: string) { return a+b; }
// ~~~ 중복된 함수 구현
```

- 타입스크립트가 함수 오버로딩 기능을 지원하기는 하지만, 온전히 타입 수준에서만 동작한다. 하나의 함수에 대해 여러 개의 선언문을 작성할 수 있지만, 구현체는 오직 하나 뿐이다.

```tsx
function add(a: number, b: number): number
function add(a: string, b: string): string

function add(a, b){
	return a + b;
}

const three = add(1,2);
const twelve = add('1', '2');
```

- add에 대한 처음 두 개의 선언문은 타입 정보를 제공할 뿐이다. 이 두 선언문은 자바스크립트로 변환되면서 제거되며, 구현체만 남게 된다.

### 🌻 3-6. 타입스크립트 타입은 런타임 성능에 영향을 주지 않는다.

- 타입과 타입 연산자는 자바스크립트 변환 시점에 제거되기 때문에, 런타임의 성능에 아무런 영향을 주지 않는다.
- 타입스크림트의 정적 타입은 실제로 비용이 전혀 들지 않는다.

### 🌻 요약

- 코드 생성은 타입 시스템과 무관하다. 타입스크립트 타입은 런타임 동작이나 성능에 영향을 주지 않는다.
- 타입 오류가 존재하더라도 코드 생성(컴파일)은 가능하다.
- 타입스크립트 타입은 런타임에 사용할 수 없다. 런타임에 타입을 지정하려면, 타입 정보 유지를 위한 별도의 방법이 필요하다. 일반적으로는 태그된 유니온과 속성 체크 방법을 사용한다. 또는 클래스 같이 타입스크립트 타입과 런타임 값 둘 다 제공하는 방법이 있다.

## 🍄 4. 구조적 타이핑에 익숙해지기

- 자바스크립트는 본질적으로 덕 타이핑 기반이다. 만약 어떤 함수의 매개변수 값이 모두 제대로 주어진다면, 그 값이 어떻게 만들어졌는지 신경 쓰지 않고 사용한다.
- 타입스크립트는 매개변수 값이 요구사항을 만족한다면 타입이 무엇인지 신경 쓰지 않는 동작을 그대로 모델링한다.
- 구조적 타이핑을 제대로 이해한다면 오류인 경우와 오류가 아닌 경우의 차이를 알 수 있고, 더욱 견고한 코드를 작성할 수 있다.

```tsx
interface Vector2D {
	x: number;
	y: number;
}

// 벡터의 길이 계산
function calculateLength(v: Vector2D) {
	return Math.sqrt(v.x * v.x + v.y * v.y);
}
```

- 이제 이름이 들어간 벡터를 추가한다.

```tsx
interface NamedVector {
	name: string;
	y: number;
	x: number;
}
```

- NamedVector는 number 타입의 x와 y 속성이 있기 때문에 calculateLength 함수로 호출 가능하다.

```tsx
const v: NamedVector = { x: 3, y: 4, name: 'Zee' };
calculateLength(v); // 정상, 결과는 5
```

- 흥미로운 점은 Vector2D와 NamedVector 관계를 전혀 선언하지 않았다는 것이다. 그리고 NamedVector를 위한 별도의 calculateLength를 구현할 필요도 없다.
- 타입스크립트 타입 시스템은 자바스크립트의 런타임 동작을 모델링한다. NamedVector의 구조가 Vector2D와 호환되기 때문에 calculateLength 호출이 가능하다. 여기서 **구조적 타이핑**이라는 용어가 사용된다.
- 구조적 타이핑 때문에 문제가 발생하기도 한다.

```tsx
interface Vector3D {
	x: number;
	y: number;
	z: number;
}

// 벡터의 길이를 1로 만드는 정규화 함수
function normalize(v: Vector3D) {
	const length = calculateLength(v);
	return {
		x: v.x / length;
		y: v.y / length;
		z: v.z / length;
	}
}
```

- 그러나 이 함수는 1보다 조금 더 긴 길이를 가진 결과를 출력한다.
- 타입스크립트가 오류를 잡지 못한 이유는 calculateLength는 2D 벡터를 기반으로 연산하는데, 버그로 인해 normalize가 3D 벡터로 연산되었다. z가 정규화에서 무시된 것이다. 그런데 타입 체커가 문제를 잡지 못하였다. 이는 x와 y가 있어 Vector2D로 호환되면서 발생한 문제이다.
- 함수를 작성할 때 호출에 사용되는 매개변수의 속성들이 매개변수의 타입에 선언된 속성만을 가질 거라 생각하기 쉽다. 이러한 타입은 봉인된 또는 정확한 타입이라고 불리며, 타입스크립트 타입 시스템에서는 표현할 수 없다. 좋든 싫든 타입은 열려있다. 이러한 특성 때문에 가끔 당황스러운 결과가 발생한다.
- 구조적 타이핑은 클래스와 관련된 할당문에서도 당황스러운 결과를 보여준다.

```tsx
class C {
	foo: string;
	constructor(foo: string) {
		this.foo = foo;
	}
}

const c = new C('instance of C');
const d:C = {foo: 'object literal' }; // 정상
```

- d가 C타입에 할당되는 이유는 d는 string타입의 foo 속성을 가진다. 게다가 하나의 매개변수로 호출 되는 생성자를 가진다. 그래서 구조적으로는 필요한 속성과 생성자가 존재하기 때문에 문제가 없다. 만약 C의 생성자에 단순 할당이 아닌 연산 로직이 존재한다면, d의 경우 생성자를 실행하지 않으므로 문제가 발생할 것이다.
- 테스트를 작성할 때는 구조적 타이핑이 유리하다.
- 테스트 이외에 구조적 타이핑의 또 다른 장점은 라이브러리 간의 의존성을 완벽히 분리할 수 있다는 것이다.

### 🌻 요약

- 자바스크립트가 덕 타이핑 기반이고 타입스크립트가 이를 모델링하기 위해 구조적 타이핑을 사용함을 이해해야 한다. 어떤 인터페이스에 할당 가능한 값이라면 타입 선언에 명시적으로 나열된 속성들을 가지고 있을 것이다. 타입은 봉인되어 있지 않다.
- 클래스 역시 구조적 타이핑 규칙을 따른다는 것을 명심해야 한다. 클래스의 인스턴스가 예상과 다를 수 있다.
- 구조적 타이핑을 사용하면 유닛 테스팅을 손쉽게 할 수 있다.

## 🍄 5. any 타입 지양하기

- 타입스크립트의 타입 시스템은 점진적이고 선택적이다.
- 코드에 타입을 조금씩 추가할 수 있기 때문에 점진적이며, 언제든지 타입 체커를 해제할 수 있기 때문에 선택적이다. 이 기능들의 핵심은 any 타입이다.

```tsx
let age: number;
age = '12';
// ~~~ '12' 형식은 'number' 형식에 할당할 수 없습니다.
age = '12' as any; // OK
```

- 타입 체커를 통해 위 코드의 오류를 찾아낼 수 있으며 오류는 as any를 추가해 해ㄹ결할 수 있다.
- 타입선언을 추가하는 데에 시간을 쏟고 싶지 않아서 any 타입이나 타입 단언문(as any)을 사용하고 싶기도 할텐데 일부 특별한 경우를 제외하고는 any를 사용하면 타입스크립트의 수많은 장점을 누릴 수 없게 된다. 부득이하게 any를 사용하더라도 그 위험성을 알고 있어야 한다.

### 🌻 5-1. any 타입에는 타입 안정성이 없다.

- 위의 예제에서 age는 number 타입으로 선언되었다. 그러나 as any를 사용하면 string 타입을 할당할 수 있게 된다. 타입 체커는 선언에 따라 number 타입으로 판단할 것이고 혼돈은 걷잡을 수 없게 된다.

### 🌻 5-2. any는 함수 시그니처를 무시해 버린다.

- 함수를 작성할 때는 시그니처를 명시해야 한다. 호출하는 쪽은 약속된 타입의 입력을 제공하고, 함수는 약속된 타입의 출력을 반환한다. 그러나 any 타입을 사용하면 이런 약속도 어길 수 있다.

```tsx
function calculateAge(birthDate: Date): number {
	// ...
}

let birthDate: any = '1990-01-19';
calculateAge(birthDate); // 정상
```

- any 타입을 사용하면 calculateAge의 시그니처를 무시하게 된다.
- 자바스크립트에서는 종종 암시적으로 타입이 변환되기 때문에 이런 경우 특히 문제가 될 수 있다. string 타입은 number 타입이 필요한 곳에서 오류 없이 실행될 때가 있고, 그럴 경우 다른 곳에서 문제를 일으키게 된다.

### 🌻 5-3. any 타입에는 언어 서비스가 적용되지 않는다.

- 어떤 심벌에 타입이 있다면 타입스크립트 언어 서비스는 자동완성 기능과 적절한 도움말을 제공한다.
- 그러나 any 타입인 심벌을 사용하면 아무런 도움을 받지 못한다.
- 이름 변경 기능은 또 다른 언어 서비스이다. 다음 코드는 Person 타입과 이름을 포매팅 해주는 함수이다.

```tsx
interface Person {
	first: string;
	last: string;
}

const formatName = (p: Person) => `${p.first} ${p.last}`;
const formatNameAny = (p: Person) => `${p.first} ${p.last}`;
```

- 편집기에서 interaface의 first를 name으로 바꾸면 formatName의 first는 name으로 바뀌지만 formatNameAny는 바뀌지 않는다.
- 타입스크립트의 모토는 확장 가능한 자바스크립트이다. 확장의 중요한 부분은 바로 타입스크립트 경험의 핵심 요소인 언어 서비스이다.

### 🌻 5-4. any 타입은 코드 리팩터링 때 버그를 감춘다.

```tsx
interface ComponentProps {
	onSelectItem: (item: any) => void;
}

function renderSelector(props: ComponentProps) { /* ... */}

let selectedId: number = 0;

function handleSelectItem(item: any) {
	selectedId = item.id;
}

renderSelector({ onSelectItem: handleSelctItem });
```

- 여기서 onSelecItem에 아이템 객체를 필요한 부분만 전달하도록 리팩토링 해보자. 여기서는 id만 필요하다.

```tsx
interface ComponentProps{
	onSelectItem: (id: number) => void;
}
```

- 컴포넌트를 수정하고 타입체크를 통과했다. 하지만 handleSelectItem은 any 매개 변수를 받는다. 따라서 id를 전달받아도 문제가 없다고 나온다. id를 전달 받으면, 타입 체커를 통과함에도 불구하고 런타임에는 오류가 발생할 것이다.
- any가 아니라 구체적인 타입을 사용했다면, 타입 체커가 오류를 발견했을 것이다.

### 🌻 5-5. any는 타입 설계를 감춰버린다.

- 애플리케이션 상태 같은 객체를 정의하려면 꽤 복잡하다. 상태 객체 안에 있는 수많은 속성의 타입을 일일이 작성해야 하는데, any 타입을 사용하면 간단히 끝내버릴 수 있다.
- 물론 이때도 any를 사용하면 안된다. 객체를 정의할 때 특히 문제가 되는데, 상태 객체의 설계를 감춰버리기 때문이다.
- any 타입을 사용하면 타입 설계가 불분명해진다. 설계가 잘되었느지, 설계가 어떻게 되어 있는지 전혀 알 수 없다. 만약 동료가 코드를 검토해야 한다면, 동료는 애플리케이션의 상태를 어떻게 변경했는지 코드부터 재구성해봐야 할 것이다. 그러므로 설계가 명확히 보이도록 타입을 일일이 작성하는 것이 좋다.

### 🌻 5-6. any는 타입시스템의 신뢰도를 떨어뜨린다.

- 사람은 항상 실수를 한다. 보통은 타입 체커가 실수를 잡아주고 코드의 신뢰도가 높아진다. 그러나 런타임에 타입 오류를 발견하게 된다면 타입 체커를 신뢰할 수 없을 것이다.
- any 타입을 쓰지 않으면 런타임에 발견될 오류를 미리 잡을 수 있고 신뢰도를 높일 수 있다.
- 타입스크립트는 개발자의 삶을 편하게 하는 데 목적이 있지만, 코드 내에 존재하는 수 많은 any 타입으로 인해 자바스크립트보다 일을 더 어렵게 만들기도 한다. 타입 오류를 고쳐야 하고 여전히 머릿속에 실제 타입을 기억해야 하기 때문이다.

### 🌻 요약

- any 타입을 사용하면 타입 체커와 타입스크립트 언어 서비스를 무력화시켜버린다. any 타입은 진짜 문제점을 감추며, 개발 경험을 나쁘게 하고, 타입 시스템의 신뢰도를 떨어뜨린다. 최대한 사용을 피하자.
