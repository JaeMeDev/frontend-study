# 🌸 Chapter 5: 더 좋은 액션 만들기

## 🍄 1. 비즈니스 요구 사항과 설계를 맞추기

- 액션에서 계산으로 리팩터링하는 과정은 단순하고 기계적이었다. 기계적인 리팩터링이 항상 최선의 구조를 만들어주는 것은 아니다. 좋은 구조를 위해서는 어느 정도 사람의 손길이 필요하다.
- `gets_free_shipping()` 함수는 비즈니스 요구사항으로 봤을 때 맞지 않는 부분이 있다. 요구 사항은 장바구니에 담긴 제품을 주문할 때 무료 배송인지 확인하는 것이다. 하지만 함수는 장바구니로 무료 배송을 확인하지 않고 제품의 합계와 가격으로 확인하고 있다. 이는 비즈니스 요구사항에 맞지 않는 인자이다.

```jsx
function gets_free_shipping(total, item_price){ // 이 인자는 요구사항에 맞지 않는다.
	return item_price + total >= 20;
}
```

- 또 찾아보면 중복된 코드도 존재한다. 합계에 제품 가격을 더하는 코드가 두 군데 있다. 중복이 항상 나쁜 것은 아니지만 코드에서 나는 냄새이다. 코드의 냄새는 나중에 문제가 될 수 있다.

```jsx
function gets_free_shipping(total, item_price){
	return item_price + total >= 20; // item + total이 중복되어 있다.
}

function calc_total(cart){
	var total = 0;
	for(var i = 0; i < cart.length; i++){
		var item = cart[i];
		total += item.price; // item + total이 중복되어 있다.
	}
	return total; 
}
```

- 중복된 함수를 `gets_free_shipping(cart)` 로 장바구니로 인자를 바꿔주고 `calc_total()` 함수를 재사용해 중복을 없애자.

## 🍄 2. 비즈니스 요구 사항과 함수를 맞추기

- 함수의 동작을 바꾸는 것은 엄밀히 말하면 리팩터링이라고 할 수 없다.
- `gets_free_shipping()` 함수는 장바구니 값을 인자로 받아 합계가 20보다 크거나 같은지 알려준다.

```jsx
// 원래코드
function gets_free_shipping(total, item_price){
	return item_price + total >= 20;
}

// 바뀐 코드
function gets_free_shipping(cart){
	return calc_total(cart) >= 20;
}
```

- 바꾼 함수는 합계와 제품 가격 대신 장바구니 데이터를 사용한다. 장바구니는 전자상거래에서 많이 사용하는 엔티티타입이기 때문에 비즈니스 요구 사항과 잘 맞는다.
- 함수 시그니처가 바뀌면 사용하는 부분도 수정해야 한다.

```jsx
// 코드 수정
function update_shipping_icons() {
	var buy_buttons = get_buy_buttons_dom();
	for(var i = 0; i < buy_buttons.length; i++) {
		var button = buy_buttons[i];
		var item = button.item;
		var new_cart = add_item(shopping_cart, item.name, item.price);
		if(gets_free_shipping(new_cart)){
			button.show_free_shipping_icon();
		}else {
			button.hide_free_shipping_icon();
		}
	}
}
```

## 🍄 3. 원칙: 암묵적 입력과 출력은 적을수록 좋다.

- 인자가 아닌 모든 입력은 암묵적 입력이고 리턴값이 아닌 모든 출력은 암묵적 출력이다.
- 암묵적 입력과 암묵적 출력이 없는 함수를 **계산**이라 한다.
- 계산을 만들기 위해 암묵적 입력과 출력을 없애는 원칙은 액션에도 적용할 수 있다. 액션에서 모든 암묵적 입력과 출력을 없애지 않더라도 암묵적 입력과 출력을 줄이면 좋다.
- 어떤 함수에 암묵적 입력과 출력이 있다면 다른 컴포넌트와 강하게 연결된 컴포넌트라고 할 수 있다. 다른 곳에서 사용할 수 없기 때문에 모듈이 아니다.
- 암묵적 입력과 출력을 명시적으로 바꾼다면 모듈화된 컴포넌트를 만들 수 있다.
- 암묵적 입력이 있는 함수는 조심해서 사용해야 한다. 만약 다른 곳에서 이 값이 쓰이면 문제가 생길 것이다.
- 암묵적 출력 역시 조심해서 사용해야 한다. 다른 곳에 영향을 줄 수 있다.
- 암묵적 입력과 출력이 있는 함수는 아무 때나 실행할 수 없기 때문에 테스트하기 어렵다. 모든 입력값을 설정하고 테스트를 돌린 후에 모든 출력값을 확인해야 한다. 입력과 출력이 많을수록 테스트는 더 어려워진다.
- 계산은 암묵적 입력과 출력이 없기 때문에 테스트하기 쉽다. 모든 암묵적 입력과 출력을 없애지 못해 액션을 계산으로 바꾸지 못해도 암묵적 입력과 암묵적 출력을 줄이면 테스트하기 쉽고 재사용하기 좋다.

