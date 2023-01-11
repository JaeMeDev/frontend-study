# 🌸 Chapter 6: 변경 가능한 데이터 구조를 가진 언어에서 불변성 유지하기

## 🍄 1. 모든 동작을 불변형으로 만들 수 있을까?

- 앞으로 카피-온-라이트를 적용해야 하거나 적용해야 할지도 모르는 장바구니와 제품에 대한 동작
- 장바구니에 대한 동작
    1. 제품 개수 가져오기
    2. 제품 이름으로 제품 가져오기
    3. 제품 추가하기
    4. 제품 이름으로 제품 빼기
    5. 제품 이름으로 제품 구매 수량 바꾸기
- 제품에 대한 동작
    1. 가격 설정하기
    2. 가격 가져오기
    3. 이름 가져오기
- 모든 동작을 불변형으로 만들수 있을까? 특히 다섯 번째 동작은 장바구니 안에 제품의 제품의 정보를 바꿔야 한다. 이런 것을 **중첩된 데이터**라 한다.
- 데이터 구조 안에 데이터 구조가 있는 경우 데이터가 **중첩**되었다고 말한다. 배열안에 객체가 있다면 **중첩된 데이터**이다.
- **깊이 중첩**되었다는 말은 중첩이 이어진다는 말이다. 깊다는 말은 상대적이다. 어떤 객체가 객체 안에 또 다시 배열 안에, 객체 안에 있는 것처럼 중첩은 계속 이어질 수 있다.

## 🍄 2. 동작을 읽기, 쓰기 또는 둘 다로 분류하기

- 동작을 읽기 또는 쓰기 또는 둘 다 하는 것으로 분류할 수 있다.
- 어떤 동작은 **읽기**이다. 읽기 동작은 데이터를 바꾸지 않고 정보를 꺼내는 것이다. 데이터가 바뀌지 않기 때문에 다루기 쉽다. 만약 인자에만 의존해 정보를 가져오는 읽기 동작이라면 계산이라고 할 수 있다.
- 다른 동작은 **쓰기**이다. 쓰기 동작은 어떻게든 데이터를 바꾼다. 바뀌는 값은 어디서 사용될지 모르기 때문에 바뀌지 않도록 원칙이 필요하다.
- 장바구니에 대한 동작 중 세 개는 쓰기 동작이다. 쓰기 동작은 불변성 원칙에 따라 구현해야 한다. 불변성 원칙은 **카피-온-라이트(copy-on-write)**라고 한다. 하스켈이나 클로저같은 언어도 이 원칙을 쓰고 있다.
- 자바스크립트는 기본적으로 변경 가능한 데이터 구조를 사용하기 때문에 불변성 원칙을 적용하려면 직접 구현해야 한다.

## 🍄 3. 카피-온-라이트 원칙 세 단계

- 카피-온-라이트는 세 단계로 되어 있다. 각 단계를 구현하면 카피-온-라이트가 동작한다.
    1. 복사본 만들기
    2. 복사본 변경하기(원하는 만큼)
    3. 복사본 리턴하기
- 지난번 카피-온-라이트로 구현한 `add_element_last()` 를 확인해보자.

```jsx
function add_element_last(array, elem) {
	var new_array = array.slice(); // 1. 복사본 만들기
	new_array.push(elem); // 2. 복사본 바꾸기
	return new_array; // 3. 복사본 리턴하기
}
```

- 이 함수는 어떻게 동작할까?
    1. 배열을 복사했고, 기존 배열을 변경하지 않았다.
    2. 복사본은 함수 범위에 있기 때문에 다른 코드에서 값을 바꾸기 위해 접근할 수 없다.
    3. 복사본을 변경하고 나서 함수를 나간다.(리턴) 이후에는 값을 바꿀 수 없다.
- `add_element_last` 읽기일까, 쓰기일까? 데이터를 바꾸지 않고 정보를 리턴했기 때문에 읽기이다.
- 카피-온-라이트는 쓰기를 읽기로 바꾼다.

## 🍄 4. 카피-온-라이트로 쓰기를 읽기로 바꾸기

