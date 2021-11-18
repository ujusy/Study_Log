# 리팩터링 ch 8.6 - 8.9

## 8.6 문장 슬라이드하기

- 관련된 코드 가까이 있도록 수정
- 변수는 처음 사용할 때 선언



## 8.7 반복문 쪼개기

종종 반복문 하나에서 두 가지 일을 수행하는 모습을 보게 된다.

이렇게 하나의 반복문에서 여러 일을 처리하는 경우는 실제 개발할때도 많이 사용한다..개인적으로 이렇게 했던 이유는 먼가 성능이 훨씬 잘 나올거 같아서..+ 반복문이 여러개면 시간 복잡도가 늘거같아서,.? 였다. 

하지만 이 책에서는 오히려 반복문 쪼개기가 다른 더 강력한 최적화를 적용할 수 있는 길을 열어주기도하고 오히려 긴 리스트를 반복하더라도 병목으로 이어지는 경우는 매우 드물다고 한다.

한 task에 하나의 반복문을 적용하도록 코드를 작성해보아야겠다 .



1. 반복문을 복제해 두 개로 만든다.
2. 반복문이 중복되어 생기는 부수효과를 파악해서 제거한다.
3. 테스트
4. 각 반복문을 함수로 추출할지 고민해본다.(반복문을 나누었으니 함수로 추출하기에 더욱 용이)



## 8.8 반복문을 파이프라인으로 바꾸기

대부분의 프로그래머들이 객체 컬렉션을 순회할 때 반복문을 사용한다.

컬렉션 파이프라인을 이용하면 처리 과정을 일련의 연산으로 표현할 수 있다. 

- 대표적인 연산은 `map` ,`filter` 이다.
- 논리를 파이프라인으로 표현하면 코드를 이해하기 훨씬 쉬워진다.



1. 반복문에서 사용하는 컬렉션을 가리키는 변수를 하나 만든다.
2. 반복문의 첫 줄부터 시작해서, 각각의 단위 행위를 적절한 컬렉션 파이프라인 연산으로 대체한다.



```javascript
function acquireData {
  const lines = input.split('\n');
  let firstLine = true;
  const result = [];
  for (const line of lines) {
    if (firstLine) {
      firstLine = false;
      continue;
    }
    if (lint.trim() === "") continue;
    const record = line.split(',');
    if (record[1].trim() === "india") {
      result.push({city: record[0].trim(), phone: record[2].trim()});
    }
  }
  
  return result;
}
```



```javascript
function acquireData {
  const lines = input.split('\n');
  const result = lines
  	.slice(1)
  	.filter(line => line.trim() !== "")
  	.map(line => line.split(","))
  	.filter(record => record[1].trim() === "india")
  	.map(record => ({city: record[0].trim(), phone: record[2].trim()}));
  
  return result;
}
```



## 8.9 죽은 코드 제거하기

 코드가 사용되지 않는다면 삭제하자!
