# 리팩터링 ch 6.7 - 6.11

## ch 6.7 변수 이름 바꾸기

함수 호출 한 번으로 끝나는 것이 아닌 값이 영속되는 필드라면 이름에 신경써서 어떠한 역할을 하는지 직관적으로 알 수 있어야한다.

자바스크립트의 경우 함수의 매개변수 이름을 지을 때 이름 앞에 타입을 드러내는 문자를 붙인다. 

- 폭 넓게 사용되는 변수라면 변수 캡슐화하기를 고려



## Ch 6.8 매개변수 객체 만들기

데이터 뭉치를 데이터 구조로 묶자. 이러면 데이터 사이의 관계가 명확해진다는 장점이 있다.

- (다시 한번 이해해보기) 이런 데이터 구조를 새로 발견하면 이 데이터 구조를 활용하는 형태로 프로그램 동작을 재구성한다. 데이터 구조에 담길 데이터에 공통으로 적용되는 동작을 추출해서 함수로 만드는 것이다.

1. 클래스로 데이터 구조를 만들자. (값 객체)
2. 함수 선언 바꾸기로 새 데이터 구조를 매개변수로 추가한다.
3. 함수 호출 시 데이터 구조 인스턴스를 넘기도록 수정한다.
4. 기존 매개변수를 사용하던 코드를 새 데이터 구조의 원소를 사용하도록 바꾼다.

매개변수로 최소값, 최대값을 받는 경우이다.

