# πΈ Chapter 5: λ μ’μ μ‘μ λ§λ€κΈ°

## πΒ 1. λΉμ¦λμ€ μκ΅¬ μ¬ν­κ³Ό μ€κ³λ₯Ό λ§μΆκΈ°

- μ‘μμμ κ³μ°μΌλ‘ λ¦¬ν©ν°λ§νλ κ³Όμ μ λ¨μνκ³  κΈ°κ³μ μ΄μλ€. κΈ°κ³μ μΈ λ¦¬ν©ν°λ§μ΄ ν­μ μ΅μ μ κ΅¬μ‘°λ₯Ό λ§λ€μ΄μ£Όλ κ²μ μλλ€. μ’μ κ΅¬μ‘°λ₯Ό μν΄μλ μ΄λ μ λ μ¬λμ μκΈΈμ΄ νμνλ€.
- `gets_free_shipping()` ν¨μλ λΉμ¦λμ€ μκ΅¬μ¬ν­μΌλ‘ λ΄€μ λ λ§μ§ μλ λΆλΆμ΄ μλ€. μκ΅¬ μ¬ν­μ μ₯λ°κ΅¬λμ λ΄κΈ΄ μ νμ μ£Όλ¬Έν  λ λ¬΄λ£ λ°°μ‘μΈμ§ νμΈνλ κ²μ΄λ€. νμ§λ§ ν¨μλ μ₯λ°κ΅¬λλ‘ λ¬΄λ£ λ°°μ‘μ νμΈνμ§ μκ³  μ νμ ν©κ³μ κ°κ²©μΌλ‘ νμΈνκ³  μλ€. μ΄λ λΉμ¦λμ€ μκ΅¬μ¬ν­μ λ§μ§ μλ μΈμμ΄λ€.

```jsx
function gets_free_shipping(total, item_price){ // μ΄ μΈμλ μκ΅¬μ¬ν­μ λ§μ§ μλλ€.
	return item_price + total >= 20;
}
```

- λ μ°Ύμλ³΄λ©΄ μ€λ³΅λ μ½λλ μ‘΄μ¬νλ€. ν©κ³μ μ ν κ°κ²©μ λνλ μ½λκ° λ κ΅°λ° μλ€. μ€λ³΅μ΄ ν­μ λμ κ²μ μλμ§λ§ μ½λμμ λλ λμμ΄λ€. μ½λμ λμλ λμ€μ λ¬Έμ κ° λ  μ μλ€.

```jsx
function gets_free_shipping(total, item_price){
	return item_price + total >= 20; // item + totalμ΄ μ€λ³΅λμ΄ μλ€.
}

function calc_total(cart){
	var total = 0;
	for(var i = 0; i < cart.length; i++){
		var item = cart[i];
		total += item.price; // item + totalμ΄ μ€λ³΅λμ΄ μλ€.
	}
	return total; 
}
```

- μ€λ³΅λ ν¨μλ₯Ό `gets_free_shipping(cart)` λ‘ μ₯λ°κ΅¬λλ‘ μΈμλ₯Ό λ°κΏμ£Όκ³  `calc_total()` ν¨μλ₯Ό μ¬μ¬μ©ν΄ μ€λ³΅μ μμ μ.

## πΒ 2. λΉμ¦λμ€ μκ΅¬ μ¬ν­κ³Ό ν¨μλ₯Ό λ§μΆκΈ°

- ν¨μμ λμμ λ°κΎΈλ κ²μ μλ°ν λ§νλ©΄ λ¦¬ν©ν°λ§μ΄λΌκ³  ν  μ μλ€.
- `gets_free_shipping()` ν¨μλ μ₯λ°κ΅¬λ κ°μ μΈμλ‘ λ°μ ν©κ³κ° 20λ³΄λ€ ν¬κ±°λ κ°μμ§ μλ €μ€λ€.

