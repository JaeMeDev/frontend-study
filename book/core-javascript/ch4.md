# ๐ธ Chapter 4: ์ฝ๋ฐฑ ํจ์

## ๐ย 1. ์ฝ๋ฐฑ ํจ์๋?

- ์ฝ๋ฐฑ ํจ์(callback function)๋ ๋ค๋ฅธ ์ฝ๋์ ์ธ์๋ก ๋๊ฒจ์ฃผ๋ ํจ์์ด๋ค. ์ฝ๋ฐฑ ํจ์๋ฅผ ๋๊ฒจ๋ฐ์ ์ฝ๋๋ ์ด ์ฝ๋ฐฑ ํจ์๋ฅผ ํ์์ ๋ฐ๋ผ ์ ์ ํ ์์ ์ ์คํํ๋ค.
- ์ฝ๋ฐฑ ํจ์๋ **์ ์ด๊ถ**๊ณผ ๊ด๋ จ์ด ๊น๋ค.
- callback์ โ๋ถ๋ฅด๋คโ, โํธ์ถ(์คํ)ํ๋คโ๋ ์๋ฏธ์ธ call๊ณผ, โ๋ค๋์์ค๋คโ, โ๋๋๋คโ๋ ์๋ฏธ์ธ back์ ํฉ์ฑ์ด๋ก โ๋๋์ ํธ์ถํด๋ฌ๋ผโ๋ ๋ช๋ น์ด๋ค.
- ์ฝ๋ฐฑ ํจ์๋ ๋ค๋ฅธ ์ฝ๋(ํจ์ ๋๋ ๋ฉ์๋)์๊ฒ ์ธ์๋ก ๋๊ฒจ์ค์ผ๋ก์จ ๊ทธ ์ ์ด๊ถ๋ ํจ๊ป ์์ํ ํจ์์ด๋ค. ์ฝ๋ฐฑ ํจ์๋ฅผ ์์๋ฐ์ ์ฝ๋๋ ์์ฒด์ ์ธ ๋ด๋ถ ๋ก์ง์ ์ํด ์ด ์ฝ๋ฐฑ ํจ์๋ฅผ ์ ์ ํ ์์ ์ ์คํํ  ๊ฒ์ด๋ค.

## ๐ย 2. ์ ์ด๊ถ

### ๐ปย 2-1. ํธ์ถ ์์ 

```jsx
var count = 0;
var timer = setInterval(function () {
	console.log(count);
	if(++count > 4) clearInterval(timer);
});
```

- 1๋ฒ์งธ ์ค์์ count ๋ณ์๋ฅผ ์ ์ธํ๊ณ  0์ ํ ๋นํ๋ค. 2๋ฒ์งธ ์ค์์๋ timer ๋ณ์๋ฅผ ์ ์ธํ๊ณ  ์ฌ๊ธฐ์ setInterval์ ์คํํ ๊ฒฐ๊ณผ๋ฅผ ํ ๋นํ๋ค. setInterval์ ํธ์ถํ  ๋ ๋๊ฐ์ ๋งค๊ฐ๋ณ์๋ฅผ ์ ๋ฌํ๋๋ฐ, ๊ทธ์ค ์ฒซ ๋ฒ์งธ๋ ์ต๋ช ํจ์์ด๊ณ  ๋ ๋ฒ์งธ๋ 300์ด๋ผ๋ ์ซ์์ด๋ค.

```jsx
var intervalID = scope.setInterval(func, delay[, param1, param2, ...]);
```

