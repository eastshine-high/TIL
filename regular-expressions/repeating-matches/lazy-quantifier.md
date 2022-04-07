## 과하게 일치하는 상황 방지하기

별표(`*`)와 더하기(`+`) 같은 메타 문자는 **탐욕적(greedy**)이므로 이는 가능한 한 가장 큰 덩어리를 찾으려 한다는 뜻이다. 이런 메타 문자는 **찾으려는 텍스트를 앞에서부터 찾는 게 아니라, 텍스트 마지막에서 시작해 거꾸로 찾는다**. 의도적으로 수량자(quantifier)를 탐욕적으로 설계했기 때문이다.

하지만 만약 우리가 탐욕적 일치를 원하지 않는다면 어떻게 해야 할까? 탐욕적 수량자를 **게으른(lazy)** 수량자로 바꿔 이 문제를 해결한다. **‘게으른'이라고 부르는 이유는 문자가 최소로 일치하기 때문**이다. 게으른 수량자는 기존 수량자 뒤에 물음표(?)를 붙여서 표현한다.

### Case 7(greedy)

Regular Expression : <[Bb]>.*<\/[Bb]>

all match : This offer is not available to customers living in `<b>AK</b> and <b>HI</b>`.

### Case 8(lazy)

Regular Expression : <[Bb]>.*?<\/[Bb]>

all match : This offer is not available to customers living in `<b>AK</b>` and `<b>HI</b>`.

### Reference

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_17](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_17)

<벤 포터, 손에 잡히는 10분 정규 표현식, 인사이트>
