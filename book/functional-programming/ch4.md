# 🌸 Chapter 4: 액션에서 계산 빼내기

## 🍄 1. MegaMart.com에 오신 것을 환영합니다.

- 예제 코드에 대한 설명
- MegaMart는 온라인 쇼핑몰이다. 경쟁력을 유지하고 있는 중요한 기능 중 하나는 쇼핑 중에 장바구니에 다겨 있는 제품의 금액 합계를 볼 수 있는 기능이다.

```jsx
var shpping_cart = []; // 장바구니 제품
var shopping_cart_total = 0; // 금액 합계

function add_item_to_cart(name, price){
	shopping_cart.push({
		name,
		price
	});
	calc_cart_total();
}

function calc_cart_total() {
	shopping_cart_total = 0;
	for(var i = 0; i < shopping_cart.length; i++){
		var item = shoppinig_cart[i];
		shopping_cart_total += item.price;
	}
	// DOM 업데이트 코드
	set_cart_total_dom();
}
```

## 🍄 2. 무료 배송비 계산하기

- 새로운 요구사항으로 MegaMart는 구매 합계가 20 달러 이상이면 무료 배송을 해주려고 한다. 그래서 장바구니에 넣으면 합계가 20달러가 넘는 제품의 구매 버튼 옆에 무료 배송 아이콘을 표시해 주려 한다.
- 구매 버튼에 무료 배송 아이콘을 표시하기 위한 함수를 만들자. 일단 절차적으로.

```jsx
function update_shipping_icons() {
	var buy_buttons = get_buy_buttons_dom(); // 페이지에 있는 모든 구매 버튼을 가져와 반복문 적용
	for(var i = 0; i < buy_buttons.length; i++) {
		var button = buy_buttons[i];
		var item = button.item;
		if(item.price + shopping_cart_total >= 20){
			button.show_free_shipping_icon();
		}else {
			button.hide_free_shipping_icon();
		}
	}
}
```

- 합계 금액이 바뀔 때마다 모든 아이콘을 업데이트하기 위해 `calc_cart_total()` 함수 마지막에 `update_shipping_icons()` 함수를 불러준다.

```jsx
function calc_cart_total(){
	shopping_cart_total = 0;
	for(var i = 0; i < shopping_cart.length; i++){
		var item = shopping_cart[i];
		shopping_cart_total += item.price;
	}
	set_cart_total_dom();
	update_shipping_icons(); // 아이콘 업데이트 함수
}
```

## 🍄 3. 세금 계산하기

- 장바구니의 금액 합계가 바뀔 때마다 세금을 다시 계산해야 한다. 함수형 프로그래밍을 적용하지 않고 이 기능을 추가하는 것은 어렵지 않다.

```jsx
function update_tax_dom(){
	set_tax_dom(shopping_cart_total * 0.10);
}
```

- `calc_cart_total()` 에서 새로 만든 함수를 불러준다.

```jsx
function calc_cart_total(){
	shopping_cart_total = 0;
	for(var i = 0; i < shopping_cart.length; i++){
		var item = shopping_cart[i];
		shopping_cart_total += item.price;
	}
	set_cart_total_dom();
	update_shipping_icons(); // 아이콘 업데이트 함수
	update_tax_dom(); // 세금 업데이트 함수
}
```

## 🍄 4. 테스트하기 쉽게 만들기

- 지금 코드는 비즈니스 규칙을 테스트하기 어렵다. 현재 코드가 바뀔 때마다 개발자는 이와 같은 테스트를 만들어야 한다.
    1. 브라우저 설정하기
    2. 페이지 로드하기
    3. 장바구니에 제품 담기 버튼 클릭
    4. DOM이 업데이트될 때까지 기다리기
    5. DOM에서 값 가져오기
    6. 가져온 문자열 값을 숫자로 바꾸기
    7. 예상하는 값과 비교하기
- 테스트를 개선하기 위해서는 다음과 같은 조건이 필요하다.
    - DOM 업데이트와 비즈니스 규칙은 분리되어야 한다.
    - 전역변수가 없어야 한다.

## 🍄 5. 재사용하기 쉽게 만들기

### 🌻 5-1. 결제팀과 배송팀이 코드를 사용하려고 한다.

- 결제팀과 배송팀이 위에서 작성한 코드를 재사용하려 했지만, 다음과 같은 이유로 재사용할 수 없다.
    - 장바구니 정보를 전역변수에서 읽어오고 있지만, 결제팀과 배송팀은 데이터베이스에서 장바구니 정보를 읽어와야 한다.
    - 결과를 보여주기 위해 DOM을 직접 바꾸고 있지만, 결제팀은 영수증을, 배송팀은 운송장을 출력해야 한다.

