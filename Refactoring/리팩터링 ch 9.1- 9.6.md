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





## 9.3 파생 변수를 질의 함수로 바꾸기



## 9.4 참조를 값으로 바꾸기



## 9.5 값을 참조로 바꾸기



## 9.6 매직 리터럴로 바꾸기

