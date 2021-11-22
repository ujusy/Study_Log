# 리팩터링 ch 9.1 - 9.6

## 9.1 변수 쪼개기

- 변수에는 값을 단 한번만 대입
  - 대입이 두 번 이상 이뤄진다면 여러 가지 역할을 수행한다는 신호
    - 변수 쪼개기
    - 왜? 여러 용도로 쓰인 변수는 코드를 읽는 이에제 커다란 혼란을 주기 때문

1. 변수를 선언한 곳과 값을 처음 대입하는 곳에서 변수 이름을 바꾼다.
2. 가능하면 불변으로 선언한다.
3. 이 변수에 두 번째로 값을 대입하는 곳 앞까지의 모든 참조를 새로운 변수 이름으로 변경
4. 두 번째 대입 시 변수를 원래 이름으로 다시 선언 후 테스트
5. 반복



일반적인 재할달은 변수를 쪼개어 불변으로 할당하는 것에 동의한다. 해당 책에는 입력 매개변수의 값을 수정할 때도 소개하고있다. 

매개변수에서 재할당은 종종 많이 사용해왔기 때문에 주의해야겠다.

```javascript
function discount(inputValue, quantity) {
  if (inputValue > 50) inputValue = inputValue - 2;
  if (quantity > 100) inputValue = inputValue -1;
  return inputValue;
}
```

수정 후

```javascript
function discount (inputValue, quantity) {
  let result = inputValue;
	if (inputValue > 50) result = result - 2;
  if (quantity > 100) result = result -1;
  return result;
}
```

​	해당 코드가 입력 값에 기초하여 결괏값을 누적해 계산한다는 사실을 더 명확히 드러내었다.



## 9.2 필드 이름 바꾸기

- 프로그램 곳곳에서 스이는 레코드 구조체의 필드 이름들은 특히 중요
  - 데이터 구조는 프로그램을 이해하는 데 큰 역할을 한다.

1. 레코드의 유효 범위가 제한적이라면 필드에 접근하는 모든 코드를 수정한 후 테스트한다. 이후 단계는 필요 없다.
2. 레코드가 캡슐화되지 않았다면 우선 레코드를 캡슐화한다.
3. 캡슐화된 객체 안의 private 필드명을 변경하고, 그에 맞게 내부 메서드들을 수정한다.
4. 테스트한다.
5. 생성자의 매개변수 중 필드와 이름이 겹치는게 있다면 함수 선언 바꾸기로 변경한다.
6. 접근자들의 이름도 바꿔준다.



```javascript
const organization = {name: "애크미 구스베리", country: "GB"};

name => title로 변경하고싶다. >>  코드 베이스 곳곳에서 사용됨.

1. 먼저 organization 레코드를 클래스로 캡슐화한다.
```



```javascript
class Organization {
  constuctor(data) {
    this._name = data.name;
    this.country = data.country;
  }
  
  get name() {return this._name;}
  set name(aString) {this._name = aString;}
  get country() {return this._country;}
  set country(aCountryCode) {this._country = aCountryCode;}
}

const organization = new Organization({name: "애크미 구스베리", country: "GB"});

변경해야할 부분: 게터 함수, 세터 함수, 생성자, 내부 데이터 구조
```



```javascript
class Organization {
  constuctor(data) {
    this._title = (data.title !== undefined) ? data.title : data.name; 
    // 생성자를 호출하는 쪽에서 name, title 모두 사용 가능 - 생성자를 호출하는 곳을 모두 찾아서 새로운 이름을 사용하도록 변경 -> 모두 수정후 		data.title로 생성자 변경
    this.country = data.country;
  }
  
  get name() {return this._name;}
  set name(aString) {this._name = aString;}
  get country() {return this._country;}
  set country(aCountryCode) {this._country = aCountryCode;}
}
```



```javascript
class Organization {
  constuctor(data) {
    this._title = data.title;
    this.country = data.country;
  }
  
  get title() {return this._title;}
  set title(aString) {this._title = aString;}
  get country() {return this._country;}
  set country(aCountryCode) {this._country = aCountryCode;}
}
```

만약 함수 안에서만 쓰였다면 캡슐화할 필요 없이 원하는 속성 이름만 변경했으면 됐다.

만약 리팩터링 도중 테그트에 실패한다면 더 작은 단계로 나눠 진행해야 한다는 신호이다.



## 9.3 파생 변수를 질의 함수로 바꾸기

- 가변 데이터의 유효 범위를 줄이자 
- 새로운 데이터 구조를 생성하는 변형 연산이라면 계산 코드로 대체할 수 있더라도 그대로 두는 것도 좋다.
  1. 데이터 구조를 감싸며 그 데이터에 기초하여 계산한 결과를 속성으로 제공하는 객체
  2. 데이터 구조를 받아 다른 데이터 구조로 변환해 반환하는 함수



