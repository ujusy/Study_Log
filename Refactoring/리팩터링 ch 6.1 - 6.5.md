# 리팩터링 ch 6.1 - 6.5

리팩터링 ch 6은 기본적인 리팩터링 챕터이다.

6장에서는

- 함수 추출하기
- 함수 인라인하기
- 변수 추출하기
- 변수 인라인하기
- 함수 선언 바꾸기
- 변수 캡슐화하기
- 변수 이름 바꾸기
- 매개변수 객체 만들기

로 구성되어있다.



## ch 6.1 함수 추출하기

전체적으로 보면 아주 간단한데 체계적으로 정리해놨다.

1. 함수를 새로 만들고 목적을 잘 드러내는 이름을 붙인다.
2. 추출한 코드를 원본 함수에서 복사하여 새 함수에 붙여넣는다.
3. 추출한 코드 중 지역 변수를 참조하면 매개변수로 전달한다.

예시

```javascript
function printOwing(invoice) {
  let outstanding = 0; // 선언문 위치도 중요
  for (const o of invoice.orders) {
    outstanding += 0.amount;
  }
}
```

- 함수 추출하기

  ```javascript
  function printOwing(invoice) {
    const outStanding = calculateOutstanding(invoice);
  }
  
  function calculateOutstanding(invoice) {
    let outstanding = 0;
    for (const o of invoice.orders) {
      outstanding += 0.amount;
    }
    return outstanding;
  }
  ```

- 코딩 스타일에 맞게 변경

  ```javascript
  function calculateOutstanding(invoice) {
    let result = 0;
    for (const o of invoice.orders) {
      result += 0.amount;
    }
    return result;
  }
  ```

  