## 🍄 4. 암묵적 입력과 출력 줄이기

- `update_shipping_icons()` 함수에 암묵적 입력과 출력을 줄여보자.

```jsx
// 원래 코드
function update_shipping_icons() {
	var buy_buttons = get_buy_buttons_dom();
	for(var i = 0; i < buy_buttons.length; i++) {
		var button = buy_buttons[i];
		var item = button.item;
		var new_cart = add_item(shopping_cart, item.name, item.price);
		if(gets_free_shipping(new_cart)){
			button.show_free_shipping_icon();
		}else {
			button.hide_free_shipping_icon();
		}
	}
}

// 수정된 코드
function update_shipping_icons(cart) {
	var buy_buttons = get_buy_buttons_dom();
	for(var i = 0; i < buy_buttons.length; i++) {
		var button = buy_buttons[i];
		var item = button.item;
		var new_cart = add_item(shopping_cart, item.name, item.price);
		if(gets_free_shipping(cart)){
			button.show_free_shipping_icon();
		}else {
			button.hide_free_shipping_icon();
		}
	}
}
```

- 함수를 호출하는 곳도 수정하자.

```jsx
// 원래 코드
function calc_cart_total(){
	shopping_cart_total = calc_total(shopping_cart);
	set_cart_total_dom();
	update_shipping_icons(); 
	update_tax_dom(); 
}

// 수정된 코드
function calc_cart_total(){
	shopping_cart_total = calc_total(shopping_cart);
	set_cart_total_dom();
	update_shipping_icons(shopping_cart); 
	update_tax_dom(); 
}

```

## 🍄 5. 연습문제

- 액션을 전역변수를 읽는 부분을 인자로 바꿔보자.

```jsx
// 예전 코드
function add_item_to_cart(name, price){
	shopping_cart = add_item(shopping_cart, name, price);
	calc_cart_total();
}

function calc_cart_total(){
	shopping_cart_total = calc_total(shopping_cart);
	set_cart_total_dom();
	update_shipping_icons(shopping_cart); 
	update_tax_dom(); 
}

// 새로운 코드의 추가
function set_cart_total_dom(){
	// shopping_cart_total
}

function update_shipping_icons(cart) {
	var buy_buttons = get_buy_buttons_dom();
	for(var i = 0; i < buy_buttons.length; i++) {
		var button = buy_buttons[i];
		var item = button.item;
		var new_cart = add_item(shopping_cart, item.name, item.price);
		if(gets_free_shipping(cart)){
			button.show_free_shipping_icon();
		}else {
			button.hide_free_shipping_icon();
		}
	}
}

function update_tax_dom(){
	set_tax_dom(update_tax(shopping_cart_total));
}
```

```jsx
// 전역변수 읽는 부분을 없앤 코드
function add_item_to_cart(name, price){
	shopping_cart = add_item(shopping_cart, name, price);
	calc_cart_total(shopping_cart);
}

function calc_cart_total(cart){
	var total = calc_total(cart);
	set_cart_total_dom(total);
	update_shipping_icons(shopping_cart); 
	update_tax_dom(total); 
}

function set_cart_total_dom(total){
	// total
}

function update_shipping_icons(cart) {
	var buy_buttons = get_buy_buttons_dom();
	for(var i = 0; i < buy_buttons.length; i++) {
		var button = buy_buttons[i];
		var item = button.item;
		var new_cart = add_item(shopping_cart, item.name, item.price);
		if(gets_free_shipping(cart)){
			button.show_free_shipping_icon();
		}else {
			button.hide_free_shipping_icon();
		}
	}
}

function update_tax_dom(total){
	set_tax_dom(update_tax(total));
}
```

## 🍄 6. 코드 다시 살펴보기