- ์ฐ์  scope์๋ Window ๊ฐ์ฒด ๋๋ Worker์ ์ธ์คํด์ค๊ฐ ๋ค์ด์ฌ ์ ์๋ค. ๋ ๊ฐ์ฒด ๋ชจ๋ setInterval ๋ฉ์๋๋ฅผ ์ ๊ณตํ๊ธฐ ๋๋ฌธ์ด๋ค. ์ผ๋ฐ์ ์ธ ๋ธ๋ผ์ฐ์  ํ๊ฒฝ์์๋ window๋ฅผ ์๋ตํด์ ํจ์์ฒ๋ผ ์ฌ์ฉ ๊ฐ๋ฅํ  ๊ฒ์ด๋ค.
- ๋งค๊ฐ๋ณ์๋ก๋ func, delay ๊ฐ์ ๋ฐ์ผ์ ์ ๋ฌํด์ผ ํ๊ณ , ์ธ ๋ฒ์งธ ๋งค๊ฐ๋ณ์๋ถํฐ๋ ์ ํ์ ์ด๋ค. func๋ ํจ์์ด๊ณ , delay๋ ๋ฐ๋ฆฌ์ด(ms) ๋จ์์ ์ซ์์ด๋ฉฐ, ๋๋จธ์ง๋ func ํจ์๋ฅผ ์คํํ  ๋ ๋งค๊ฐ๋ณ์๋ก ์ ๋ฌํ  ์ธ์์ด๋ค. func์ ๋๊ฒจ์ค ํจ์๋ ๋งค delay ๋ง๋ค ์คํ๋๋ฉฐ, ๊ทธ ๊ฒฐ๊ณผ ์ด๋ ํ ๊ฐ๋ ๋ฆฌํดํ์ง ์๋๋ค.
- setInterval๋ฅผ ์คํํ๋ฉด ๋ฐ๋ณต์ ์ผ๋ก ์คํ๋๋ ๋ด์ฉ ์์ฒด๋ฅผ ํน์ ํ  ์ ์๋ ๊ณ ์ ํ ID ๊ฐ์ด ๋ฐํ๋๋ค. ์ด๋ฅผ ๋ณ์์ ๋ด๋ ์ด์ ๋ ๋ฐ๋ณต ์คํ๋๋ ์ค๊ฐ์ ์ข๋ฃ(clearInterval)ํ  ์ ์๊ฒ ํ๊ธฐ ์ํด์์ด๋ค.

```jsx
var count = 0;
var cbFunc = function(){
	console.log(count);
	if(++count > 4) clearInterval(timer);
}

var timer = setInterval(cbFunc, 300);

// -- ์คํ ๊ฒฐ๊ณผ --
// 0 (0.3์ด)
// 1 (0.6์ด)
// 2 (0.9์ด)
// 3 (1.2์ด)
// 4 (1.5์ด)
```

- timer ๋ณ์์๋ setInterval์ ID ๊ฐ์ด ๋ด๊ธด๋ค. setInterval์ ์ ๋ฌํ ์ฒซ ๋ฒ์งธ ์ธ์์ธ cbFunc ํจ์(์ฝ๋ฐฑ ํจ์)๋ 0.3์ด๋ง๋ค ์๋์ผ๋ก ์คํ๋  ๊ฒ์ด๋ค.
- setInterval์ด๋ผ๊ณ  ํ๋ โ๋ค๋ฅธ ์ฝ๋โ์ ์ฒซ ๋ฒ์งธ ์ธ์๋ก์ cbFunc ํจ์๋ฅผ ๋๊ฒจ์ฃผ์ ์ ์ด๊ถ์ ๋๊ฒจ๋ฐ์ setInterval์ด ์ค์ค๋ก์ ํ๋จ์ ๋ฐ๋ผ ์ ์ ํ ์์ ์(0.3์ด๋ง๋ค) ์ด ์ต๋ช ํจ์๋ฅผ ์คํํ๋ค. ์ด์ฒ๋ผ ์ฝ๋ฐฑ ํจ์์ ์ ์ด๊ถ์ ๋๊ฒจ๋ฐ์ ์ฝ๋๋ ์ฝ๋ฐฑ ํจ์ ํธ์ถ ์์ ์ ๋ํ ์ ์ด๊ถ์ ๊ฐ์ง๋ค.

### ๐ปย 2-2. ์ธ์

```jsx
var newArr = [10, 20, 30].map(function (currentValue, index) {
	console.log(currentValue, index);
	return currentValue + 5;
});

console.log(newArr);

// -- ์คํ ๊ฒฐ๊ณผ --
// 10 0
// 20 1
// 30 2
// [15, 25, 35]
```

```jsx
Array.prototype.map(callback[, thisArg])
callback: function(currentValue, index, array)
```