```jsx
// 제품 이름으로 장바구니에서 제품을 빼는 함수
function remove_item_by_name(cart, name){
	var idx = null;
	for(var i = 0; i < cart.length; i++){
		if(cart[i].name === name){
			idx = i;
		}
	}
	if(idx !== null){
		cart.splice(idx, 1);
	}
}
```

- `cart.splice()` 가 하는 일은 무엇일까? `.splice()` 메서드는 배열에서 항목을 삭제하는 메서드이다.
- 이 함수는 장바구니를 변경한다. 만약 함수에 전역변수를 넘겼다면 전역변수인 장바구니가 변경된다.
- 장바구니를 변경 불가능한 데이터로 쓰려고 한다면 카피-온-라이트를 적용해야 한다.

```jsx
function remove_item_by_name(cart, name){
	var new_cart = cart.slice();
	var idx = null;
	for(var i = 0; i < cart.length; i++){
		if(cart[i].name === name){
			idx = i;
		}
	}
	if(idx !== null){
		cart.splice(idx, 1);
	}
}
```

- 카피-온-라이트의 첫번째 단계인 복사본 만들기를 수행하였다.

```jsx
function remove_item_by_name(cart, name){
	var new_cart = cart.slice();
	var idx = null;
	for(var i = 0; i < new_cart.length; i++){
		if(new_cart[i].name === name){
			idx = i;
		}
	}
	if(idx !== null){
		new_cart.splice(idx, 1);
	}
}
```

- 카피-온-라이트의 두번째 단계인 복사본 변경하기를 수행하였다. 하지만 복사본은 아직 함수 안에만 있다.

```jsx
function remove_item_by_name(cart, name){
	var new_cart = cart.slice();
	var idx = null;
	for(var i = 0; i < new_cart.length; i++){
		if(new_cart[i].name === name){
			idx = i;
		}
	}
	if(idx !== null){
		new_cart.splice(idx, 1);
	}

	return new_cart;
}
```

- 카피-온-라이트 마지막 단계인 복사본 리턴하기를 수행하였다. 이제 `remove_item_by_name()` 함수는 카피-온-라이트 버전으로 바뀌었다. 현재코드에서 호출부를 수정해보자.

```jsx
// 현재 코드
function delete_handler(name){
	remove_item_by_name(shopping_cart, name);
	var total = calc_total(shopping_cart);
	set_cart_total_dom(total);
	update_shipping_icons(shopping_cart);
	update_tax_dom(total);
}

// 수정 코드
function delete_handler(name){
	shopping_cart = remove_item_by_name(shopping_cart, name);
	var total = calc_total(shopping_cart);
	set_cart_total_dom(total);
	update_shipping_icons(shopping_cart);
	update_tax_dom(total);
}
```

## 🍄 6. 앞에서 만든 카피-온-라이트 동작은 일반적이다.

- 앞으로 적용할 카피-온-라이트 동작도 위에서 만든 동작과 비슷하다. 그래서 `add_element_last()` 함수처럼 재사용하기 쉽도록 일반화할 수 있다.
- 배열에 `.splice()` 메서드를 일반화해보자.

```jsx
// 현재 코드
function removeItems(array, idx, count){
	array.splice(idx, count);
}

// 수정 코드
function removeItems(array, idx, count){
	var copy = array.slice();
	copy.splice(idx, count);
	return copy;
}
```

- 이제 `removeItems` 에 맞게 `remove_item_by_name` 을 수정해보자.

```jsx
function remove_item_by_name(cart, name){
	var idx = null;
	for(var i = 0; i < cart.length; i++){
		if(cart[i].name === name){
			idx = i;
		}
	}
	if(idx !== null){
		// 수정부분
		return removeItems(cart, idx, 1);
	}
	
	// 값을 바꾸지 않으면 복사하지 않아도 된다.
	return cart;
}
```

## 🍄 7. 자바스크립트 배열 훑어보기

- 배열은 자바스크립트에 기본 컬렉션이다.
- 자바스크립트 배열은 순서 있는 값을 나타내는 컬렉션이다.
- 배열은 다른 타입의 항목을 동시에 가질 수 있다.
- 그리고 인덱스로 접근할 수 있으며 자바나 C에서 말하는 배열과 다르게 크기를 늘리거나 줄일 수 있다.

## 🍄 8. 연습문제1