```jsx
// μλμ½λ
function gets_free_shipping(total, item_price){
	return item_price + total >= 20;
}

// λ°λ μ½λ
function gets_free_shipping(cart){
	return calc_total(cart) >= 20;
}
```

- λ°κΎΌ ν¨μλ ν©κ³μ μ ν κ°κ²© λμ  μ₯λ°κ΅¬λ λ°μ΄ν°λ₯Ό μ¬μ©νλ€. μ₯λ°κ΅¬λλ μ μμκ±°λμμ λ§μ΄ μ¬μ©νλ μν°ν°νμμ΄κΈ° λλ¬Έμ λΉμ¦λμ€ μκ΅¬ μ¬ν­κ³Ό μ λ§λλ€.
- ν¨μ μκ·Έλμ²κ° λ°λλ©΄ μ¬μ©νλ λΆλΆλ μμ ν΄μΌ νλ€.

```jsx
// μ½λ μμ 
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

## πΒ 3. μμΉ: μλ¬΅μ  μλ ₯κ³Ό μΆλ ₯μ μ μμλ‘ μ’λ€.

- μΈμκ° μλ λͺ¨λ  μλ ₯μ μλ¬΅μ  μλ ₯μ΄κ³  λ¦¬ν΄κ°μ΄ μλ λͺ¨λ  μΆλ ₯μ μλ¬΅μ  μΆλ ₯μ΄λ€.
- μλ¬΅μ  μλ ₯κ³Ό μλ¬΅μ  μΆλ ₯μ΄ μλ ν¨μλ₯Ό **κ³μ°**μ΄λΌ νλ€.
- κ³μ°μ λ§λ€κΈ° μν΄ μλ¬΅μ  μλ ₯κ³Ό μΆλ ₯μ μμ λ μμΉμ μ‘μμλ μ μ©ν  μ μλ€. μ‘μμμ λͺ¨λ  μλ¬΅μ  μλ ₯κ³Ό μΆλ ₯μ μμ μ§ μλλΌλ μλ¬΅μ  μλ ₯κ³Ό μΆλ ₯μ μ€μ΄λ©΄ μ’λ€.
- μ΄λ€ ν¨μμ μλ¬΅μ  μλ ₯κ³Ό μΆλ ₯μ΄ μλ€λ©΄ λ€λ₯Έ μ»΄ν¬λνΈμ κ°νκ² μ°κ²°λ μ»΄ν¬λνΈλΌκ³  ν  μ μλ€. λ€λ₯Έ κ³³μμ μ¬μ©ν  μ μκΈ° λλ¬Έμ λͺ¨λμ΄ μλλ€.
- μλ¬΅μ  μλ ₯κ³Ό μΆλ ₯μ λͺμμ μΌλ‘ λ°κΎΌλ€λ©΄ λͺ¨λνλ μ»΄ν¬λνΈλ₯Ό λ§λ€ μ μλ€.
- μλ¬΅μ  μλ ₯μ΄ μλ ν¨μλ μ‘°μ¬ν΄μ μ¬μ©ν΄μΌ νλ€. λ§μ½ λ€λ₯Έ κ³³μμ μ΄ κ°μ΄ μ°μ΄λ©΄ λ¬Έμ κ° μκΈΈ κ²μ΄λ€.
- μλ¬΅μ  μΆλ ₯ μ­μ μ‘°μ¬ν΄μ μ¬μ©ν΄μΌ νλ€. λ€λ₯Έ κ³³μ μν₯μ μ€ μ μλ€.
- μλ¬΅μ  μλ ₯κ³Ό μΆλ ₯μ΄ μλ ν¨μλ μλ¬΄ λλ μ€νν  μ μκΈ° λλ¬Έμ νμ€νΈνκΈ° μ΄λ ΅λ€. λͺ¨λ  μλ ₯κ°μ μ€μ νκ³  νμ€νΈλ₯Ό λλ¦° νμ λͺ¨λ  μΆλ ₯κ°μ νμΈν΄μΌ νλ€. μλ ₯κ³Ό μΆλ ₯μ΄ λ§μμλ‘ νμ€νΈλ λ μ΄λ €μμ§λ€.
- κ³μ°μ μλ¬΅μ  μλ ₯κ³Ό μΆλ ₯μ΄ μκΈ° λλ¬Έμ νμ€νΈνκΈ° μ½λ€. λͺ¨λ  μλ¬΅μ  μλ ₯κ³Ό μΆλ ₯μ μμ μ§ λͺ»ν΄ μ‘μμ κ³μ°μΌλ‘ λ°κΎΈμ§ λͺ»ν΄λ μλ¬΅μ  μλ ₯κ³Ό μλ¬΅μ  μΆλ ₯μ μ€μ΄λ©΄ νμ€νΈνκΈ° μ½κ³  μ¬μ¬μ©νκΈ° μ’λ€.

## πΒ 4. μλ¬΅μ  μλ ₯κ³Ό μΆλ ₯ μ€μ΄κΈ°

- `update_shipping_icons()` ν¨μμ μλ¬΅μ  μλ ₯κ³Ό μΆλ ₯μ μ€μ¬λ³΄μ.

```jsx
// μλ μ½λ
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

