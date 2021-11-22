# 리팩터링 ch 6.1 - 6.6

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



## Ch 6.2 함수 인라인하기

- 반대 리팩터링: 함수 추출하기

1. 다형 메서드인지 확인한다
   - 서브클래스에서 오버라이드하는 메서드는 인라인하면 안된다.
2. 인라인할 함수를 호출하는 곳을 모두 찾는다.
3. 각 호출문을 함수 본문으로 교체한다.
4. 교체할 때마다 테스트한다.
5. 원래 함수를 삭제한다.

```javascript
function reportLines(aCustomer) {
  const lines = [];
  gaterCustomerData(lines, aCustomer);
  return lines;
}

function gaterCustomerData(out, aCustomer) {
  out.push(["name",aCustomer.name]);
  out.push(["location",aCustomer.location]);
}
```

- 인라인할 함수의 한 줄 씩 잘라내고 붙이기.

  ```javascript
  function reportLines(aCustomer) {
    const lines = [];
  	lines.push(["name",aCustomer.name]);
    return lines;
  }
  
  function gaterCustomerData(out, aCustomer) {
    out.push(["location",aCustomer.location]);
  }
  ```

  ```javascript
  function reportLines(aCustomer) {
    const lines = [];
  	lines.push(["name",aCustomer.name]);
    lines.push(["location",aCustomer.location]);
    return lines;
  }
  ```



## Ch 6.3 변수 추출하기

반대 리팩터링: 변수 인라인하기

- 언제사용해야할까
  - 표현식이 너무 복잡해서 이해하기 어려울 때.
  - 지역변수를 활용하여 표현식을 쪼개보자.
- 장점
  - 추가한 변수는 디버깅에도 도움이 된다.



## Ch 6.4 변수 인라인하기

반대 리팩터링: 변수 추출하기

- 언제 사용해야할까

  - 이름이 표현식과 다를 바 없을 때

  ```javascript
  let basePrice = anOrder.basePrice;
  return (basePrice > 1000);
  ```

- 변수가 불변으로 선언되지 않았다면 불변으로 만든 후 테스트한다.



## Ch 6.5 함수 선언 바꾸기

== 함수 이름 바꾸기, 시그니처 바꾸기

- 중요한 이유 및 언제 사용해야할까

  - 이름이 좋으면 함수의 구현 코드를 살펴볼 필요 없이 호출문만 보고도 무슨 일을 하는지 파악할 수 있다.

- 이름 변경과 매개변수 추가를 모두 하고 싶으면 각각 독립적으로 처리

  - 문제가 생길 때를 대비하여 마이그레이션 절차를 따르자.

  1. 추출 단계를 수월히하기 위해 함수의 본문 리팩토링
  2. 새로운 함수로 추출
  3. 테스트 
  4. 기존 함수 인라인
  5. 이름을 임시로 붙였다면 변경

  ```javascript
  / * 함수 이름 바꾸기 * /
  function circum(radius) {
    return circumference(radius);
  }
  
  function circumference(radius) {
    return 2 * Math.PI * radius;
  } 
  
  // 기존 함수인 circum이 새로운 함수인 circumference를 리턴하게 한다.
  // 클라이언트 모두가 새로운 함수로 변경되었다면 circum 삭제한다.
  
  
  / * 매개 변수 추가하기 * / 
  // 기존
  addReservation(customer) {
    this.newAddReservation(customer);
  }
  
  newAddReservation(customer) {
    ...
  }
   
  // 변경
  addReservation(customer) {
    this.newAddReservation(customer, false);
  }
  
  newAddReservation(customerm, isPriority) {
    assert(isPrioiry === true || isPrioiry === false); // 새로 추가한 매개변수 실제로 사용하는지 확인
    ...
  }
  ```



## Ch 6.6 변수 캡슐화하기

- 데이터의 유효범위가 넓을수록 캡슐화해야한다.

- 접근할 수 있는 범위가 넓은 데이터를 옮길 때에는 먼저 그 데이터로의 접근을 독점하는 함수를 만드는 식으로 캡슐화하는 것이 가장 좋은 방법일 때가 많다.

- 객체지향에서 private 필드를 사용하여 가시 범위를 제한한다. 

  ```javascript
  let defaultOwnerData = {firstName: "마틴", lastName: "파울러"}
  export function defaultOwner() {return new Person(defaultOwnerData);}
  // 속성을 다시 대입하는 연산이 무시되므로 변경을 막는다.
  export function setDefaultOwner(arg) {defaultOwnerData = arg;}
  
  class Person {
    constructor(data) {
      this.lastName = data.lastName;
      this.firstName = data.firstName;
    }
    get lastName() {return this.lastName};
    get firstName() {return this.firstName};
  }
  ```

  