- map ๋ฉ์๋๋ ์ฒซ ๋ฒ์งธ ์ธ์๋ก callback ํจ์๋ฅผ ๋ฐ๊ณ , ์๋ต ๊ฐ๋ฅํ ๋ ๋ฒ์งธ ์ธ์๋ก ์ฝ๋ฐฑ ํจ์ ๋ด๋ถ์์ this๋ก ์ธ์ํ  ๋์์ ํน์ ํ  ์ ์๋ค. thisArg๋ฅผ ์๋ตํ  ๊ฒฝ์ฐ์๋ ์ผ๋ฐ์ ์ธ ํจ์์ ๋ง์ฐฌ๊ฐ์ง๋ก ์ ์ญ๊ฐ์ฒด๊ฐ ๋ฐ์ธ๋ฉ๋๋ค.
- map ๋ฉ์๋๋ ๋ฉ์๋์ ๋์์ด ๋๋ ๋ฐฐ์ด์ ๋ชจ๋  ์์๋ค์ ์ฒ์๋ถํฐ ๋๊น์ง ํ๋์ฉ ๊บผ๋ด์ด ์ฝ๋ฐฑ ํจ์๋ฅผ ๋ฐ๋ณต ํธ์ถํ๊ณ , ์ฝ๋ฐฑ ํจ์์ ์คํ ๊ฒฐ๊ณผ๋ค์ ๋ชจ์ ์๋ก์ด ๋ฐฐ์ด์ ๋ง๋ ๋ค. ์ฝ๋ฐฑ ํจ์์ ์ฒซ ๋ฒ์งธ ์ธ์์๋ ๋ฐฐ์ด์ ์์ ์ค ํ์ฌ๊ฐ์ด, ๋ ๋ฒ์งธ ์ธ์์๋ ํ์ฌ๊ฐ์ ์ธ๋ฑ์ค๊ฐ, ์ธ ๋ฒ์งธ ์ธ์์๋ map ๋ฉ์๋์ ๋์์ด ๋๋ ๋ฐฐ์ด ์์ฒด๊ฐ ๋ด๊ธด๋ค.
- ์ ์ด์ฟผ๋ฆฌ์ ๋ฉ์๋๋ค์ ๊ธฐ๋ณธ์ ์ผ๋ก ์ฒซ ๋ฒ์งธ ์ธ์์ index๊ฐ, ๋ ๋ฒ์งธ ์ธ์์ currentValue๊ฐ ์จ๋ค. ์ ์ด์ฟผ๋ฆฌ์ ๋ฐฉ์์ฒ๋ผ ์์๋ฅผ ๋ฐ๊พธ์ด ์ฌ์ฉํด๋ณด๋ฉด ์ด๋ค์ผ์ด ์๊ธธ๊น?

```jsx
var newArr = [10, 20, 30].map(function (index, currentValue) {
	console.log(index, currentValue);
	return currentValue + 5;
});

console.log(newArr);

// -- ์คํ ๊ฒฐ๊ณผ --
// 10 0
// 20 1
// 30 2
// [5, 6, 7]
```

- ์ฌ๋์ ๋จ์ด๋ก ์ ๊ทผํ๊ธฐ ๋๋ฌธ์ ์์๋ฅผ ๋ฐ๊พธ๋๋ผ๋ ๊ฐ ๋จ์ด์ ์๋ฏธ๊ฐ ๋ฐ๋์ง ์์ผ๋๊น ๋ฌธ์  ์์ ๊ฒ์ด๋ผ๊ณ  ์๊ฐํ๊ธฐ ์ฝ์ง๋ง, ์  ๋จ์ด๋ค์ ์ฌ์ฉ์๊ฐ ๋ช๋ชํ ๊ฒ ๋ฟ์ด๋ค. ์ปดํจํฐ๋ ๊ทธ์  ์ฒซ ๋ฒ์งธ, ๋ ๋ฒ์งธ์ ์์์ ์ํด์๋ง ๊ฐ๊ฐ์ ๊ตฌ๋ถํ๊ณ  ์ธ์ํ๋ค.
- ์ํ๋ ์๊ฐ์ ์๋์ด ์ธ๋ฆฌ๋ ๊ฒฐ๊ณผ๋ฅผ ์ป๊ธฐ ์ํด์๋ ์๊ณ๊ฐ ์ ํ ๊ท์น์ ์ง์ผ์ผ ํ๋ฏ์ด map ๋ฉ์๋๋ฅผ ํธ์ถํด์ ์ํ๋ ๋ฐฐ์ด์ ์ป์ผ๋ ค๋ฉด map ๋ฉ์๋์ ์ ์๋ ๊ท์น์ ๋ฐ๋ผ ํจ์๋ฅผ ์์ฑํด์ผ ํ๋ค.
- ์ด์ฒ๋ผ ์ฝ๋ฐฑ ํจ์์ ์ ์ด๊ถ์ ๋๊ฒจ๋ฐ์ ์ฝ๋๋ ์ฝ๋ฐฑ ํจ์๋ฅผ ํธ์ถํ  ๋ ์ธ์์ ์ด๋ค ๊ฐ๋ค์ ์ด๋ค ์์๋ก ๋๊ธธ ๊ฒ์ธ์ง์ ๋ํ ์ ์ด๊ถ์ ๊ฐ์ง๋ค.

