# 리팩터링 ch 7.5 - 7.9

## 7.5 클래스 추출하기

##### 언제 & 왜 해야하는가

>클래스: 명확하게 추상화하고 소수의 주어진 역할만 처리해야한다.
>
>but... 실무에서는 몇 가지 연산을 추가 및 데이터를 보강하면서 어느 순간 클래스가 아주 비대해져있는 것을 발견하게된다.
>
>그러면... 메서드와 데이터가 너무 많은 클래스는 이해하기 쉽지 않다.
>
>언제...작은 일부의 기능만을 위해 서브클래스를 만들거나, 확장해야 할 기능이 무엇이냐에 따라 서브 클래스 만드는 방식이 달라질 때



1. 클래스의 역할을 분리할 방법을 정한다.
2. 분리될 역할을 담당할 클래스를 새로 만든다.
3. 원래 클래스의 생성자에서 새로운 클래스의 인스턴스를 생성하여 필드에 저장해둔다.
4. 분리될 역할에 필요한 필드들을 새 클래스로 옮긴다. 
5. 메서드들도 새 클래스로 옮긴다. 이때 다른 메서드를 호출하기보다는 호출을 당하는 일이 많은 메서드들 부터 옮긴다.
6. 양쪽 클래스의 인터페이스를 살펴보면서 불필요한 메서드를 제거하고 이름 변경한다.
7. 새 클래스를 외부로 노출할지 정한다. 노출하려거든 새 클래스에 참조를 값을 바꾸기를 적용할지 고민해본다.



```javascript
class Person {
  get name() {return this._name};
  set name(arg) {this._name = arg;}
  get telephoneNumber() {return ~~;}
  get officeAreaCode() {return this._officeAreaCode;}
  set officeAreaCode(arg) {this._officeAreaCode = arg;}
  get officeNumber() {return this._officeNumber;}
  set officeNumber(arg) {this._officeNumber = arg;}
}
```

변경 후

```javascript
class Person {
  get areaCode() {return this._areaCode;}
  set areaCode(arg) {this._areaCode = arg;}
  get number() {return this._number;}
  set number(arg) {this._number = arg;}
  
  get telephoneNumber() {return this._telephoneNumber.toString();}
}

class TelephoneNumber {
  get officeAreaCode() {return this._telephoneNumber.areaCode;}
  set officeAreaCode(arg) {this._telephoneNumber.areaCode = arg;}
  get officeNumber() {return this._telephoneNumber.number;}
  set officeNumber(arg) {this._telephoneNumber.number = arg;}
  toString() {
    return ~;
  }
  get telephoneNumber() {return this._telephoneNumber.toString();}
}
```



## 7.6 클래스 인라인하기

7.5의 반대 리팩토링 기법이다.

##### 언제 & 왜 해야하는가

>제 역할을 못 해서 그대로 두면 안 되는 클래스를 인라인. 



## 7.7 위임 숨기기

>캡슐화?
>
>- 필드 숨기기
>- 서버 객체의 필드가 가리키는 객체의 메서드를 호출하려면 클라이언트는 이 위임 객체를 알아야한다. 이 경우 위임 객체의 인터페이스가 바뀌면 이 인터페이스를 사용하는 모든 클라이언트가 코드를 수정해야한다. 이러한 의존성을 없애려면 서버 자체에 위임 메서드를 만들어서 위임 객체의 존재를 숨기면 된다. 
>  - 그러면 위임 객체가 수정되더라도 서버 코드만 고치면 되며, 클라이언트는 아무런 영향을 받지 않는다.



1. 위임 객체의 각 메서드에 해당하는 위임 메서드를 서버에 생성한다.
2. 클라이언트가 위임 객체 대신 서버를 호출하도록 수정한다. 하나 씩 바꿀 때마다 테스트한다.
3. 모두 수정했다면, 서버로부터 위임 객체를 얻는 접근자를 제거한다.



수정 전

```javascript
class Person {
  constructor(name) {
    this._name = name;
  }
  get name() {return this._name;}
  get department() {return this._department;}
  set department(arg) {this._department = arg;}
}

class Department {
  get chargeCode() {return this._chargeCode;}
  set chargeCode(arg) {this._chargeCode = arg;}
  get manager() {return this._manager;}
  set manager(arg) {this._manager = arg;}
}

// client
manager = aPerson.department.manager;
```

수정 후

```javascript
class Person {
  constructor(name) {
    this._name = name;
  }
  get name() {return this._name;}
  get department() {return this._department;}
  set department(arg) {this._department = arg;}
  
  get manager() {return this._department.manager;}
}

class Department {
  get chargeCode() {return this._chargeCode;}
  set chargeCode(arg) {this._chargeCode = arg;}
  get manager() {return this._manager;}
  set manager(arg) {this._manager = arg;}
}

// client
manager = aPerson.manager;
```



## 7.8 중개자 제거하기

7.7의 반대이다. 

클라이언트가 위임 객체의 또 다른 기능을 사용하고 싶을 때마다 위임 메서드를 추가해야 하는데, 이렇게 기능을 추가하다 보면 단순히 전달만 하는 위임 메서드들이 점점 많아진다. 

그러면..? 서버 클래스가 그저 중개자 역할로 전락하여 차라리 클라이언트가 위임 객체를 직접 호출하는 것이 나을 수 있다.



++ this._departmnet.manager; vs this.department.manager : '__' 을 빼면 더 이상 필드를 직접 접근하지 않고 새로 만든 게터를 사용한다는 뜻



## 7.9 알고리즘 교체하기

1. 교체할 코드를 함수 하나에 모은다.

2. 이 함수만을 이용해 동작을 검증하는 테스트를 마련한다.

3. 대체할 알고리즘을 준비한다.

4. 정적 검사를 수행한다.

5. 기존 알고리즘과 새 알고리즘의 결과를 비교하는 테스트를 수행한다.

   