- 원래 함수

  ```javascript
  function readingsOutsideRange(station, min, max) {
    return station.readings
    	.filter(r => r.temp < min || r.temp > max);
  }

- Min - max를 위한 클래스 작성

  ```javascript
  class NumberRange {
    contructor(min, max) {
      this._data = {min: min, max: max};
    }
    get min() {return this._data.min;}
    get max() {return this._data.max;}
  }
  ```

- 기존 함수에 매개변수 추가

  ```javascript
  function readingsOutsideRange(station, min, max, range) {
    // 자바스크립트가 아닌 다른 언어를 사용할 때는 매개변수 자리에 null을 적어두자.
    return station.readings 
    	.filter(r => r.temp < min || r.temp > max);
  }
  ```

- 호출문 및 변경된 함수

  ```javascript
  const range = New NumberRange(operatingPlan.temperatureFloor, operationPlan.temperatureCeiling);
  
  function readingsOutsideRange(station, range) {
    // 자바스크립트가 아닌 다른 언어를 사용할 때는 매개변수 자리에 null을 적어두자.
    return station.readings 
    	.filter(r => r.temp < ranage.min || r.temp > range.max);
  }
  ```



## Ch 6.9 여러 함수를 클래스로 묶기

클래스는 데이터와 함수를 하나의 공유 환경으로 묶은 후, 다른 프로그램 요소와 어우러질 수 있도록 그중 일부를 외부에 제공.

공통 데이터를 중심으로 긴밀하게 엮여 작동하는 함수 무리를 발견하면 클래스 하나로 묶자. 

- 장점: 이 함수들이 공유하는 공통 환경을 더 명확하게 표현할 수 있고, 각 함수에 전달되는 인수를 줄여서 객체 안에서의 함수 호출을 간결하게 만들 수 있다.
- 만들어진 객체를 시스템의 다른 부분에 전달하기 위한 참조 제공 가능.

1. 함수들이 공유하는 공통 데이터 레코드를 캡슐화한다.
2. 공통 레코드를 사용하는 함수 각각을 새 클래스로 옮긴다.
3. 데이터를 조작하는 로직들은 함수로 추출해서 새 클래스로 옮긴다.


- 측정값을 다음과 같이 기록

  ```javascript
  reading = {customer: "ivan", quantity: 10, month: 5, yaer: 2017},
  ```
  
- 레코드를 클래스로 변환하기 위해 레코드를 캡슐화
  - getter, setter를 통해서는 값에 접근할 수 있다.

  ```javascript
  class Reading {
    constructor(data) {
      this._customer = data.customer;
      this._quantity = data.quantity;
      this._month = data.month;
      this._year = data.year;
    }
    
    get customer() {return this._cutomer;}
    get quantity() {return this._quantity;}
    get month() {return this._month;}
    get year() {return this._year;}
  }
  ```
  
- 데이터를 조작하는 로직들은 함수로 추출해서 클래스에 반영

  ```javascript
  class Reading {
    constructor(data) {
      this._customer = data.customer;
      this._quantity = data.quantity;
      this._month = data.month;
      this._year = data.year;
    }
    
    get customer() {return this._cutomer;}
    get quantity() {return this._quantity;}
    get month() {return this._month;}
    get year() {return this._year;}
    
    get calculateBaseCharge() {
      return baseRate(this.month, this.yaer) * this.quantity;
    }
  }
  ```
  
  
  
  
  
  
  
  ## Ch 6.10 여러 함수를 변환 함수로 묶기
  
  6.9의 경우에는 여러 함수를 클래스로 묶었다면 6.10은 여러 함수를 변환 함수로 묶는 케이스이다.
  
  책에서는 원본 데이터가 코드 안에서 갱신될 때는 클래스로 뮦는 편이 훨씬 낫다고 표현하고있따.
  
  변환함수로 묶으면 가공한 데이터를 새로운 레코드에 저장하므로, 원본 데이터가 수정되면 일관성이 깨질 수 있다.
  
  개인적으로 클래스로 묶는 방식이 편하다고 생각하기 때문에 실제 업무에서 변환 함수로 묶는 경우는 거의 없을것 같다.
  
  
  
  ## Ch 6.11 단계 쪼개기
  
  - 서로 다른 두 대상을 한꺼번에 다루는 코드 -> 별개 모듈로 나누자.
    - 코드를 수정해야할 때 두 대상을 동시에 생각할 필요 없이 하나에만 집중하기 위해
    - 모듈이 잘 분리되어있다면 다른 모듈의 상세 내용은 전혀 기억하지 못해도 원하는 대로 수정 가능
  
  1. 두 번째 단계에 해당하는 코드를 독립 함수로 추출
  2. 테스트
  3. 중간 데이터 구조를 만들어서 앞에서 추출한 함수의 인수로 추가한다.
  4. 테스트
  5. 추출한 두 번째 단계 함수의 매개변수를 하나씩 검토한다. 그중 첫 번째 단계에서 사용되는 것은 중간 데이터 구조로 옮긴다. 
  6. 첫 번째 단계 코드를 함수로 추출하면서 중간 데이터 구조를 반환하도록 만든다.
  
  
  
  ```javascript
  function priceOrder(product, quantity, shippinhMethod) {
    const basePrice = product.basePrice * quantity;
    const discount = Math.max(quantity - product.discountThreshold, 0) * product.basePrice * product.discountRate;
    const shippingCase = (basePrice > shippingMethod.discountThreshold) ? shippingMethod.discountedFee: shippingMethod.feePerCase;
    const shippingCost = quantity * shippingPerCase;
    const price = basePrice - discount + shippingCost;
    return price;
  }
  ```
  
  
  
  - 배송비 계산 부분을 함수로 추출한다.
  
  ```javascript
  function priceOrder(product, quantity, shippinhMethod) {
    const basePrice = product.basePrice * quantity;
    const discount = Math.max(quantity - product.discountThreshold, 0) * product.basePrice * product.discountRate;
    const price = applyShipping(basePrice, shippingMethod, quantity, discount);
    return price;
  }
  
  function applyShipping(basePrice, shippingMethod, quantity, discount) {
     const shippingCase = (basePrice > shippingMethod.discountThreshold) ? shippingMethod.discountedFee: shippingMethod.feePerCase;
    const shippingCost = quantity * shippingPerCase;
    const price = basePrice - discount + shippingCost;
    return price;
  }
  ```
  
  
  
  - 첫 번째 단계와 두 번째 단계가 주고받을 중간 데이터 구조를 만든다.
  
  ```javascript
  function priceOrder(product, quantity, shippinhMethod) {
    const basePrice = product.basePrice * quantity;
    const discount = Math.max(quantity - product.discountThreshold, 0) * product.basePrice * product.discountRate;
    const priceData = {}; // 중간 데이터 구조
    const price = applyShipping(priceData, basePrice, shippingMethod, quantity, discount);
    return price;
  }
  
  function applyShipping(priceData, basePrice, shippingMethod, quantity, discount) {
     const shippingCase = (basePrice > shippingMethod.discountThreshold) ? shippingMethod.discountedFee: shippingMethod.feePerCase;
    const shippingCost = quantity * shippingPerCase;
    const price = basePrice - discount + shippingCost;
    return price;
  }
  ```
  
  - 중간 데이터 구조로 매개변수 옮기고 제거
  
  ```javascript
  function priceOrder(product, quantity, shippinhMethod) {
    const basePrice = product.basePrice * quantity;
    const discount = Math.max(quantity - product.discountThreshold, 0) * product.basePrice * product.discountRate;
    const priceData = {
      basePrice: basePrice,
      quantity: quantity,
      discount: discount,
    }; // 중간 데이터 구조
    const price = applyShipping(priceData, shippingMethod);
    return price;
  }
  
  // shippingMethod를 데이터구조로 옮기지 않는 이유: 1단계에서 사용하지 않고 바로 2단계로 전달
  function applyShipping(priceData, shippingMethod) {
     const shippingCase = (priceData.basePrice > shippingMethod.discountThreshold) ? shippingMethod.discountedFee: shippingMethod.feePerCase;
    const shippingCost = priceData.quantity * shippingPerCase;
    const price = priceData.basePrice - priceData.discount + shippingCost;
    return price;
  }
  ```
  
  - 첫 단계 코드를 함수로 추출
  
  ```javascript
  function priceOrder(product, quantity, shippinhMethod) {
    const priceData = calculatePricingData(product, quantity);
    const price = applyShipping(priceData, shippingMethod);
    return price;
  }
  
  function calculatePricingData(product, quantity) {
    const basePrice = product.basePrice * quantity;
    const discount = Math.max(quantity - product.discountThreshold, 0) * product.basePrice * product.discountRate;
    return {
      basePrice: basePrice,
      quantity: quantity,
      discount: discount,
    }; // 중간 데이터 구조
    
  }
  
  function applyShipping(priceData, shippingMethod) {
     const shippingCase = (priceData.basePrice > shippingMethod.discountThreshold) ? shippingMethod.discountedFee: shippingMethod.feePerCase;
    const shippingCost = priceData.quantity * shippingPerCase;
    const price = priceData.basePrice - priceData.discount + shippingCost;
    return price;
  }
  ```
  
  
