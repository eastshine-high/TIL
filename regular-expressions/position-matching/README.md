# 위치 찾기

위치 찾기는 텍스트 문자열 안에서 반드시 일치해야 하는 위치를 지정할 때 사용한다. 위치 찾기가 왜 필요한지 이해하기 위해 다음 예제를 살펴보자.

### Source

The cat scattered his food all over the room.

### Case

Regular Excpression : cat

All matches : The `cat` s`cat`tered his food all over the room.

cat 패턴은 cat이 있는 부분과 모두 일치한다. 심지어 scattered라는 단어 사이에 있는 cat과도 일치한다. 이런 결과를 바랐을 수도 있지만 그렇지 않을 가능성이 더 크다. 만약 cat을 모두 dog로 치환하려고 검색했다면 말도 안 되는 결과를 얻게 된다.

<벤 포터, 손에 잡히는 10분 정규 표현식, 인사이트>
