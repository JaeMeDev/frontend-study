# 🌸 Chapter 7: 신뢰할 수 없는 코드를 쓰면서 불변성 지키기

## 🍄 1. 레거시 코드와 불변성

- **레거시 코드**란 오래전에 만든 것으로, 지금 당장 고칠 수 없어서 그대로 사용해야 하는 코드를 말한다.
- 이번 챕터의 예시는 MegaMart는 블랙프라이데이 세일을 준비하기로 했다. 매달 한 번씩 세일을하며 마케팅 부서는 오래된 재고를 처리하기 위해 할인을 하려 한다. 지금까지 장바구니 관련된 코드는 모두 카피-온-라이트를 적용해 유지했다. 하지만 블랙 프라이데이 행사 코드는 많은 곳에서 장바구니 데이터를 변경한다. 잘 동작하긴 하지만 오래전 만든 코드라 당장 바꿀 시간이 없다. 그래서 레거시 코드에 쓸 수 있는 안전한 인터페이스가 필요하다.

```jsx
function add_item_to_cart(name, price){
	var item = make_cart_item(name, price);
	shopping_cart = add_item(shopping_cart, item);
	var total = calc_total(shopping_cart);
	set_cart_total_dom(total);
	update_shipping_icons(shopping_cart); 
	update_tax_dom(total); 
	// black friday 코드
	black_friday_promotion(shopping_cart);
}
```

- 추가된 함수를 호출하면 카피-온-라이트의 원칙을 지킬 수 없다. 그리고 `black_friday_promotion()` 함수를 고칠 수도 없다.
- 카피-온-라이트 원칙을 지키면서 안전하게 함수를 사용할 수 있는 원칙이 있는데, 이를 **방어적 복사**라 한다.

## 🍄 2. 우리가 만든 카피-온-라이트 코드는 신뢰할 수 없는 코드와 상호작용해야 한다.

- 블랙 프라이데이 행사 함수는 카피-온-라이트를 적용한 코드가 아니기 때문에 신뢰할 수 없으며 불변성을 지킬 수 없다.
- 전에 만들었던 코드들은 불변성이 지켜지는 안전지대에 있다. 안전지대에 있는 코드는 걱정없이 쓸 수 있다.
- 블랙 프라이데이 행사 함수는 안전지대 밖에 있지만, 요구사항을 구현하려면 전에 만든 코드에서 안전하지 않은 함수를 사용해야 한다. 따라서 블랙 프라이데이 함수의 입력과 출력을 통해 안전지대에 있는 코드와 데이터를 주고 받아야 한다.
- 여기서 문제가 있다. 안전지대 밖으로 나가는 데이터는 잠재적으로 바뀔 수 있다. 신뢰할 수 없는 코드가 데이터를 바꿀 수 있기 때문이다. 마찬가지로 신뢰할 수 없는 코드에서 안전지대로 들어오는 데이터 역시 잠재적으로 바뀔 수 있다.
- 불변성을 지키면서 데이터를 주고받는 방법을 찾아야한다. 카피-온-라이트 패턴으로는 이 문제를 해결할 수 없다. 블랙프라이데이 코드는 분석하기 힘든 레거시 코드라 어떤 일이 일어날지 정확히 알 수 없다. 그래서 데이터가 바뀌는 것을 완벽히 막아주는 원칙이 필요하다. 이 원칙을 **방어적 복사**라 한다.

## 🍄 3. 방어적 복사는 원본이 바뀌는 것을 막아준다.

- 신뢰할 수 없는 코드가 들어올때 동작 순서
    1. 바뀔 수도 있는 데이터가 신뢰할 수 없는 코드에서 안전지대로 들어온다.
    2. 들어온 데이터로 깊은 복사본을 만들고 변경 가능한 원본은 버린다.
    3. 신뢰할 수 있는 코드만 복사본을 쓰기 때문에 데이터는 바뀌지 않는다.
- 안전지대에서 나가는 데이터 동작 순서
    1. 안전지대 밖으로 나가는 데이터는 신뢰할 수 없는 코드가 값을 변경할 수 있어서 변경하지 못하도록 해야 한다.
    2. 나가는 데이터도 깊은 복사본을 만든다.
    3. 깊은 복사를 한 데이터를 안전지대에서 내보낸다.
- 들어오고 나가는 데이터의 복사본을 만드는 것이 방어적 복사가 동작하는 방식이다. 안전지대에 불변성을 유지하고, 바꾸리 수도 있는 데이터가 안전지대로 들어오지 못하도록 하는 것이 방어적 복사의 목적이다.

## 🍄 4. 방어적 복사 구현하기