### 🌻 5-2. 코드에 대한 설명

```jsx
function update_shipping_icons() {
	var buy_buttons = get_buy_buttons_dom(); 
	for(var i = 0; i < buy_buttons.length; i++) {
		var button = buy_buttons[i];
		var item = button.item;
		if(item.price + shopping_cart_total >= 20){ // 결제팀, 배송팀은 이 비즈니스 규칙을 사용하려 한다.(>=20)
			button.show_free_shipping_icon(); // 이 함수는 전역변수인 shopping_cart_total이 있어야 한다.
		}else {
			button.hide_free_shipping_icon(); // 이 코드는 DOM이 있어야 한다.
		}
	}
	// return 값이 없기 때문에 결과를 받을 방법이 없다.
}
```

### 🌻 5-3. 재사용의 조건

- 전역변수에 의존하지 않아야 한다.
- DOM을 사용할 수 있는 곳에서 실행된다고 가정하면 안된다.
- 함수가 결괏값을 리턴해야 한다.

## 🍄 6. 액션과 계싼, 데이터를 구분하기

- 가장 먼저 해야할 일은 각 함수가 액션과 계산, 데이터 중 어떤 것인지 구분하는 일이다. 그래야 어떻게 코드를 개선할 수 있는지 알 수 있다.
- 각 함수에 주석으로 액션은 (A), 계산은 (C), 데이터는 (D)라고 표시를 해보자.

```jsx
var shpping_cart = []; // (A)
var shopping_cart_total = 0; // (A)

function add_item_to_cart(name, price){ // (A)
	shopping_cart.push({
		name,
		price
	});
	calc_cart_total();
}

function update_shipping_icons() { // (A)
	var buy_buttons = get_buy_buttons_dom(); 
	for(var i = 0; i < buy_buttons.length; i++) {
		var button = buy_buttons[i];
		var item = button.item;
		if(item.price + shopping_cart_total >= 20){ 
			button.show_free_shipping_icon(); 
		}else {
			button.hide_free_shipping_icon(); 
		}
	}
}

function update_tax_dom(){ // (A)
	set_tax_dom(shopping_cart_total * 0.10);
}

function calc_cart_total(){ // (A)
	shopping_cart_total = 0;
	for(var i = 0; i < shopping_cart.length; i++){
		var item = shopping_cart[i];
		shopping_cart_total += item.price;
	}
	set_cart_total_dom();
	update_shipping_icons(); 
	update_tax_dom(); 
}
```

- 이 코드에서는 계산, 데이턴는 없고 모든 코드가 액션이다.

## 🍄 7. 함수에는 입력과 출력이 있다.

- 모든 함수에는 입력과 출력이 있다. 입력은 함수가 계산을 하기 위한 외부 정보이다. 출력은 함수 밖으로 나오는 정보나 어떤 동작이다. 함수를 부르는 이유는 결과가 필요하기 때문이고, 원하는 결과를 얻으려면 입력이 필요하다.

```jsx
var total = 0;

// 인자는 입력이다.
function add_to_total(amount) {
	console.log("Old total : " + total); // 전역변수를 읽는 것은 입력이다. / 콘솔 출력은 출력이다.
	total += amount; // 전역변수를 바꾸는 것은 출력이다.
	return total; //  리턴값은 출력이다.
}
```

### 🌻 7-1. 입력과 출력은 명시적이거나 암묵적일 수 있다.

- 인자는 명시적인 입력이다. 리턴값은 명시적인 출력이다. 하지만 암묵적으로 함수로 들어가거나 나오는 정보도 있다.

```jsx
var total = 0;

// 인자는 명시적 입력이다.
function add_to_total(amount) {
	console.log("Old total : " + total); // 전역변수를 읽는 것은 암묵적 입력이다. / 콘솔 출력은 암묵적 출력이다.
	total += amount; // 전역변수를 바꾸는 것은 암묵적 출력이다.
	return total; //  리턴값은 명시적 출력이다.
}
```

### 🌻 7-2. 함수에 암묵적 입력과 출력이 있으면 액션이 된다.

- 함수에서 암묵적 입력과 출력을 없애면 계산이 된다. 암묵적 입력은 함수의 인자로 바꾸고, 암묵적 출력은 함수의 리턴값으로 바꾸면 된다.
- 함수형 프로그래머는 암묵적 입력과 출력을 **부수 효과**라 부른다.

## 🍄 8. 테스트와 재사용성은 입출력과 관련 있다.