// μμ λ μ½λ
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

- ν¨μλ₯Ό νΈμΆνλ κ³³λ μμ νμ.

```jsx
// μλ μ½λ
function calc_cart_total(){
	shopping_cart_total = calc_total(shopping_cart);
	set_cart_total_dom();
	update_shipping_icons(); 
	update_tax_dom(); 
}

// μμ λ μ½λ
function calc_cart_total(){
	shopping_cart_total = calc_total(shopping_cart);
	set_cart_total_dom();
	update_shipping_icons(shopping_cart); 
	update_tax_dom(); 
}

```

## πΒ 5. μ°μ΅λ¬Έμ 

- μ‘μμ μ μ­λ³μλ₯Ό μ½λ λΆλΆμ μΈμλ‘ λ°κΏλ³΄μ.

```jsx
// μμ  μ½λ
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

// μλ‘μ΄ μ½λμ μΆκ°
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
// μ μ­λ³μ μ½λ λΆλΆμ μμ€ μ½λ
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

## πΒ 6. μ½λ λ€μ μ΄ν΄λ³΄κΈ°

```jsx
function add_item_to_cart(name, price){ // μ΄ ν¨μλ 'κ΅¬λ§€νκΈ°'λ²νΌμ λλ₯Ό λ μ¬μ©
	shopping_cart = add_item(shopping_cart, name, price);
	calc_cart_total(shopping_cart);
}

