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