### 🌻 8-1. DOM 업데이트와 비즈니스 규칙은 분리되어야 한다.

- DOM을 업데이트하는 일은 함수에서 어떤 정보가 나오는 것이기 때문에 출력이다.
- 리턴값이 아니기 때문에 암묵적 출력이다.
- 사용자가 정보를 볼 수 있어야 하기 때문에 DOM 업데이트는 어디선가 필요하다.
- 암묵적 출력인 DOM 업데이트와 비즈니스 규칙을 분리해야 한다.

### 🌻 8-2. 전역변수가 없어야 한다.

- 전역변수를 읽는 것은 암묵적 입력이고, 바꾸는 것은 암묵적 출력이다.
- 암묵적 입력은 인자로 바꾸고 출력은 리턴값으로 바꾸면 된다.

## 🍄 9. 액션에서 계산 빼내기

- 먼저 계산에 해당하는 코드를 분리하고 입력은 인자로 출력은 리턴값으로 바꾼다.

```jsx
// 원래 코드
function calc_cart_total(){
	shopping_cart_total = 0;
	for(var i = 0; i < shopping_cart.length; i++){
		var item = shopping_cart[i];
		shopping_cart_total += item.price;
	}
	set_cart_total_dom();
	update_shipping_icons(); 
	update_tax_dom(); 
}

// 바꾼코드
function calc_cart_total(){
	calc_total();
	set_cart_total_dom();
	update_shipping_icons(); 
	update_tax_dom(); 
}

function calc_total(){
	shopping_cart_total = 0;
	for(var i = 0; i < shopping_cart.length; i++){
		var item = shopping_cart[i];
		shopping_cart_total += item.price;
	}
}
```

- 빼낸 코드를 새로운 함수로 만들고 이름을 붙여주자. 그리고 원래 코드에서 빼낸 부분은 새로 만든 함수를 호출하도록 수정하자. 아직 새 함수는 액션이다. 이것을 계산으로 바꾸어야 한다.
- 위에서 한 리팩터링은 **서브루틴 추출하기**라고 할 수 있다. 기존 코드에서 동작은 바뀌지 않는다.
- 새로 만든 함수는 아직 액션이기 때문에 계산으로 바꿔야 하며, 계산으로 바꾸려면 먼저 어떤 입력과 출력이 있는지 확인해야 한다. 이 함수에는 `shopping_cart_toal` 전역변숫값을 바꾸는 것이 출력이고, `shopping_cart` 전역 변숫값을 읽어오는 것이 입력이다. 여기 있는 입력과 출력은 모두 암묵적이기 때문에 명시적인 입력과 출력으로 바꿔야 계산이 된다.
- 출력은 모두 같은 전역변숫값을 바꾼다. 그래서 같은 리턴값을 사용해 바꿀 수 있다. 전역변수 대신 지역변수를 사용하도록 바꾸고 지역변숫값을 리턴하도록 수정하자. 그리고 원래의 함수는 새 함수의 리턴값을 받아 전역변수에 할당하도록 수정하자.

```jsx
function calc_cart_total(){
	shopping_cart_total = calc_total(shopping_cart);
	set_cart_total_dom();
	update_shipping_icons(); 
	update_tax_dom(); 
}

// 암묵적 입력, 출력 없애기
function calc_total(cart){
	var total = 0;
	for(var i = 0; i < cart.length; i++){
		var item = cart[i];
		total += item.price;
	}
	return total;
}
```

- 이렇게 수정하면 `calc_total()` 함수는 계산이 된다. 모든 입력은 인자이고 모든 출력은 리턴값이다.

## 🍄 10. 액션에서 또 다른 계산 빼내기

- `add_item_to_cart()` 함수에서도 계산을 빼낼 수 있다. 위에서 처럼 빼낼 부분을 찾고 함수로 빼서 입력과 출력을 명시적으로 바꾼다.

```jsx
// 원래 코드
function add_item_to_cart(name, price){ 
	shopping_cart.push({
		name,
		price
	});
	calc_cart_total();
}

// 바꾼 코드
function add_item_to_cart(name, price){
	add_item(name, price);
	calc_cart_total();
}

function add_item(name, price){
	shopping_cart.push({
		name,
		price
	});
}
```

- 계산으로 빼낸 코드를 `add_item()` 이라는 새 함수로 만들었다. 새로 만든 함수는 아직 액션이다. 전역변수인 `shopping_cart` 배열을 바꾸기 때문이다.

```jsx
function add_item_to_cart(name, price){
	add_item(shopping_cart, name, price);
	calc_cart_total();
}

function add_item(cart, name, price){
	cart.push({
		name,
		price
	});
}
```