### ๐ปย 2-3. this

- ์ฝ๋ฐฑ ํจ์๋ ํจ์์ด๊ธฐ ๋๋ฌธ์ ๊ธฐ๋ณธ์ ์ผ๋ก this๊ฐ ์ ์ญ๊ฐ์ฒด๋ฅผ ์ฐธ์กฐํ์ง๋ง, ์ ์ด๊ถ์ ๋๊ฒจ๋ฐ์ ์ฝ๋์์ ์ฝ๋ฐฑ ํจ์์ ๋ณ๋๋ก this๊ฐ ๋  ๋์์ ์ง์ ํ ๊ฒฝ์ฐ์๋ ๊ทธ ๋์์ ์ฐธ์กฐํ๊ฒ ๋๋ค.

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

- ๋ฉ์๋ ๊ตฌํ์ ํต์ฌ์ call/apply ๋ฉ์๋์ ์๋ค. this์๋ thisArg ๊ฐ์ด ์์ ๊ฒฝ์ฐ์๋ ๊ทธ ๊ฐ์, ์์ ๊ฒฝ์ฐ์๋ ์ ์ญ๊ฐ์ฒด๋ฅผ ์ง์ ํ๊ณ , ์ฒซ ๋ฒ์งธ ์ธ์์๋ ๋ฉ์๋์ this๊ฐ ๋ฐฐ์ด์ ๊ฐ๋ฆฌํฌ ๊ฒ์ด๋ฏ๋ก ๋ฐฐ์ด์ i๋ฒ์งธ ์์ ๊ฐ์, ๋ ๋ฒ์งธ ์ธ์์๋ i ๊ฐ์, ์ธ ๋ฒ์งธ ์ธ์์๋ ๋ฐฐ์ด ์์ฒด๋ฅผ ์ง์ ํด ํธ์ถํ๋ค. ๊ทธ ๊ฒฐ๊ณผ๊ฐ ๋ณ์ mappedValue์ ๋ด๊ฒจ mappedArr์ i๋ฒ์งธ ์ธ์์ ํ ๋น๋๋ค.
- this์ ๋ค๋ฅธ ๊ฐ์ด ๋ด๊ธฐ๋ ์ด์ ๋ ๋ฐ๋ก ์ ์ด๊ถ์ ๋๊ฒจ๋ฐ์ ์ฝ๋์์ call/apply ๋ฉ์๋์ ์ฒซ ๋ฒ์งธ ์ธ์์ ์ฝ๋ฐฑ ํจ์ ๋ด๋ถ์์์ this๊ฐ ๋  ๋์์ ๋ช์์ ์ผ๋ก ๋ฐ์ธ๋ฉํ๊ธฐ ๋๋ฌธ์ด๋ค.

```jsx
setTimeout(function () { console.log(this); }, 300); // (1) Window { ... }

[1,2,3,4,5].forEach(function(x){
	console.log(this); // (2) Window { ... }
});

document.body.innerHTML += '<button id="a">ํด๋ฆญ</button>';
document.body.querySelector('#a')
	.addEventListener('click', function(e) {
		console.log(this, e); // (3) <button id="a">ํด๋ฆญ</button> MouseEvent ...
});
```

## ๐ย 3. ์ฝ๋ฐฑ ํจ์๋ ํจ์๋ค.

- ์ฝ๋ฐฑ ํจ์๋ ์ด๋ค ๊ฐ์ฒด์ ๋ฉ์๋๋ฅผ ์ ๋ฌํ๋๋ผ๋ ๊ทธ ๋ฉ์๋๋ ๋ฉ์๋๊ฐ ์๋ ํจ์๋ก์ ํธ์ถ๋๋ค.

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

