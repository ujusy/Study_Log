# LSP: 리스코프 치환 원칙

### 상속을 사용하도록 가이드하기

```markdown
Blilling -> License (메서드: calcFree())
            <- Personal License
            <- Business License
```

Billing 애플리케이션에서 `calcFree()` 메서드를 호출하는데 `License` 에는 
`Personal License`, `Business License` 라는 두 가지 하위타입이 존재하여 각각 다른 알고리즘으로 비용을 계산한다.
이 때 `Billing`은 하위 타입 중 무엇을 사용하는지 의존하지 않아 두 하위 타입이 모두 `License` 타입을 치환할 수 있다.

### 위배 사례

고객이 이용할 택시를 결정하면, 시스템은 REST 서비스를 통해 선택된 택시를 고객 위치로 파견한다.
시스템이 고객에게 알맞은 기사를 선택하면, 해당 기사의 레코드로부터 URI 정보를 얻은 다음, 그 URI 정보를 이용하여
해당 기사를 고객 위치로 파견.

이 예제에서 분명한 점: 파견 서비스를 만들 때 다양한 택시업체에서 동일한 REST 인터페이스를 반드시 준수해야함. (필드 모두 동일하게 준수)

한 회사가 다른 규칙을 적용한다고 하면 모든 모듈에 if 문을 추가해야함. 계속 if 문을 추가할 수 없음.

# ISP: 인터페이스 분리 원칙

```markdown
user1 ---
         |
user2 -----> ops(op1 + op2 + op3_
         |
user3 ---
```

user1 은 op1, user2는 op2, user3는 op3 만을 사용한다고 가정하면 
user1은 op2, op3을 전혀 사용하지 않음에도 소스코드가 두 메서드에 의존하게 된다. 이런 의존성으로 인해 OPS 클래스에서 op2 코드가
변경되면 user1도 다시 컴파일한 후 새로 배포해야한다.

아래와 같이 분ㄹ리하면 OPS에서 발생한 변경이 use1과는 전혀 관계없는 변경이기 때문에 다시 컴파일, 재배포하지 않아도 된다.

```markdown
user1 -------> u1OPS (op1)
                         |
user2 -------> u2OPS (op2) ---->  ops(op1 + op2 + op3_
                         |         
user3 -------> u3OPS (op3)        
```