소스 테이터가 가변이고 파생 데이터 구조의 수명을 관리해야 하는 상황에서는 객체를 사용하는 편이 확실히 유리.



1. 변수 값이 갱신되는 지점을 모두 찾는다. 필요하면 변수 쪼개기를 활용해 각 갱신 지점에서 변수를 분리
2. 해당 변수의 값을 계산해주는 함수를 만든다.
3. 해당 변수가 사용되는 모든 곳에 assertion을 추가하여 함수의 계산 결과가 변수의 값과 같은지 확인한다.
4. 테스트
5. 변수를 읽는 코드를 모두 함수 호출로 대체한다.
6. 테스트
7. 변수를 선언하고 갱신하는 코드를 죽은 코드 제거하기로 없앤다.



```javascript
class ProductionClass {
 	get production() {return this._production;}
	applyAdjustment(anAdjustment) {
  	this._adjustments.push(anAdjustment);
  	this._production += anAdjustment.amount; // 관련없는 누적값 계산 
	} 
}
```



```javascript
class ProductionClass {
 	get production() {
    assert(this.production === this.calculatedProduction;) // 누적값 매번 갱신하지 않아도 되는지 확인하기 위한 assertion
    return this._production;
  }
  
  get calculatedProduction() {
    return this._adjustments
    	.reduce((sum, a) => sum + a.amount, 0);
  }
}
```



## 9.4 참조를 값으로 바꾸기

- 객체를 다른 객체에 중첩하면 내부 객체를 참조 혹은 값으로 취급 할 수 있다.
  - 참조로 다루는 경우: 내부 객체를 그대로 둔 채 그 객체의 속성만 갱신
  - 값으로 다루는 경우: 새로운 속성을 담은 객체로 기존 내부 객체를 통째로 대체 
    - 불변이어서 서로 간의 참조를 관리 안해주어도 된다. 
    - 분산 시스템과 동시성 시스템에서 특히 유용

1. 후보 클래스가 불변인지, 혹은 불변이 될 수 있는지 확인한다.
2. 각각의 세터를 하나씩 제거한다.
3. 이 값 객체의 필드들을 사용하는 동치성 비교 메서드를 만든다.



## 9.5 값을 참조로 바꾸기

- 값을 참조로 다루어야하는 경우?
  - 값으로 다룬다면 여러요소에서 사용될 때 복사되고, 참조로 다룬다면 해당 데이터가 사용되는 곳이 단 하나의 데이터 구조를 참조하게 된다.

- 갱신될 일이 있는 데이터라면 참조로 다루어야 한 번 수정이 되었을 때 사용되는 부분 모두를 수정할 필요가 없다. 하지만 값으로 다루는 경우 사용되는 부분을 모두 찾아 수정해주어야한다.

- 값을 참조로 변경한 경우 엔티티 하나당 객체도 단 하나만 존재하게 되는데, 보통 이런 객체들을 한데 모아놓고 클라이언트들의 접근을 관리해주는 일종의 저장소가 필요해진다. 
  - 각 엔티티를 표현하는 객체를 한 번만 만들고, 객체가 필요한 곳에서는 모두 이 저장소로부터 얻어 쓰는 방식



1. 같은 부류에 속하는 객체들을 보관할 저장소를 만든다.
2. 생성자에서 이 부류의 객체들 중 특정 객체를 정확히 찾아내는 방법이 있는지 확인한다.
3. 호스트 객체의 생성자들을 수정하여 필요한 객체를 이 저장소에서 찾도록 한다.



```javascript
class Order {
  constructor(data) {
    this._number = data.number;
    this._customer = new Customer(data.customer); 
    // 고객 id가 1인 주문을 다섯개 생성하면 고객 객체가 다섯개 생성됨
  }
  
  get customer() {return this._customer;}
}

class Cusomter {
  constructor(id) {
    this._id = id;
  }
  
  get id() {return this._id;}
}
```



```javascript
// 저장소 객체
// id 하나당 하나의 고객 객체만 생성됨을 보장
// 주문 클래스의 생성자가 이 저장소를 사용하도록 수정
let _repositoryData;

export function initialize() {
  _repositoryData = {};
  _repositoryData.customers = new Map();
}

export function registerCustomer(id) {
  if(!_repositoryData.customer.has(id)) 
    _repositoryData.customers.set(id, new Customer(id));
  return findCustomer(id);
}

export function findCustomer(id) {
  return _repositoryData.csutomer.get(id);
}

class Order {
  construtor(data) {
    this._number = data.number;
    this._customer = registerCustomer(data.customer);
  }
  ...
}
```



## 9.6 매직 리터럴로 바꾸기

1. 상수를 선언하고 매직 리터럴을 대입힌다.
2. 해당 리터럴이 사용되는 곳을 모두 찾는다.
3. 찾은 곳 각각에서 리터럴이 새 상수와 똑같은 의미로 쓰였는지 확인하며, 같은 의미라면 상수로 대체한 후 테스트한다.