- obj๋ฅผ this๋ก ํ๋ ๋ฉ์๋๋ฅผ ๊ทธ๋๋ก ์ ๋ฌํ ๊ฒ์ด ์๋๋ผ, obj.logValues๊ฐ ๊ฐ๋ฆฌํค๋ ํจ์๋ง ์ ๋ฌํ๋ค. ์ด ํจ์๋ ๋ฉ์๋๋ก์ ํธ์ถํ  ๋๊ฐ ์๋ ํ obj์์ ์ง์ ์ ์ธ ์ฐ๊ด์ด ์์ด์ง๋ค.
- forEach์ ์ํด ์ฝ๋ฐฑ์ด ํจ์๋ก์ ํธ์ถ๋๊ณ , ๋ณ๋๋ก this๋ฅผ ์ง์ ํ๋ ์ธ์๋ฅผ ์ง์ ํ์ง ์์์ผ๋ฏ๋ก ํจ์ ๋ด๋ถ์์์ this๋ ์ ์ญ๊ฐ์ฒด๋ฅผ ๋ฐ๋ผ๋ณด๊ฒ ๋๋ค.
- ์ด๋ค ํจ์์ ์ธ์์ ๊ฐ์ฒด์ ๋ฉ์๋๋ฅผ ์ ๋ฌํ๋๋ผ๋ ์ด๋ ๊ฒฐ๊ตญ ๋ฉ์๋๊ฐ ์๋ ํจ์์ผ ๋ฟ์ด๋ค.

## ๐ย 4. ์ฝ๋ฐฑ ํจ์ ๋ด๋ถ์ this์ ๋ค๋ฅธ ๊ฐ ๋ฐ์ธ๋ฉํ๊ธฐ

- ์ ํต์ ์ผ๋ก๋ this๋ฅผ ๋ค๋ฅธ ๋ณ์์ ๋ด์ ์ฝ๋ฐฑ ํจ์๋ก ํ์ฉํ  ํจ์์์๋ this ๋์  ๊ทธ ๋ณ์๋ฅผ ์ฌ์ฉํ๊ฒ ํ๊ณ , ์ด๋ฅผ ํด๋ก์ ๋ก ๋ง๋๋ ๋ฐฉ์์ด ๋ง์ด ์ฌ์ฉ๋์๋ค.

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

- ์ด ๋ฐฉ์์ ์ค์ ๋ก this๋ฅผ ์ฌ์ฉํ์ง๋ ์์ ๋ฟ๋๋ฌ ๋ฒ๊ฑฐ๋กญ๊ธฐ ๊ทธ์ง ์๋ค. ์ฐจ๋ผ๋ฆฌ this๋ฅผ ์์ ์์ฐ๋ ํธ์ด ๋ ๋ซ๊ฒ ๋ค๋ ์๊ฐ์ด ๋ ๋ค.

```jsx
var obj1 = {
	name: 'obj1',
	func: function(){
		console.log(obj1.name);
	}
};

setTimeout(obj1.func, 1000);
```

- ํจ์ฌ ๊ฐ๊ฒฐํ๊ณ  ์ง๊ด์ ์ด์ง๋ง ์์ฌ์ด ๋ถ๋ถ๋ ์๋ค. ์์ฑํ ํจ์๋ฅผ this๋ฅผ ์ด์ฉํด ๋ค์ํ ์ํฉ์ ์ฌํ์ฉํ  ์ ์๊ฒ ๋์๋ค.

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

- ๋คํํ ์ด์ ๋ ์ ํต์ ์ธ ๋ฐฉ์์ ์์ฌ์์ ๋ณด์ํ๋ ํ๋ฅญํ ๋ฐฉ๋ฒ์ด ์๋ค. ๋ฐ๋ก ES5์์ ๋ฑ์ฅํ bind ๋ฉ์๋๋ฅผ ์ด์ฉํ๋ ๋ฐฉ๋ฒ์ด๋ค.

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

## ๐ย 5. ์ฝ๋ฐฑ ์ง์ฅ๊ณผ ๋น๋๊ธฐ ์ ์ด