```jsx
function add_item_to_cart(name, price){ // 이 함수는 '구매하기'버튼을 누를 때 사용
	shopping_cart = add_item(shopping_cart, name, price);
	calc_cart_total(shopping_cart);
}

function calc_cart_total(cart){ // 이 함수는 조금 과한 것 같다. add_item_to_cart()에 있어도 될 것 같다.
	var total = calc_total(cart);
	set_cart_total_dom(total);
	update_shipping_icons(shopping_cart); 
	update_tax_dom(total); 
}

function set_cart_total_dom(total){
	// total
}

function update_shipping_icons(cart) {
	var buy_buttons = get_buy_buttons_dom();
	for(var i = 0; i < buy_buttons.length; i++) {
		var button = buy_buttons[i];
		var item = button.item;
		var new_cart = add_item(shopping_cart, item.name, item.price);
		if(gets_free_shipping(cart)){
			button.show_free_shipping_icon();
		}else {
			button.hide_free_shipping_icon();
		}
	}
}

function update_tax_dom(total){
	set_tax_dom(update_tax(total));
}
```

```jsx
// 수정된 코드
function add_item_to_cart(name, price){ // 이 함수는 '구매하기'버튼을 누를 때 사용
	shopping_cart = add_item(shopping_cart, name, price);
	var total = calc_total(shopping_cart);
	set_cart_total_dom(total);
	update_shipping_icons(shopping_cart); 
	update_tax_dom(total); 
}

function set_cart_total_dom(total){
	// total
}

function update_shipping_icons(cart) {
	var buy_buttons = get_buy_buttons_dom();
	for(var i = 0; i < buy_buttons.length; i++) {
		var button = buy_buttons[i];
		var item = button.item;
		var new_cart = add_item(shopping_cart, item.name, item.price);
		if(gets_free_shipping(cart)){
			button.show_free_shipping_icon();
		}else {
			button.hide_free_shipping_icon();
		}
	}
}

function update_tax_dom(total){
	set_tax_dom(update_tax(total));
}
```

## 🍄 7. 계산 분류하기

- 의미 있는 계층에 대해 알아보기 위해 계산을 분류하자.
- 계산 함수를 들여다보자. 어떤 함수가 장바구니 구조를 알아야 하면 (C), 제품에 대한 구조를 알아야 하면 (I)라고 표현하고, 비즈니스 규칙에 대한 함수라면 (B)로 표시해보자.

```jsx
function add_item(cart, name, price){ // (C) (I)
	var new_cart = cart.slice();
	new_cart.push({
		name,
		price
	});
	return new_cart;
}

function calc_total(cart){ // (C) (I) (B)
	var total = 0;
	for(var i = 0; i < cart.length; i++){
		var item = cart[i];
		total += item.price;
	}
	return total; 
}

function gets_free_shipping(cart){ // (B)
	return calc_total(cart) >= 20;
}

function calc_tax(amount){ // (B)
	return amount * 0.10;
}
```

- 시간이 지날수록 나눈 그룹은 더 명확해질 것이다.

## 🍄 8. 원칙: 설계는 엉켜있는 코드를 푸는 것이다

- 함수를 사용하면 관심사를 자연스럽게 분리할 수 있다. 함수는 인자로 넘기는 값과 그 값을 사용하는 방법을 분리한다.

### 🌻 8-1. 재사용하기 쉽다.

- 함수는 작으면 작을수록 재사용하기 쉽다. 하는 일도 적고 쓸 때 가정을 많이 하지 않아도 된다.

### 🌻 8-2. 유지보수하기 쉽다.

- 작은 함수는 쉽게 이해할 수 있고 유지보수하기 쉽다. 코드가 작기 때문에 올바른지 아닌지 명확하게 알 수 있다.

### 🌻 8-3. 테스트하기 쉽다.

- 작은 함수는 테스트하기 좋다. 한 가지 일만 하기 때문에 한 가지만 테스트하면 된다.
- 함수에 특별한 문제가 없어도 꺼낼 것이 있다면 분리하는 것이 좋다.

## 🍄 9. add_item()을 분리해 더 좋은 설계 만들기

- `add_item()` 은 잘 동작한다. 장바구니에 제품을 추가하는 간단한 일만 하는 것 같다. 하지만 자세히 살펴보면 `add_item()` 은 네 부분으로 나눌 수 있다.