```jsx
// 기존 코드 (메일링 리스트에 연락처를 추가하는 코드)
var mailing_list = [];

function add_contact(email){
	mailing_list.push(email);
}

function submit_form_handler(event){
	var form = event.target;
	var email = form.elements["email"].value;
	add_contact(email);
}

// 수정 코드 (카피-온-라이트 적용)
var mailing_list = [];

function add_contact(mailing_list, email){
	const list_copy = mailing_list.slice();
	list_copy.push(email);
	return list_copy;
}

function submit_form_handler(event){
	var form = event.target;
	var email = form.elements["email"].value;
	mailing_list = add_contact(mailing_list, email);
}
```

## 🍄 9. 쓰기를 하면서 읽기도 하는 동작은 어떻게 해야 할까?

- 어떤 동작은 읽고 변경하는 일을 동시에 한다. 이런 동작은 값을 변경하고 리턴한다. `.shift()` 가 좋은 예제이다.

```jsx
var a = [1,2,3,4];
var b = a.shift();
console.log(b); // 1 (값을 리턴)
console.log(a); // [2,3,4] (값이 바뀜)
```

- `.shift()` 메서드는 값을 바꾸는 동시에 배열의 첫 번째 항목을 리턴한다. 변경하면서 읽는 동작이다. 이 동작을 카피-온-라이트로 어떻게 바꿀 수 있을까?
- 카피-온-라이트에서 쓰기를 읽기로 바꿨다. 읽기라는 말은 값을 리턴한다는 의미이다. 하지만 `.shift()` 메서드는 이미 읽기이다. 값을 리턴하고 있기 때무이다. 어떻게 해야할까?
    1. 읽기와 쓰기 함수로 각각 분리한다.
    2. 함수에서 값을 두 개 리턴한다.

## 🍄 10. 쓰면서 읽기도 하는 함수를 분리하기

### 🌻 10-1. 읽기와 쓰기 동작으로 분리하기

- `.shift()` 메서드의 읽기 동작은 값을 단순히 리턴하는 동작이다. `.shift()` 메서드가 리턴하는 값은 배열의 첫 번째 항목이다. 따라서 배열에 첫 번째 항목을 리턴하는 계산 함수를 만들면 된다. 이렇게 만든 함수는 읽기 동작만 할 뿐 아무것도 바꾸지 않기 때문에 계산 함수이다.

```jsx
function first_element(array){
	return array[0]
}
```

- `first_element()` 함수는 배열을 바꾸지 않는 읽기 함수이기 때문에 카피-온-라이트를 적용할 필요가 없다.
- `.shift()` 메서드의 쓰기 동작은 새로 만들 필요가 없다. `.shift()` 메서드가 하는 일을 그대로 감싸기만 하면 된다. 그리고 `.shift()` 메서드의 리턴값은 사용하지 않는다는 것을 강조하기 위해 리턴값을 무시하도록 처리하자.

```jsx
function drop_first(array){
	array.shift();
}
```

### 🌻 10-2. 쓰기 동작을 카피-온-라이트로 바꾸기

- `drop_first()` 함수는 인자로 들어온 값을 변경하는 쓰기이다. 이 함수를 카피-온-라이트로 만들자.

```jsx
function drop_first(array){
	const array_copy = array.slice();
	array_copy.shift();
	return array_copy;
} 
```

- 읽기와 쓰기를 분리하는 접근 방법은 분리된 함수를 따로 쓸 수 있기 때문에 더 좋은 접근 방법이다. 물로 ㄴ함께 쓸 수도 있다.

## 🍄 11. 값을 두 개 리턴하는 함수로 만들기

### 🌻 11-1. 동작을 감싸기

- 첫 번째 단계는 `.shift()` 메서드를 바꿀 수 있도록 새로운 함수로 감싸는 것이다. 여기서 함수 리턴값을 무시하면 안된다.

```jsx
function shift(array){
	return array.shift();
}
```

### 🌻 11-2. 읽으면서 쓰기도 하는 함수를 읽기 함수로 바꾸기

- 인자를 복사한 후 복사한 값의 첫 번째 항목을 지우고, 지운 첫 번째 항목과 변경된 배열을 함께 리턴하도록 바꾼다.