- ์ฝ๋ฐฑ ์ง์ฅ์ ์ฝ๋ฐฑ ํจ์๋ฅผ ์ต๋ช ํจ์๋ก ์ ๋ฌํ๋ ๊ณผ์ ์ด ๋ฐ๋ณต๋์ด ์ฝ๋์ ๋ค์ฌ์ฐ๊ธฐ ์์ค์ด ๊ฐ๋นํ๊ธฐ ํ๋ค ์ ๋๋ก ๊น์ด์ง๋ ํ์์ผ๋ก ์๋ฐ์คํฌ๋ฆฝํธ์์ ํํ ๋ฐ์ํ๋ ๋ฌธ์ ์ด๋ค.
- ์ฃผ๋ก ์ด๋ฒคํธ ์ฒ๋ฆฌ๋ ์๋ฒ ํต์ ๊ณผ ๊ฐ์ด ๋น๋๊ธฐ์ ์ธ ์์์ ์ํํ๊ธฐ ์ํด ์ด๋ฐ ํํ๊ฐ ์์ฃผ ๋ฑ์ฅํ๊ณค ํ๋๋ฐ, ๊ฐ๋์ฑ์ด ๋จ์ด์ง๋ฟ๋๋ฌ ์ฝ๋๋ฅผ ์์ ํ๊ธฐ๋ ์ด๋ ต๋ค.
- **๋น๋๊ธฐ**๋ ๋๊ธฐ์ ๋ฐ๋๋ง์ด๋ค. ๋๊ธฐ์ ์ธ ์ฝ๋๋ ํ์ฌ ์คํ ์ค์ธ ์ฝ๋๊ฐ ์๋ฃ๋ ํ์์ผ ๋ค์ ์ฝ๋๋ฅผ ์คํํ๋ ๋ฐฉ์์ด์ง๋ง ๋น๋๊ธฐ์ ์ธ ์ฝ๋๋ ํ์ฌ ์คํ ์ค์ธ ์ฝ๋์ ์๋ฃ ์ฌ๋ถ์ ๋ฌด๊ดํ๊ฒ ์ฆ์ ๋ค์ ์ฝ๋๋ก ๋์ด๊ฐ๋ค. CPU์ ๊ณ์ฐ์ ์ํด ์ฆ์ ์ฒ๋ฆฌ๊ฐ ๊ฐ๋ฅํ ๋๋ถ๋ถ์ ์ฝ๋๋ ๋๊ธฐ์ ์ธ ์ฝ๋์ด๋ค.
- ์ฌ์ฉ์์ ์์ฒญ์ ์ํด ํน์  ์๊ฐ์ด ๊ฒฝ๊ณผ๋๊ธฐ ์ ๊น์ง ์ด๋ค ํจ์์ ์คํ์ ๋ณด๋ฅํ๋ค๊ฑฐ๋(setTimeout), ์ฌ์ฉ์์ ์ง์ ์ ์ธ ๊ฐ์์ด ์์ ๋ ๋น๋ก์ ์ด๋ค ํจ์๋ฅผ ์คํํ๋๋ก ๋๊ธฐํ๋ค๊ฑฐ๋(addEventListener), ์น๋ธ๋ผ์ฐ์  ์์ฒด๊ฐ ์๋ ๋ณ๋์ ๋์์ ๋ฌด์ธ๊ฐ๋ฅผ ์์ฒญํ๊ณ  ๊ทธ์ ๋ํ ์๋ต์ด ์์ ๋ ๋น๋ก์ ์ด๋ค ํจ์๋ฅผ ์คํํ๋๋ก ๋๊ธฐํ๋ ๋ฑ(XMLHttpRequest), ๋ณ๋์ **์์ฒญ, ์คํ ๋๊ธฐ, ๋ณด๋ฅ** ๋ฑ๊ณผ ๊ด๋ จ๋ ์ฝ๋๋ ๋น๋๊ธฐ์ ์ธ ์ฝ๋์ด๋ค.

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
			}, 500, '์นดํ๋ผ๋ผ');
		}, 500, '์นดํ๋ชจ์นด');
	}, 500, '์๋ฉ๋ฆฌ์นด๋ธ');
}, 500, '์์คํ๋ ์');
```

- ๋ชฉ์  ๋ฌ์ฑ์๋ ์ง์ฅ์ด ์์ง๋ง ๋ค์ฌ์ฐ๊ธฐ ์์ค์ด ๊ณผ๋ํ๊ฒ ๊น์ด์ก์๋ฟ๋๋ฌ ๊ฐ์ด ์ ๋ฌ๋๋ ์์๊ฐ ์๋์์ ์๋ก ํฅํ๊ณ  ์์ด ์ด์ํ๊ฒ ๋๊ปด์ง๋ค. ๊ฐ๋์ฑ ๋ฌธ์ ์ ์ด์ํจ์ ๋์์ ํด๊ฒฐํ๋ ๊ฐ์ฅ ๊ฐ๋จํ(?) ๋ฐฉ๋ฒ์ ์ต๋ช์ ์ฝ๋ฐฑ ํจ์๋ฅผ ๋ชจ๋ ๊ธฐ๋ชํจ์๋ก ์ ํํ๋ ๊ฒ์ด๋ค.

```jsx
var coffeeList = '';

