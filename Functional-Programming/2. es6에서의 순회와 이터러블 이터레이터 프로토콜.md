# es6에서의 순회와 이터러블: 이터레이터 프로토콜

**[인프런] 함수형 프로그래밍과 JavaScript ES6+**  강의 정리

- 기존과 달라진 es6에서의 리스트 순회

  - for i++
  - for of

  ```jsx
  const list = [1,2,3];
  for (var =0; i <list.length; i++) {
  	log(list[i]);
  }
  ```

- `array`를 통해 알아보기

  ```jsx
  const arr = [1,2,3];
  for (const a of arr) log(a);
  ```

- `set`을 통해 알아보기

  ```jsx
  const set = new Set([1,2,3]);
  for (const a of set) log(a);
  ```

- `map`을 통해 알아보기

  ```jsx
  const map = new Map(['a',1]);
  for (const a of map) log(a);
  for (const a of map.keys()) log(a);
  for (const a of map.values()) log(a);
  for (const a of map.entries()) log(a);
  ```

- `Symbol.iterator`

- 이터러블/이터레이터 프로토콜

  - `이터러블`: 이터레이터를 리턴하는 [Symbol.iterator]()를 가진 값
  - `이터레이터`: {value, done} 객체를 리턴하는 next()를 가진 값
  - `이터러블/이터레이터 프로토콜`: 이터러블을 for ... of, 전개 연산자 등과 동작하도록 규약

- 사용자 정의 이터러블을 통해 알아보기

  ```jsx
  const iterable = {
  	[Symbol.iterator]() {
  		let i = 3;
  		return {
  			next() {
  				return i ==0? {done: true} : {value: i--, done: false}
  			}
  		}
  	}
  }
  
  let iterator = itereable[Symbol.iterator]();
  ```

- 전개 연산자(`...`)

  ```jsx
  const a = [1,2];
  log([...a,...arr, ...map.keys()]); // [1,2,1,2,3,"a"];
  ```