- 호출하는 쪽은 전역변수를 인자로 넘기도록 고친다. 암묵적인 입력을 인자로 바꿔 명시적인 입력이 되었다.
- 아직 암묵적 출력이 남아있다. `push()` 함수로 전역배열을 변경하는 부분이다. 이것도 명시적인 출력으로 바꿔야 한다.
- `shopping_cart` 에 있는 배열을 바꾸는 부분을 값을 바꾸는 대신 복사본을 만들고 박사본에 추가해 리턴해야 한다.

```jsx
function add_item_to_cart(name, price){
	shopping_cart = add_item(shopping_cart, name, price);
	calc_cart_total();
}

function add_item(cart, name, price){
	var new_cart = cart.slice();
	new_cart.push({
		name,
		price
	});
	return new_cart;
}
```

- 자바스크립트에서 배열을 직접 복사하는 방법이 없다. 그래서 `.slice()` 메서드를 사용했다.
- 어떤 값을 바꿀 때 그 값을 복사해서 바꾸는 방법은 불변성을 구현하는 방법 중 하나이다. 이 방법을 **카피-온-라이트**라 한다.

## 🍄 11. 계산 추출을 단계별로 알아보기

1. 계산을 찾아 빼낸다.
    - 빼낼 코드를 찾는다. 코드를 추출해 새로운 함수를 만들어 리팩터링 한다. 새 함수에 인자가 필요하다면 추가한다. 원래 코드에서 빼낸 부분에 새 함수를 부르도록 바꾼다.
2. 새 함수에 암묵적 입력과 출력을 찾는다.
    - 새 함수에 암묵적 입력과 출력을 찾는다. 암묵적 입력은 함수를 부루는 동안 결과에 영향을 줄 수 있는 것을 말한다. 암묵적 출력은 함수 호출의 결과로 영향을 받는 것을 말한다.
    - 함수 인자를 포함해 함수 밖에 있는 변수를 읽거나 데이터베이스에서 값을 가져오는 것은 입력이다.
    - 리턴값을 포함해 전역변수를 바꾸거나 공유 객체를 바꾸거나 웹 요청을 보내는 것은 출력이다.
3. 암묵적 입력은 인자로 암묵적 출력은 리턴값으로 바꾼다.
    - 한 번에 하나씩 입력은 인자로 출력은 리턴값으로 바꾼다.
    - 인자와 리턴값은 바뀌지 않는 불변값이라는 것이 중요하다. 리턴값이 나중에 바뀐다면 암묵적 출력이다. 또 인자로 받은 값이 바뀔 수 있다면 암묵적 입력이다.

## 🍄 12. 연습문제 풀어보기

```jsx
// 원래 코드
function update_tax_dom(){
	set_tax_dom(shopping_cart_total * 0.10);
}

// 바뀐 코드
function update_tax_dom(){
	set_tax_dom(update_tax(shopping_cart_total));
}

function calc_tax(amount) {
	return amount * 0.10;
}
```

```jsx
// 원래 코드
function update_shipping_icons() {
	var buy_buttons = get_buy_buttons_dom(); // 페이지에 있는 모든 구매 버튼을 가져와 반복문 적용
	for(var i = 0; i < buy_buttons.length; i++) {
		var button = buy_buttons[i];
		var item = button.item;
		if(item.price + shopping_cart_total >= 20){
			button.show_free_shipping_icon();
		}else {
			button.hide_free_shipping_icon();
		}
	}
}

// 바뀐 코드
function update_shipping_icons() {
	var buy_buttons = get_buy_buttons_dom(); // 페이지에 있는 모든 구매 버튼을 가져와 반복문 적용
	for(var i = 0; i < buy_buttons.length; i++) {
		var button = buy_buttons[i];
		var item = button.item;
		if(gets_free_shipping(item.price, shopping_cart_total)){
			button.show_free_shipping_icon();
		}else {
			button.hide_free_shipping_icon();
		}
	}
}

function gets_free_shipping(price, total){
	return price + total >= 20;
}
```

## 🍄 13. 정리

- 액션은 암묵적인 입력 또는 출력을 가지고 있다.
- 계산의 정의에 따르면 계산은 암묵적인 입력이나 출력이 없어야 한다.
- 공유 변수(전역변수 같은)는 일반적으로 암묵적 입력 또는 출력이다.
- 암묵적 입력은 인자로 바꿀 수 있다.
- 암묵적 출력은 리턴값으로 바꿀 수 있다.
- 함수형 원칙을 적용하면 액션은 줄어들고 계산은 늘어난다.