var addEspresso = function (name) {
	coffeeList = name;
	console.log(coffeeList);
	setTimeout(addAmericano, 500, '์๋ฉ๋ฆฌ์นด๋ธ');
};
var addAmericano = function (name) {
	coffeeList += ', ' + name;
	console.log(coffeeList);
	setTimeout(addMocha, 500, '์นดํ๋ชจ์นด');
};
var addMocha = function (name) {
	coffeeList += ', ' + name;
	console.log(coffeeList);
	setTimeout(addLatte, 500, '์นดํ๋ผ๋ผ');
};
var addLatte = function (name) {
	coffeeList += ', ' + name;
	console.log(coffeeList);
};

setTimeout(addEspresso, 500, '์์คํ๋ ์');
```

- ์ด ๋ฐฉ์์ ์ฝ๋์ ๊ฐ๋์ฑ์ ๋์ผ๋ฟ ์๋๋ผ ํจ์ ์ ์ธ๊ณผ ํจ์ ํธ์ถ๋ง ๊ตฌ๋ถํ  ์ ์๋ค๋ฉด ์์์๋ถํฐ ์๋๋ก ์์๋๋ก ์ฝ์ด๋ด๋ ค๊ฐ๋ ๋ฐ ์ด๋ ค์์ด ์๋ค. ๋ํ ๋ณ์๋ฅผ ์ต์๋จ์ผ๋ก ๋์ด์ฌ๋ฆผ์ผ๋ก์จ ์ธ๋ถ์ ๋ธ์ถ๋๊ฒ ๋์ง๋ง ์ ์ฒด๋ฅผ ์ฆ์ ์คํ ํจ์ ๋ฑ์ผ๋ก ๊ฐ์ธ๋ฉด ๊ฐ๋จํ ํด๊ฒฐ๋  ๋ฌธ์ ์ด๋ค.
- ์ผํ์ฑ ํจ์๋ฅผ ์ ๋ถ ๋ณ์์ ํ ๋นํ๋ ๊ฒ์ ์ฝ๋๋ช์ ์ผ์ผ์ด ๋ฐ๋ผ๋ค๋์ผ ํ๋ฏ๋ก ์คํ๋ ค ํท๊ฐ๋ฆด ์์ง๊ฐ ์๋ค.
- ์๋ฐ์คํฌ๋ฆฝํธ๋ ๋น๋๊ธฐ์ ์ธ ์ผ๋ จ์ ์์์ ๋๊ธฐ์ ์ผ๋ก, ํน์ ๋๊ธฐ์ ์ธ ๊ฒ์ฒ๋ผ ๋ณด์ด๊ฒ๋ ์ฒ๋ฆฌํด์ฃผ๋ ์ฅ์น๋ฅผ ๋ง๋ จํ๊ณ ์ ๋์์์ด ๋ธ๋ ฅํ๋ค. ES6์์๋ Promise, Generator ๋ฑ์ด ๋์๋๊ณ , ES2017์์๋ async/await๊ฐ ๋์๋ฌ๋ค.

```jsx
new Promise(function (resolve){
	setTimeout(function(){
		var name = "์์คํ๋ ์ค";
		console.log(name);
		resolve(name);
	}, 500);
}).then(function (prevName){
	return new Promise(function (resolve) {
		setTimeout(function(){
			var name = prevName + ', ์๋ฉ๋ฆฌ์นด๋ธ';
			console.log(name);
			resolve(name);
		}, 500);
	});
});

// ... ์ค๋ณต๋ ์ฝ๋
```

- new ์ฐ์ฐ์์ ํจ๊ป ํธ์ถํ Promise์ ์ธ์๋ก ๋๊ฒจ์ฃผ๋ ์ฝ๋ฐฑ ํจ์๋ ํธ์ถํ  ๋ ๋ฐ๋ก ์คํ๋์ง๋ง ๊ทธ ๋ด๋ถ์ resolve ๋๋ reject ํจ์๋ฅผ ํธ์ถํ๋ ๊ตฌ๋ฌธ์ด ์์ ๊ฒฝ์ฐ ๋ ์ค ํ๋๊ฐ ์คํ๋๊ธฐ ์ ๊น์ง๋ ๋ค์(then) ๋๋ ์ค๋ฅ ๊ตฌ๋ฌธ(catch)์ผ๋ก ๋์ด๊ฐ์ง ์๋๋ค. ๋ฐ๋ผ์ ๋น๋๊ธฐ ์์์ด ์๋ฃ๋  ๋ ๋น๋ก์ resolve ๋๋ reject๋ฅผ ํธ์ถํ๋ ๋ฐฉ๋ฒ์ผ๋ก ๋น๋๊ธฐ ์์์ ๋๊ธฐ์  ํํ์ด ๊ฐ๋ฅํ๋ค.

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

addCoffee('์์คํ๋ ์')()
	.then(addCoffee('์๋ฉ๋ฆฌ์นด๋ธ'))
	.then(addCoffee('์นดํ๋ชจ์นด'))
	.then(addCoffee('์นดํ๋ผ๋ผ'));
```

