# 리팩터링 ch 2
Ch2 는 리팩터링 원칙을 담고있는 챕터이다.

리팩터링의 정의, 리팩터링은 언제 해야할지, 하는 이유 등을 담고 있는 챕터이다.



가장 먼저 리팩터링이 무엇인가에 대한 정의를 한다.

`리팩터링` : 소프트웨어의 겉보기 동작은 그대로 유지한 채, 코드를 이해하고 수정하기 쉽도록 내부 구조를 변경하는 것

여기서 주목해야하는 부분은 "겉보기 동작을 그대로 유지하는" 이라고 생각한다.



리팩터링을 한다고하면 해당 로직이 변경이 된다고  생각을 했었는데 해당 책에 

`누군가 리팩터링 하다가 코드가 깨져서 며칠이나 고생했다 라고 한다면, 리팩터링한 것이 아니다.` 라는 문장을 보고 내면의 깨닳음(?)이 있었다. 



일을 할 때 정말 간간히 리팩터링(?)할 때가 있는데 작은 부분을 고치는데도 다시 짜는 느낌을 받을 때가 있었고 실제로 동작이 일치하지 않는 경우도 있었다. (내가 한게 리팩터링이 아니었구나 ......ㅋ껄..껄)



또 리팩터링을 진행하면서 항상 고민인 부분이 성능 문제 였는데 이 부분을 시원하게 긁어주었다.

```text

리팩터링의 목적은 코드를 이해하고 수정하기 쉽게 만드는 것이기 때문에 성능이 좋아질 수도, 나빠질 수도 있다.

대부분의 프로그램은 전체 코드 중 극히 일부에서 대부분의 시간을 소비한다.

의도적으로 성능 최적화에 돌입하기 전가지는 성능에 신경 쓰지 않고 코드를 다루기 쉽게 만드는데 집중한다. 그러다 성능 최적화 단계가 되면 구체적인 절차를 따라 프로그램을 튜닝힌다.

```

결론은 성능 문제의 경우 프로그램의 작은 부분이 큰 영향을 주는 경우가 많으니 일단 프로그램을 잘 리팩토링해 두고 이후에 성능 최적화에 집중하여 문제인 부분을 개선하는 것이 더 효율적라는 것이었다.

리팩토링을 잘 해두면 성능 문제가 있는 부분의 코드 범위를 잡는데 더 좁아질 수 있고 튜닝하기가 쉬워질 것이라고 말하고있다.



해당 내용은 꽤나 공감이 많이 되었다. 실제로 코드를 짜거나 수정을 할 때 성능에 관련해서 생각을 할 때가 많았기 때문에...(실제로 성능 테스트를 해보지 않았음에도 불구하고..ㅎ)

일단 좋은 설계, 좋은 코드를 작성하고 성능에 대한 부분은 성능 최적화 단계에서 진행하는 것이 더욱 더 효율적일 거 같다는 생각에 동감한다.



2.4에서는 언제 리팩터링해야 할지에 대한 내용을 담고있는데 이는 많은 개발자들이 고민하는 부분이라고 생각한다.

나는 개인적으로 새로운 기능을 추가하기가 너무 버거울 때 리팩터링해야한다고 생각했었다.(저렇게 생각하고있는데 지금까지 그러한 규모에서 개발해본적이 없어서 리팩터링을 중요하게 생각안했나 싶다..;;)



해당 챕터에 언제 해야할지에 대해 긴 내용을 담고있는데 결론은 최대한 자주하는 것이 좋다. 인 것 같다.

마틴 파울러는 3의 법칙을 따른다고 한다.

- 처음에는 그냥 한다
- 비슷한 일을 두 번째로 하게 되면, 일단 작업을 계속한다.
- 비슷한 일을 세 번째 하게 되면 리팩터링한다.

이론적으로는 완벽한데 막상 개발하다보면 비슷한 일을 하는 부분을 어떻게 추상화하여 통일해야하는지에 대한 고민을 많이 하게 된다. 실제로 개발하다가 어떻게 추출해야할지 감이 안와서 중복 코드가 발생한 적도 있고.. ,,  (책 다 읽고나면 아키텍처에 대한 공부도 해봐야겠다..)



챕터 2는 이론적인 부분이 많았다. 별거 아닌거 같아보여도 리팩터링에 대해 더 정확히 알게되었고 지금까지 리팩터링은 모든 팀원이 긴 기간을 가지고 진행해야 한다고 생각했었다. 하지만 오히려 시간이 날 때 조금씩 자주 하는것 좋고 전체 팀원이 리팩터링에 집중하는게 회의적이라고 표현이 된 글을 읽으며 리팩터링을 좀 더 깊게 생각할 수 있는 챕터였다. (그리고 아직 초반이여서 그런지 책이 너무 재밌다 ' _ ' )