```jsx
function shift(array){
	const array_copy = array.slice();
	const first = array_copy.shift();
	return {
		first,
		array: array_copy
	};
}
```

### 🌻 11-3. 다른 방법

- 다른 방법으로 첫 번째 접근 방식을 사용해 두 값을 객체로 조합하는 방법이다.

```jsx
function shift(array){
	return {
		first: first_element(array);
		array: drop_first(array);
	}
}
```

## 🍄 12. 연습문제2

```jsx
// pop() 메서드를 카피-온-라이트 버전으로 바꾸기
// 1. 읽기 함수와 쓰기 함수로 분리하기
function last_element(array){
	return array[array.length - 1];
}
function drop_last(array){
	const array_copy = array.slice();
	array_copy.pop();
	return array_copy;
****}
// 2. 값 두개를 리턴하는 함수로 만들기
function pop(array){
	const array_copy = array.slice();
	const last = array_copy.pop();
	return {
		last,
		array: array_copy
	}
}
```

## 🍄 13. 연습문제3

```jsx
// push() 메서드를 카피-온-라이트 버전으로 바꾸기
function push(array, elem){
	const array_copy = array.slice();
	array_copy.push(elem);
	return array_copy;
}
```

## 🍄 14. 연습문제4

```jsx
// 앞에서 만든 카피-온-라이트 버전의 push() 함수를 사용해 add_contact() 함수를 리팩터링 하시오.
// 원래 코드
function add_contact(mailing_list, email){
	var list_copy = mailing_list.slice();
	list_copy.push(email);
	return list_copy;
}

// 수정 코드
function add_contact(mailing_list, email){
	return push(mailing_list, email);
}
```

## 🍄 15. 연습문제5

```jsx
 // 배열 항목을 카피-온-라이트 방식으로 설정하는 arraySet() 함수를 만들어보시오.
// 이 동작을 카피-온-라이트 버전으로 만드시오.
a[15] = 2; 

// 추가코드
function arraySet(array, idx, value){
	const array_copy = array.slice();
	array_copy[idx] = value;
	return array_copy;
}
```

## 🍄 16. 불변 데이터 구조를 읽는 것은 계산이다.

- 변경 가능한 데이터를 읽는 것은 액션이다.
    - 변경 가능한 값을 읽을 때마다 다른 값을 읽을 수도 있다. 따라서 변경 가능한 데이터를 읽는 것은 액션이다.
- 쓰기는 데이터를 변경 가능한 구조로 만든다.
    - 쓰기는 데이터를 바꾸기 때문에 데이터를 변경 가능한 구조로 만든다.
- 어떤 데이터에 쓰기가 없다면 데이터는 변경 불가능한 데이터이다.
    - 쓰기를 모두 없앴다면 데이터는 생성 이후 바뀌지 않다. 따라서 불변 데이터이다.
- 불변 데이터 구조를 읽는 것은 계산이다.
    - 어떤 데이터를 불변형으로 만들었다면 그 데이터에 모든 읽기는 계산이다.
- 쓰기를 읽기로 바꾸면 코드에 계산이 많아진다.
    - 데이터 구조를 불변형으로 만들수록 코드에 더 많은 계산이 생기고 액션이 줄어든다.

## 🍄 17. 애플리케이션에는 시간에 따라 변하는 상태가 있다.

- 데이터가 모두 불변형이면 애플리케이션에서 시간에 따라 변하는 상태를 어떻게 다뤄야 할까? 바꿀 수 있는 것이 없다면 사용자는 장바구니에 제품을 어떻게 넣을까?
- 모든 값을 불변형으로 만들더라도 시간에 따라 바뀌는 값을 다룰 수 있어야 한다. 예제 코드에서는 `shopping_cart` 전역 변수가 그런 값이다. 장바구니가 바뀔때 `shopping_cart` 전역변수에 새로운 값을 할당한다. 그래서 `shopping_cart` 는 항상 최신 장바구니를 나타낸다. 사실 자바구니 값은 새 값으로 교체된다고 할 수 있다.
- 교체를 하는 방법은 함수형 프로그래밍에서 일밙적으로 사용하는 방법이다. 교체를 사용하면 되돌리기를 쉽게 구현할 수 있다.

## 🍄 18. 불변 데이터 구조는 충분히 빠르다.