- ๋ฐ๋ณต์ ์ธ ๋ด์ฉ์ ํจ์ํํด์ ๋์ฑ ์งง๊ฒ ํํํ  ์ ์๋ค.

```jsx
var addCoffee = function (prevName, name) {
	setTimeout(function () {
		coffeeMaker.next(prevName ? prevName + ', ' + name : name);
	}, 500);
};
var coffeeGenerator = function* () {
	var espresso = yield addCoffee('', '์์คํ๋ ์');
	console.log(espresso);
	var americano = yield addCoffee(espresso, '์๋ฉ๋ฆฌ์นด๋ธ');
	console.log(americano);
	var mocha = yield addCoffee(americano, '์นดํ๋ชจ์นด');
	console.log(mocha);
	var latte = yield addCoffee(mocha, '์นดํ๋ผ๋ผ');
	console.log(latte);
};
var coffeeMaker = coffeeGenerator();
coffeeMaker.next();
```

- 6๋ฒ์งธ ์ค์ `*` ์ด ๋ถ์ ํจ์๊ฐ ๋ฐ๋ก Generator ํจ์์ด๋ค. Generator ํจ์๋ฅผ ์คํํ๋ฉด Iterator๊ฐ ๋ฐํ๋๋๋ฐ Iterator๋ next๋ผ๋ ๋ฉ์๋๋ฅผ ๊ฐ์ง๊ณ  ์๋ค. ์ด next ๋ฉ์๋๋ฅผ ํธ์ถํ๋ฉด Generator ํจ์ ๋ด๋ถ์์ ๊ฐ์ฅ ๋จผ์  ๋ฑ์ฅํ๋ yield์์ ํจ์์ ์คํ์ด ๋ฉ์ถ๋ค. ์ดํ ๋ค์ next ๋ฉ์๋๋ฅผ ํธ์ถํ๋ฉด ์์ ๋ฉ์ท๋ ๋ถ๋ถ๋ถํฐ ์์ํด์ ๊ทธ๋ค์์ ๋ฑ์ฅํ๋ yield์์ ํจ์์ ์คํ์ด ๋ฉ์ถ๋ค. ๋น๋๊ธฐ ์์์ด ์๋ฃ๋๋ ์์ ๋ง๋ค next ๋ฉ์๋๋ฅผ ํธ์ถํด์ค๋ค๋ฉด Generator ํจ์ ๋ด๋ถ์ ์์ค๊ฐ ์์์๋ถํฐ ์๋๋ก ์์ฐจ์ ์ผ๋ก ์งํ๋  ๊ฒ์ด๋ค.

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
	await _addCoffee('์์คํ๋ ์');
	console.log(coffeeList);
	await _addCoffee('์๋ฉ๋ฆฌ์นด๋ธ');
	console.log(coffeeList);
	await _addCoffee('์นดํ๋ชจ์นด');
	console.log(coffeeList);
	await _addCoffee('์นดํ๋ผ๋ผ');
	console.log(coffeeList);
};
coffeeMaker();
```

- ๋น๋๊ธฐ ์์์ ์ํํ๊ณ ์ ํ๋ ํจ์ ์์ async๋ฅผ ํ๊ธฐํ๊ณ , ํจ์ ๋ด๋ถ์์ ์๋ฆด์ ์ธ ๋น๋๊ธฐ ์์์ด ํ์ํ ์์น๋ง๋ค await๋ฅผ ํ๊ธฐํ๋ ๊ฒ๋ง์ผ๋ก ๋ค์ ๋ด์ฉ์ Promise๋ก ์๋ ์ ํํ๊ณ , ํด๋น ๋ด์ฉ์ด resolve๋ ์ดํ์์ผ ๋ค์์ผ๋ก ์งํํ๋ค.