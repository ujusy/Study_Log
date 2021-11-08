# 리팩터링 ch 7.1 - 7.4

## ch 7.1 레코드 캡슐화하기

- 가변 데이터를 저장하는 용도: 레코드 보다 객체
  - 레코드 -> 데이터 클래스
  - 객체를 사용하여 캡슐화하자

1. 레코드를 담은 변수를 캡슐화
2. 레코드를 감싼 단순한 클래스로 해당 변수의 내용을 교체한다. 이 클래스에 원본 레코드를 반환하는 접근자도 정의하고, 변수를 캡슐화하는 함수들이 이 접근자를 사용하도록 수정한다.
3. 원본 레코드 대신 새로 정의한 클래스 타입의 객체를 반환하는 함수들을 새로 만든다.
4. 레코드를 반환하는 예전 함수를 사용하는 코드를 새롭게 만든 함수를 사용하도록 바꾼다. 필드에 접근할 때는 객체의 접근자를 사용한다,

- 중첩된 레코드 캡슐화 예시

  ```javascript
  "soyeon": {
    "age": 12,
    "id": 2,
    "sceret": {
      "weight": 40,
    }
  }
  ```

  ```javascript
  userData[userName].secret[weight] = newWight;
  ```

  - 변수 캡슐화

    ```javascript
    function getRawDataOfUser() {return userData};
    function setRawDataOfUser(arg) {userData = arg};
    ```

  - 클래스 정의

    ```javascript
    class UserData {
      constructor(data) {
        this.data = data;
      }
      
      function getUserData() {return userData;}
      function getRawDataOfUser() {return userData.data};
    	function setRawDataOfUser(arg) {userData = new UserData(arg)};
      
      function setUsage(userName, weigth, newWeight) {
        this.data[userName].secret[weight] = newWeight;
      }
    }
    ```

    ```javascript
    getUserData().setUsage(~); 
    // 모든 쓰임을 명시적인 API로 제공.
    // 해당 클래스만 보면 데이터 사용 방법을 모두 파악 가능
                           -> [???]근데 이 방법이 '모든 쓰기를 함수 안에서 처리한다' 캡슐화의 핵심 원칙이 깨진다고하는데 이해 잘 안간다. 클래스 내부 함수를 이용해서 변경해주는게아닌가..?
    ```

    그래서 또 `lodash`의 `cloneDeep`을 이용하여 깊은 복사해서 사용하기..

    근데 이 방법도 문제가 있다. -> 데이터 구조가 클수록 복제 비용이 커져서 성능이 느려질 수 있따.

    또 다른 방법은 레코드 캡슐화를 재귀적으로하기. 7.2 절이다.... 7.2 절을 참고해야할거같다.

- 음.. 이 챕터 읽고 궁금증 아래와 같다. 읽다보면 해결되겠지?

  - '모든 쓰기를 함수 안에서 처리한다' 캡슐화의 핵심 원칙이 깨진다고하는데 이해 잘 안간다. 클래스 내부 함수를 이용해서 변경해주는게아닌가..?
  - 왜 깊은 복사를해서 데이터 반환해줘야할까.. 

## ch 7.2 컬렉션 캡슐화하기 

컬렉션: list, set, map 등

- 원본을 변경할 수 없도록 컬렉션을 캡슐화한다.

- 컬렙션을 클래스 외부에서 마음대로 수정하게 되면 오류가 발생할 가능성이 높다.

- 변경전

  ```javascript
  class Person {
    get courses() {
      return this._courses;
    }
    set courses(aList) {
      this._courses = aList;
    }
  }
  ```
  
- 변경후

  ```javascript
  class Person {
    get courses() {
      return this._courses.slice();
    }
	addCourse(aCourse) { ... }
  
	removeCourse(aCourse) { ... }
  }
  ```