- 일반적으로 불변 데이터 구조는 변경 가능한 데이터 구조보다 메모리를 더 많이 쓰고 느리다.
- 하지만 불변 데이터 구조를 사용하면서 대용량의 고성능 시스템을 구현하는 사례도 있다. 이런 사례는 불변 데이터도 일반 애플리케이션에 쓰기 충분히 빠르다는 증거이다.
- 그렇지만 몇 가지 논점은 있다.

### 🌻 18-1. 언제든 최적화 할 수 있다.

- 애플리케이션을 개발할 때 예상하기 힘든 병목 지점이 항상 있다. 그래서 성능 개선을 할 때는 보통 미리 최적화하지 말라고 한다.
- 불변 데이터 구조를 사용하고 속도가 느린 부분이 있다면 그때 최적화하자.

### 🌻 18-2. 가비지 콜렉터는 매우 빠르다.

- 대부분의 언어는 가비지 콜렉터 성능 개선을 위해 꾸준히 연구했다.
- 어떤 가비지 콜렉터는 한  두개의 시스템 명령어로 메모리를 비울 수 있을 만큼 최적화되어있다.

### 🌻 18-3. 생각보다 많이 복사하지 않는다.

- 제품 100개인 배열을 복사해도 참조만 복사된다.
- 데이터 구조의 최상위 단계만 복사하는 것을 **얕은 복사**라고 한다. 얕은 복사는 같은 메모리를 가리키는 참조에 대한 복사본을 만든다. 이것을 **구조적 공유**라 한다.

### 🌻 18-4. 함수형 프로그래밍 언어에는 빠른 구현체가 있다.

- 우리는 직접 불변 데이터 구조를 만들었다. 하지만 어떤 함수형 프로그래밍 언어는 언어에서 불변 데이터 구조를 지원하며 직접 만든 것보다 더 효율적이다. 예를 들어 클로저에서 지원하는 불변 데이터 구조는 다른 언어에서 참고할 만큼 효율적이다.
- 어떻게 효율적일까? 데이터 구조를 복사를 할 때 최대한 많은 구조를 공유한다. 그래서 더 적은 메모리를 사용하고 결국 가비지 콜렉터의 부담을 줄여준다. 구현은 카피-온-라이트를 기반으로 하고 있다.

## 🍄 19. 객체에 대한 카피-온-라이트

- 객체도 배열과 같은 단계로 카피-온-라이트를 적용할 수 있다.
    1. 복사본 만들기
    2. 복사본 수정하기
    3. 복사본 리턴하기
- 배열은 `.slice()` 메서드로 복사본을 만들 수 있었다. 하지만 자바스크립트 객체에는 `.slice()` 메서드가 없다. 자바스크립트에서 객체에 있는 모든 키와 값을 복사하려면 어떻게 해야할까?
- 자바스크립트 객체에서는 `Object.assign()` 메서드를 이용해 복사를 구현한다.

```jsx
var object = { a: 1, b: 2};
var object_copy = Object.assign({}, object);
```

- ES6(spread)

```jsx
const object = { a: 1, b: 2 };
const object_copy = { ...object };
```

- 코드를 수정해보자.

```jsx
// 원래 코드
function setPrice(item, new_price){
	item.price = new_price;
}

// 수정 코드
function setPrice(item, new_price){
	var item_copy = Object.assign({}, item);
	item_copy.price = new_price;
	return item_copy;
}
```

## 🍄 20. 연습문제 6

```jsx
// 카피-온-라이트 방식으로 객체에 값을 설정하는 objectSet() 함수 만들기
// 이 동작을 카피-온-라이트 버전으로 만드시오.
o["price"] = 37;

// 코드
function objectSet(obj, key, value){
	const copy_obj = {...obj};
	copy_obj[key] = value;
	return copy_obj;
}
```

## 🍄 21. 연습문제 7

```jsx
// 방금 만든 objectSet() 함수를 사용해 setPrice()를 리팩터링 하라.
// 원래 코드
function setPrice(item, new_price){
	var item_copy = Object.assign({}, item);
	item_copy.price = new_price;
	return item_copy;
}

// 수정 코드
function setPrice(item, new_price){
	return objectSet(item, "price", new_price);
}
```