```jsx
function add_item(cart, name, price){
	var new_cart = cart.slice(); // 1. 배열 복사
	new_cart.push({ // 2. item 객체 만들기, 3. 복사본에 item 추가하기
		name,
		price
	});
	return new_cart; // 4. 복사본 리턴
}
```

- 분리한 코드를 별도의 함수로 만들어보자.

```jsx
// 2. item 객체 만들기
function make_cart_item(name, price){
	return { name, price };
}

function add_item(cart, item){
	var new_cart = cart.slice(); // 1. 배열 복사
	new_cart.push(item); // 3. 복사본에 item 추가
	return new_cart; // 4. 복사본 리턴
}

// 호출 코드 수정
add_item(shopping_cart, make_cart_item("shoes", 3.45));
```

- item 구조만 알고 있는 `make_cart_item()` 함수와 cart 구조만 알고 있는 `add_item()` 함수로 나눠 원래 코드를 수정하였다. 이렇게 분리하면 cart와 item을 독립적으로 확장할 수 있다. 예를 들어 배열인 cart를 해시 맵과 같은 자료구졸 바꾼다고 했을 때 변경해야 할 부분이 적다.

## 🍄 10. 카피-온-라이트 패턴을 빼내기

- `add_item()` 함수는 크기가 작고 괜찮은 함수이다. 자세히 보면 카피-온-라이트를 사용해 배열에 항목을 추가하는 함수이다.
- 이 함수는 일반적인 배열과 항목에 쓸 수 있지만 이름이 일반적이지 않다. 이름만 보면 장바구니를 넘겨야 쓸 수 있을 것 같다.
- 함수 이름과 인자 이름을 더 일박적인 이름으로 수정하자.

```jsx
function add_element_last(array, elem){
	var new_array = array.slice();
	new_array.push(elem);
	return new_array;
}
```

- 원래의 `add_item()` 함수는 간단하게 다시 만들 수 있다.

```jsx
function add_item(cart, item){
	return add_element_last(cart, item);
}
```

- `add_element_last()` 함수는 장바구니와 제품에서만 쓸 수 있는 함수가 아닌 어떤 배열이나 항목에도 쓸 수 있는 이름으로 바꿨다. 이 함수는 재사용할 수 있는 **유틸리티 함수**이다.

## 🍄 11. add_item() 사용하기

- `add_item()` 은 cart, name, price인자가 필요한 함수였다. 하지만 이제는 cart와 item 인자만 필요하다.
- 그렇기 때문에 `add_item()` 을 호출하는 부분을 수정해줘야 한다.

```jsx
function add_item_to_cart(name, price){
	var item = make_cart_item(name, price);
	shopping_cart = add_item(shopping_cart, item);
	var total = calc_total(shopping_cart);
	set_cart_total_dom(total);
	update_shipping_icons(shopping_cart); 
	update_tax_dom(total); 
}
```

## 🍄 12. 연습문제

```jsx
// 예전코드
function update_shipping_icons(cart) {
	var buy_buttons = get_buy_buttons_dom();
	for(var i = 0; i < buy_buttons.length; i++) {
		var button = buy_buttons[i];
		var item = button.item;
		var new_cart = add_item(cart, item.name, item.price);
		if(gets_free_shipping(new_cart)){
			button.show_free_shipping_icon();
		}else {
			button.hide_free_shipping_icon();
		}
	}
}
```

```jsx
// 수정된 코드
function update_shipping_icons(cart) {
	var buy_buttons = get_buy_buttons_dom();
	for(var i = 0; i < buy_buttons.length; i++) {
		var button = buy_buttons[i];
		var item = button.item;
		var has_free_shipping = gets_free_shipping_with_item(cart, item)
		set_free_shipping_icon(button, has_free_shipping);
	}
}

function gets_free_shipping_with_item(cart, item){
	var new_cart = add_item(cart, item.name, item.price);
	return gets_free_shipping(new_cart);
}

function set_free_shipping_icon(button, isShown){
	if(isShown){
		button.show_free_shipping_icon();
	}else {
		button.hide_free_shipping_icon();
	}
}
```

## 🍄 13. 정리

- 일반적으로 암묵적 입력과 출력은 인자와 리턴값으로 바꿔 없내는 것이 좋다.
- 설계는 엉켜있는 것을 푸는 것이다. 풀려있는 것은 언제든 다시 합칠 수 있다.
- 엉켜있는 것을 풀어 각 함수가 하나의 일만 하도록 하면, 개념을 중심으로 쉽게 구성할 수 있다.