1. 아직 컬렉션을 캡슐화하지 않았다면 변수 캡슐화하기부터 한다.
2. 컬렉션에 원소를 추가/제거하는 함수르 추가한다.
3. 정적 검사를 수행한다.
4. 컬렉션을 참조하는 부분을 모두 찾는다. 컬렉션의 변경자를 호출하는 코드가 모두 앞에서 추가한 추가/제거 함수를 호출하도록 수정한다. 수정할 때마다 테스트를 진행한다.
5. 컬렉션 게터를 수정해서 원본 내용을 수정할 수 없는 읽기 전용 프락시나 복제본을 반환하게한다.



## 7.3 기본형을 객체로 바꾸기

단순한 정보를 숫자나 문자열 같은 간단한 데이터 항목으로 표현 -> 개발이 진행디면서 간단했던 이 정보들이 더 이상 간단하지 않게 변화

예시] 전화번호를 문자열로 표현했는데 나중에 포맷팅이나 지역 코드 추출 같은 특별한 동작이 필요

- 단순한 출력 이상 기능이 필요 ?
  - 데이터르르 표현하는 전용 클래스를 정의

1. 변수 캡슐화
2. 단순한 값 클래스 생성.  생성자는 기존 값을 인수로 받아서 저장하고, 이 값을 반환하는 게터를 추가.
3. 정적 검사를 수행
4. 값 클래스의 인스턴스를 새로 만들어서 필드에 저장하도록 세터 수정. 
5. 새로 만든 클래스의 게터를 호출한 결과를 반환하도록 게터 수정
6. 함수 이름을 바꾸면 원본 접근자의 동작을 더 잘 드러낼 수 있는지 검토



```javascript
highPriorityCount = orders.filter( o=> "high" === o.priority || "rush" === o.priority).length;

get priorityString {return this._priority.toString();}
set priority {this._priority = new Priority(aString);}

class Priority {
  constructor(value) {this._value = value;}
  toString() {return this._value;}
}
```



```javascript
get priorityString {return this._priority.toString();}
set priority {this._priority = new Priority(aString);}

class Priority {
  constructor(value) {this._value = value;}
  toString() {return this._value;}
}

highPriorityCount = orders.filter( o=> "high" === o.priorityString || "rush" === o.priorityString).length;
```



`priority` 객체를 제공하는 게터를 `order`클래스에 만들자.

```javascript
get priority {return this._priority;}
get priorityString {return this._priority.toString();}
set priority {this._priority = new Priority(aString);}

class Priority {
  constructor(value) {
    if (value instanceof Priority) return value;
    this._value = value;
  }
  toString() {return this._value;}
}

highPriorityCount = orders.filter( o=> "high" === o.priorityString || "rush" === o.priorityString).length;
```



## 7.4 임시 변수를 질의 함수로 바꾸기

```javascript
const basePrice = this._quantity * this._itemPrice;
if (basePrice > 1000) 
  return basePrice * 0.95;
else
  return basePrice * 0.98;
```

임시 변수를 질의 함수로 변경

```javascript
get basePrice() {this._quantity * this._itemPrice;}
...
if (this.basePrice > 1000) 
  return this.basePrice * 0.95;
else
  return this.basePrice * 0.98;
```

- 해당 리팩토링은 클래스 안에서 적용할 때 효과가 가장 크다. 
  - 클래스는 추출한 메서드들에 공유 컨텍스트를 제공하기 때문
- 임시변수를 질의 함수로 변경한다고 항상 좋은 것 아님.
  - 변수에 값을 한 번 대입한 뒤 더 복잡한 코드 덩어리에서 여러 차례 다시 대입하는 경우는 질의 함수를 추출!
  - 스냅숏 용도로 쓰이는 변수는 질의 함수로 적용 안됨.

1. 변수가 사용되기 전에 값이 확실히 결정되는지, 변수를 사용할 때마다 계산 로직이 매번 다른 결과를 내지는 않는지 확인
2. 읽기전용으로 만들 수 잇는 변수를 읽기전용으로 변경
3. 테스트
4. 변수 대입문을 함수로 추출
5. 변수 인라인으로 임시 변수 제거

(함수 만들고 변수 인라인하면된다.)