## 🍄 22. 연습문제 8

```jsx
// objectSet() 함수를 이용해 제품 개수를 설정하는 setQuantity()함수를 만드시오.
function setQuantity(item, new_quantity) {
	return objectSet(item, 'quantity', new_quantity);
}
```

## 🍄 23. 연습문제 9

```jsx
// 객체의 키로 키/값 쌍을 지우는 delete 연산을 카피-온-라이트 버전으로 만드시오.
// 이 동작
var a = { x: 1 };
delete a["x"];
console.log(a); // {}

// 코드
function objectDelete(obj, key){
	const copy_obj = {...obj};
	delete copy_obj[key];
	return copy_obj;
}
```

## 🍄 24. 중첩된 쓰기를 읽기로 바꾸기

- 장바구니 동작 중 쓰기를 읽기로 바꿔야 할 것이 하나 더 있다. 제품 이름으로 해당 제품의 가격을 바꾸는 쓰기 동작이다. 이 동작은 중첩된 데이터 구조를 바꿔야 하는 조금 특별한 동작이다. 장바구니 배열 안에 중첩된 항목을 바꿔야 한다.
- 가장 안쪽에 있는 쓰기 동작부터 바꾸는 것이 쉽다. 위에서 `setPrice()` 함수를 구현했기 때문에 `setPriceByName()` 에서 제품에 값을 설정하는 코드에 카피-온-라이트 버전의 `setPrice()` 함수를 사용할 수 있다.

```jsx
// 원래 코드
function setPriceByName(cart, name, price){
	for(var i = 0; i < cart.length; i++){
		if(cart[i].name === name){
			cart[i].price = price;
		}
	}
}

// 수정 코드
function setPriceByName(cart, name, price){
	var cartCopy = cart.slice();
	for(var i = 0; i < cartCopy.length; i++){
		if(cartCopy[i].name === name){
			cartCopy[i] = setPrice(cartCopy[i], price);
		}
	}
	return cartCopy;
}
```

- 중첩된 쓰기도 중첩되지 않은 쓰기와 같은 패턴을 사용한다. 복사본을 만들고 변경한 다음 복사본을 리턴한다. 중첩된 항목에 또 다른 카피-온-라이트를 사용하는 부분만 다르다.
- 항목을 직접 변경하면 불변 데이터가 아니다. 배열에 항목은 바뀌지 않지만 배열 항목이 참조하는 값은 바뀐다. 이렇게 되면 불변 데이터가 아니다. 중첩된 모든 데이터 구조가 바뀌지 않아야 불변 데이터라고 할 수 있다.
- 최하위부터 최상위까지 중첩된 데이터 구조의 모든 부분이 불변형이어야 한다. 중첩된 데이터의 일부를 바꾸려면 변경하려는 값과 상위의 모든 값을 복사해야 한다.

## 🍄 25. 연습문제 10

```jsx
// 다음 중첩된 동작을 카피-온-라이트 버전으로 만들어라.
// 기존 코드
function setQuantityByName(cart, name, quantity){
	for(var i = 0; i < cart.length; i++){
		if(cart[i].name === name){
			cart[i].quantity = quantity;
		}
	}
}

// 수정 코드
function setQuantityByName(cart, name, quantity){
	var cartCopy = cart.slice();
	for(var i = 0; i < cartCopy.length; i++){
		if(cartCopy[i].name === name){
			cartCopy[i] = objectSet(cartCopy[i], 'quantity', quantity);
		}
	}
	return cartCopy;
}

```

## 🍄 26. 정리

- 함수형 프로그래밍에서 불변 데이터가 필요하다. 계산에서는 변경 가능한 데이텅데 쓰기를 할 수 없다.
- 카피-온-라이트는 데이터를 불변형으로 유지할 수 있는 원칙이다. 복사본을 만들고 원본 대신 복사본을 변경하는 것을 말한다.
- 카피-온-라이트는 값을 변경하기 전에 얕은 복사를 한다. 그리고 리턴한다. 이렇게 하면 통제할 수 있는 범위에서 불변성을 구현할 수 있다.
- 보일러 플레이트 코드를 줄이기 위해 기본적인 배열과 객체 동작에 대한 카피-온-라이트 버전을 만들어 두는 것이 좋다.