- 인자로 들어온 값이 변경될 수도 있는 함수를 사용하면서 불변성은 지켜야 한다. 방어적 복사를 사용하면 데이터가 바뀌는 것을 막아 불변성을 지킬 수 있다.
- `black_friday_promotion()` 함수는 인자로 받은 장바구니 값을 바꾼다. 장바구니 값을 넘기기전에 깊은 복사를 해서 함수에 넘긴다. 이렇게 하면 인자로 넘긴 원본이 바뀌지 않는다.

```jsx
// 원래코드
function add_item_to_cart(name, price){
	var item = make_cart_item(name, price);
	shopping_cart = add_item(shopping_cart, item);
	var total = calc_total(shopping_cart);
	set_cart_total_dom(total);
	update_shipping_icons(shopping_cart); 
	update_tax_dom(total); 

	black_friday_promotion(shopping_cart);
}

// 수정코드
function add_item_to_cart(name, price){
	var item = make_cart_item(name, price);
	shopping_cart = add_item(shopping_cart, item);
	var total = calc_total(shopping_cart);
	set_cart_total_dom(total);
	update_shipping_icons(shopping_cart); 
	update_tax_dom(total); 
	var cart_copy = deepCopy(shopping_cart)
	black_friday_promotion(cart_copy);
}
```

- black_friday_promotion() 함수에 결과를 받아야 한다. 복사본을 전달해 black_friday_promotion() 함수가 변경한 cart_copy가 결괏값이다. 어떻게 해야 cart_copy를 안전하게 쓸 수 있을까?

```jsx
// 원래 코드
function add_item_to_cart(name, price){
	var item = make_cart_item(name, price);
	shopping_cart = add_item(shopping_cart, item);
	var total = calc_total(shopping_cart);
	set_cart_total_dom(total);
	update_shipping_icons(shopping_cart); 
	update_tax_dom(total); 
	var cart_copy = deepCopy(shopping_cart)
	black_friday_promotion(cart_copy);
}

// 수정 코드
function add_item_to_cart(name, price){
	var item = make_cart_item(name, price);
	shopping_cart = add_item(shopping_cart, item);
	var total = calc_total(shopping_cart);
	set_cart_total_dom(total);
	update_shipping_icons(shopping_cart); 
	update_tax_dom(total); 
	var cart_copy = deepCopy(shopping_cart)
	black_friday_promotion(cart_copy);
	shopping_cart = deepCopy(cart_copy);
}
```

## 🍄 5. 방어적 복사 규칙

### 🌻 5-1. 규칙1: 데이터가 안전한 코드에서 나갈때 복사하기

- 변경 불가능한 데이터가 신뢰할 수 없는 코드로 나갈 때, 아래 단계로 원본 데이터를 보호할 수 있다.
1. 불변성 데이터를 위한 깊은 복사본을 만든다.
2. 신뢰할 수 없는 코드로 복사본을 전달한다.

### 🌻 5-2. 규칙 2: 안전한 코드로 데이터가 들어올 때 복사하기

- 신뢰할 수 없는 코드에서 변경될 수도 있는 데이터가 들어온다면 다음 단계를 따른다.
1. 변경될 수도 있는 데이터가 들어오면 바로 깊은 복사본을 만들어 안전한 코드로 전달한다.
2. 복사본을 안전한 코드에서 사용한다.

## 🍄 6. 신뢰할 수 없는 코드 감싸기

- 블랙 프라이데이 행사를 위한 코드에 방어적 복사를 잘 적용하였다. 하지만 나중에 `add_item_to_cart()` 코드를 보면 복사본을 만드는 이유를 모를 수도 있다. 또한 `black_friday_promotion()` 코드가 다시 필요할 수도 있다. 그리고 다시 만든다고 해도 방어적 복사를 잘 만들기 어렵다. 따라서 방어적 복사 코드를 분리해 새로운 함수로 만들어 두면 좋다.

```jsx
// 원래 코드
function add_item_to_cart(name, price){
	var item = make_cart_item(name, price);
	shopping_cart = add_item(shopping_cart, item);
	var total = calc_total(shopping_cart);
	set_cart_total_dom(total);
	update_shipping_icons(shopping_cart); 
	update_tax_dom(total); 
	var cart_copy = deepCopy(shopping_cart)
	black_friday_promotion(cart_copy);
	shopping_cart = deepCopy(cart_copy);
}

// 수정 코드
function add_item_to_cart(name, price){
	var item = make_cart_item(name, price);
	shopping_cart = add_item(shopping_cart, item);
	var total = calc_total(shopping_cart);
	set_cart_total_dom(total);
	update_shipping_icons(shopping_cart); 
	update_tax_dom(total); 

	shopping_cart = black_friday_promotion_safe(shopping_cart);
}

function black_friday_promotion_safe(cart){
	var cart_copy = deepCopy(cart)
	black_friday_promotion(cart_copy);
	return deepCopy(cart_copy);
}
```

