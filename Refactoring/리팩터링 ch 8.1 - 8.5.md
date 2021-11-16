# 리팩터링 ch 8.1 - 8.5

## 8.1 함수 옮기기

좋은 소프트웨어 설계의 핵심은 모듈화가 얼마나 잘 되어 있느냐를 뜻하는 모듈성이다. 

> 모듈성 : 프로그램의 어딘가를 수정하려 할 때 해당 기능과 깊이 관련된 작은 일부만 이해해도 가능하게 해주는 능력.
>
> 모듈성을 높이려면 서로 연관된 요소들을 함께 묶고, 요소 사이의 연결 관계를 쉽게 찾고 이해할 수 있도록 해야한다.

다른 함수 안에서 도우미 역할로 정의된 함수 중 독립적으로도 고유한 가치가 있는 것은 접근하기 더 쉬운 장소로 옮기는게 낫다.

서로 관련된 여러 함수를 묶을 새로운 컨텍스트가 필요해질 때도 많은데 이 경우 여러 함수를 클래스로 묶기나 클래스 추출하기로 해결할 수 있다.



1. 선택한 함수가 현재 컨택스트에서 사용중인 모든 프로그램 요소를 살펴본다. 이 요소들 중에도 함께 옮겨야 할게 있는지 고민해본다.
   - 얽혀 있는 함수가 여러 개라면 다른 곳에 미치는 영향이 적은 함수부터 옮기도록한다.
2. 선택한 함수가 다형 메서드인지 확인한다.
3. 선택한 함수를 타깃 컨텍스트로 복사한다. 
4. 정적 분석 수행한다.
5. 소스 컨텍스트에서 타깃 함수를 참조할 방법을 찾아 반영한다.
6. 소스 함수를 타깃 함수의 위임 함수가 되도록 수정한다.
7. 테스트한다.
8. 소스 함수를 인라인할지 고민해본다.



## 8.2 필드 옮기기

언제해야할까?

함수에 어떤 레코드를 넘길 때마다 또 다른 레코드의 필드도 함께 넘기고 있다면 데이터 위치를 옮겨야한다. 

한 레코드를 변경하려 할 때 다른 레코드의 필드까지 변경해야한다면 필드의 위치가 잘못되었다는 신호이다.



```javascript
class Customer {
  constructor(name, discountRate) {
    this._name = name;
    this._discountRate = discountRate;
    this._contract = new CustomerContract(dateToday());
  }
  
  get discountRate() {return this._discountRate;}
  becomePreferred() {
    this._discountRate += 0.03;
  }
  
  applyDiscount(amount) {
    return amount.subtract(amount.multiply(this._discountRate;))
  }
}

class CumstomerContract {
  constructor(startDate) {
    this._startDate = startDate;
  }
}
```



할인율인 discountRate을 Customer에서 CustomerContract로 옮기고 싶다.

- 필드 캡슐화 + 필드 및 접근자 추가 후 새로운 필드를 사용하도록 수정

  ```javascript
  class Customer {
    constructor(name, discountRate) {
      this._name = name;
      this._contract = new CustomerContract(dateToday());
      this._setDiscountRate(discountRate);
    }
    
    get discountRate() {return this._contract.discountRate;}
    _setDiscountRate(aNumber) {this._contract.discountRate = aNumber;} // 할인률 수정하는 부분을 세터 속성이 아닌 메서드로
    becomePreferred() {
      this._setDiscountRate(this.discountRate + 0.03);
    }
    
    applyDiscount(amount) {
      return amount.subtract(amount.multiply(this.discountRate;))
    }
  }
  
  class CumstomerContract {
    constructor(startDate, discountRate) {
      this._startDate = startDate;
      this._discountRate = discountRate;
    }
    
    get discountRate() {return this._discountRate;}
    set discountRate(arg) {this._discountRate = arg;}
  }
  ```

  



## 8.3 문장을 함수로 옮기기



## 8.4 문장을 호출한 곳으로 옮기기



## 8.5 인라인 코드를 함수 호출로 바꾸기



 