function calc_cart_total(cart){ // μ΄ ν¨μλ μ‘°κΈ κ³Όν κ² κ°λ€. add_item_to_cart()μ μμ΄λ λ  κ² κ°λ€.
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
// μμ λ μ½λ
function add_item_to_cart(name, price){ // μ΄ ν¨μλ 'κ΅¬λ§€νκΈ°'λ²νΌμ λλ₯Ό λ μ¬μ©
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

## πΒ 7. κ³μ° λΆλ₯νκΈ°

- μλ―Έ μλ κ³μΈ΅μ λν΄ μμλ³΄κΈ° μν΄ κ³μ°μ λΆλ₯νμ.
- κ³μ° ν¨μλ₯Ό λ€μ¬λ€λ³΄μ. μ΄λ€ ν¨μκ° μ₯λ°κ΅¬λ κ΅¬μ‘°λ₯Ό μμμΌ νλ©΄ (C), μ νμ λν κ΅¬μ‘°λ₯Ό μμμΌ νλ©΄ (I)λΌκ³  νννκ³ , λΉμ¦λμ€ κ·μΉμ λν ν¨μλΌλ©΄ (B)λ‘ νμν΄λ³΄μ.

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

- μκ°μ΄ μ§λ μλ‘ λλ κ·Έλ£Ήμ λ λͺνν΄μ§ κ²μ΄λ€.

## πΒ 8. μμΉ: μ€κ³λ μμΌμλ μ½λλ₯Ό νΈλ κ²μ΄λ€

- ν¨μλ₯Ό μ¬μ©νλ©΄ κ΄μ¬μ¬λ₯Ό μμ°μ€λ½κ² λΆλ¦¬ν  μ μλ€. ν¨μλ μΈμλ‘ λκΈ°λ κ°κ³Ό κ·Έ κ°μ μ¬μ©νλ λ°©λ²μ λΆλ¦¬νλ€.

### π»Β 8-1. μ¬μ¬μ©νκΈ° μ½λ€.

- ν¨μλ μμΌλ©΄ μμμλ‘ μ¬μ¬μ©νκΈ° μ½λ€. νλ μΌλ μ κ³  μΈ λ κ°μ μ λ§μ΄ νμ§ μμλ λλ€.

### π»Β 8-2. μ μ§λ³΄μνκΈ° μ½λ€.

- μμ ν¨μλ μ½κ² μ΄ν΄ν  μ μκ³  μ μ§λ³΄μνκΈ° μ½λ€. μ½λκ° μκΈ° λλ¬Έμ μ¬λ°λ₯Έμ§ μλμ§ λͺννκ² μ μ μλ€.

### π»Β 8-3. νμ€νΈνκΈ° μ½λ€.

- μμ ν¨μλ νμ€νΈνκΈ° μ’λ€. ν κ°μ§ μΌλ§ νκΈ° λλ¬Έμ ν κ°μ§λ§ νμ€νΈνλ©΄ λλ€.
- ν¨μμ νΉλ³ν λ¬Έμ κ° μμ΄λ κΊΌλΌ κ²μ΄ μλ€λ©΄ λΆλ¦¬νλ κ²μ΄ μ’λ€.

## πΒ 9. add_item()μ λΆλ¦¬ν΄ λ μ’μ μ€κ³ λ§λ€κΈ°

- `add_item()` μ μ λμνλ€. μ₯λ°κ΅¬λμ μ νμ μΆκ°νλ κ°λ¨ν μΌλ§ νλ κ² κ°λ€. νμ§λ§ μμΈν μ΄ν΄λ³΄λ©΄ `add_item()` μ λ€ λΆλΆμΌλ‘ λλ μ μλ€.

```jsx
function add_item(cart, name, price){
	var new_cart = cart.slice(); // 1. λ°°μ΄ λ³΅μ¬
	new_cart.push({ // 2. item κ°μ²΄ λ§λ€κΈ°, 3. λ³΅μ¬λ³Έμ item μΆκ°νκΈ°
		name,
		price
	});
	return new_cart; // 4. λ³΅μ¬λ³Έ λ¦¬ν΄
}
```

- λΆλ¦¬ν μ½λλ₯Ό λ³λμ ν¨μλ‘ λ§λ€μ΄λ³΄μ.

```jsx
// 2. item κ°μ²΄ λ§λ€κΈ°
function make_cart_item(name, price){
	return { name, price };
}

function add_item(cart, item){
	var new_cart = cart.slice(); // 1. λ°°μ΄ λ³΅μ¬
	new_cart.push(item); // 3. λ³΅μ¬λ³Έμ item μΆκ°
	return new_cart; // 4. λ³΅μ¬λ³Έ λ¦¬ν΄
}

// νΈμΆ μ½λ μμ 
add_item(shopping_cart, make_cart_item("shoes", 3.45));
```

- item κ΅¬μ‘°λ§ μκ³  μλ `make_cart_item()` ν¨μμ cart κ΅¬μ‘°λ§ μκ³  μλ `add_item()` ν¨μλ‘ λλ  μλ μ½λλ₯Ό μμ νμλ€. μ΄λ κ² λΆλ¦¬νλ©΄ cartμ itemμ λλ¦½μ μΌλ‘ νμ₯ν  μ μλ€. μλ₯Ό λ€μ΄ λ°°μ΄μΈ cartλ₯Ό ν΄μ λ§΅κ³Ό κ°μ μλ£κ΅¬μ‘Έ λ°κΎΌλ€κ³  νμ λ λ³κ²½ν΄μΌ ν  λΆλΆμ΄ μ λ€.

## πΒ 10. μΉ΄νΌ-μ¨-λΌμ΄νΈ ν¨ν΄μ λΉΌλ΄κΈ°

- `add_item()` ν¨μλ ν¬κΈ°κ° μκ³  κ΄μ°?μ ν¨μμ΄λ€. μμΈν λ³΄λ©΄ μΉ΄νΌ-μ¨-λΌμ΄νΈλ₯Ό μ¬μ©ν΄ λ°°μ΄μ ν­λͺ©μ μΆκ°νλ ν¨μμ΄λ€.
- μ΄ ν¨μλ μΌλ°μ μΈ λ°°μ΄κ³Ό ν­λͺ©μ μΈ μ μμ§λ§ μ΄λ¦μ΄ μΌλ°μ μ΄μ§ μλ€. μ΄λ¦λ§ λ³΄λ©΄ μ₯λ°κ΅¬λλ₯Ό λκ²¨μΌ μΈ μ μμ κ² κ°λ€.
- ν¨μ μ΄λ¦κ³Ό μΈμ μ΄λ¦μ λ μΌλ°μ μΈ μ΄λ¦μΌλ‘ μμ νμ.

```jsx
function add_element_last(array, elem){
	var new_array = array.slice();
	new_array.push(elem);
	return new_array;
}
```

- μλμ `add_item()` ν¨μλ κ°λ¨νκ² λ€μ λ§λ€ μ μλ€.

```jsx
function add_item(cart, item){
	return add_element_last(cart, item);
}
```

- `add_element_last()` ν¨μλ μ₯λ°κ΅¬λμ μ νμμλ§ μΈ μ μλ ν¨μκ° μλ μ΄λ€ λ°°μ΄μ΄λ ν­λͺ©μλ μΈ μ μλ μ΄λ¦μΌλ‘ λ°κΏ¨λ€. μ΄ ν¨μλ μ¬μ¬μ©ν  μ μλ **μ νΈλ¦¬ν° ν¨μ**μ΄λ€.

## πΒ 11. add_item() μ¬μ©νκΈ°

- `add_item()` μ cart, name, priceμΈμκ° νμν ν¨μμλ€. νμ§λ§ μ΄μ λ cartμ item μΈμλ§ νμνλ€.
- κ·Έλ κΈ° λλ¬Έμ `add_item()` μ νΈμΆνλ λΆλΆμ μμ ν΄μ€μΌ νλ€.

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

## πΒ 12. μ°μ΅λ¬Έμ 

```jsx
// μμ μ½λ
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
// μμ λ μ½λ
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

## πΒ 13. μ λ¦¬

- μΌλ°μ μΌλ‘ μλ¬΅μ  μλ ₯κ³Ό μΆλ ₯μ μΈμμ λ¦¬ν΄κ°μΌλ‘ λ°κΏ μλ΄λ κ²μ΄ μ’λ€.
- μ€κ³λ μμΌμλ κ²μ νΈλ κ²μ΄λ€. νλ €μλ κ²μ μΈμ λ  λ€μ ν©μΉ  μ μλ€.
- μμΌμλ κ²μ νμ΄ κ° ν¨μκ° νλμ μΌλ§ νλλ‘ νλ©΄, κ°λμ μ€μ¬μΌλ‘ μ½κ² κ΅¬μ±ν  μ μλ€.