## 🍄 7. 방어적 복사가 익숙할 수도 있다.

- 모듈이 서로 통신하기 위해 방어적 복사를 구현했다면 **비공유 아키텍처**라 한다.

### 🌻 7-1. 웹 API속에 방어적 복사

- 대부분의 웹 기반 API는 암묵적으로 방어적 복사를 한다.
- JSON 데이터가 API에 요청으로 들어왔다고 해보자. 클라이언트는 데이터를 인터넷을 통해 API로 보내려고 직ㄱ렬화한다. 이때 JSON 데이터는 깊은 복사본(deep copy )이다. 서비스가 잘 동작한다면 JSON으로 응답한다. 이 JSON 역시 깊은 복사본이다.
- 이처럼 웹 API는 방어적 복사를 한다. 마이크로서비스나 서비스 지향 시스템이 서로 통신할 때 방어적 복사를 한다는 점은 장점이다.

### 🌻 7-2. 얼랭과 엘릭서에서 방어적 복사

- 얼랭과 엘릭서는 방어적 복사를 잘 구현했다. 얼랭에서 두 프로세스가 서로 메시지를 주고받을 때 수신자의 메일박스에 메시지가 복사된다. 또 프로세스에서 데이터가 나갈 때도 데이터를 복사한다. 방어적 복사는 얼랭 시스템이 고가용성을 보장하는 핵심 기능이다.

## 🍄 8. 카피-온-라이트와 방어적 복사 비교

### 🌻 8-1. 카피-온-라이트

- 언제 쓸까?
    - 통제할 수 있는 데이터를 바꿀 때 카피-온-라이트를 쓴다.
- 어디서 쓸까?
    - 안전지대 어디서나 쓸 수 있다. 사실 카피-온-라이트가 불변성을 가진 안전지대를 만든다.
- 복사 방식
    - 얕은 복사
- 규칙
    1. 바꿀 데이터의 얕은 복사를 만든다.
    2. 복사본을 변경한다.
    3. 복사본을 리턴한다.

### 🌻 8-2. 방어적 복사

- 언제 쓸까?
    - 신뢰할 수 없는 코드와 데이터를 주고받아야 할 때 방어적 복사를 쓴다.
- 어디서 쓸까?
    - 안전지대의 경계에서 데이터가 오고 갈 때 방어적 복사를 쓴다.
- 복사 방식
    - 깊은 복사
- 규칙
    1. 안전지대로 들어오는 데이터에 깊은 복사를 만든다.
    2. 안전지대에서 나가는 데이터에 깊은 복사를 만든다.

## 🍄 9. 깊은 복사는 얕은 복사보다 비싸다.

- 깊은 복사는 원본과 어떤 데이터 구조도 공유하지 않는 것이 얕은 복사와 차이점이다. 중첩된 모든 객체나 배열을 복사한다. 얕은 복사에서는 바뀌지 않은 값이라면 원본과 복사본이 데이터를 공유한다.
- 깊은 복사는 모든 것을 복사한다. 데이터가 변경되면 안 되지만 신뢰할 수 없는 코드가 변경할지도 모른다면 깊은 복사를 사용해야 한다.
- 깊은 복사는 확실히 비싸다. 그래서 모든 곳에서 쓰지 않는다. 카피-온-라이트를 사용할 수 없는 곳에서 사용한다.

## 🍄 10. 자바스크립트에서 깊은 복사를 구현하는 것은 어렵다.

- 깊은 복사는 단순한 개념이기 때문에 만들기 쉬워 보인다. 하지만 자바스크립트에서는 표준 라이브러리가 좋지 않아 만들기가 어렵다.
- 그래서 `Loadsh` 라이브러리에 있는 깊은 복사 함수를 쓰는 것을 추천한다. `Lodash` 의 `.cloneDeep()` 함수는 중첩된 데이터에 깊은 복사를 한다. `Lodash` 는 많은 개발자가 신뢰하는 라이브러리이다.

## 🍄 11. 정리

- 방어적 복사는 불변성을 구현하는 원칙이다. 데이터가 들어오고 나갈 때 복사본을 만든다.
- 방어적 복사는 깊은 복사를 한다. 그래서 카피-온-라이트보다 비용이 더 든다.
- 카피-온-라이트와 다르게 방어적 복사는 불변성 원칙을 구현하지 않은 코드로부터 데이터를 보호해준다.
- 복사본이 많이 필요하지 않기 때문에 카피-온-라이트를 더 많이 사용한다. 방어적 복사는 신뢰할 수 없는 코드와 함께 사용할 때만 사용한다.
- 깊은 복사는 위에서 아래로 중첩된 데이터 전체를 복사한다. 얕은 복사는 필요한 부분만 최소한으로 복사한다.