# 시퀀스 자료형(Sequence Types)

- 시퀀스 자료형은 여러 객체를 저장하는 자료형이며, 각 객체는 순서를 가진다.
- 각 요소는 인덱스(index)를 사용하여 참조할 수 있다.
- 시퀀스 자료형에 속하는 객체에는 `문자열`, `리스트`, `튜플`이 있다.
- 이들의 특성은 내장 자료형에만 적용되는 것이 아니라, 사용자가 나중에 설계할 시퀀스 클래스 객체들이 가져야 할 특성이기도 하다.

우선 세 가지 자료형에 대한 예를 간단히 들어 보자.

```bash
>>> s = '파이썬만세' # 문자열
>>> L = [100, 200, 300] # 리스트
>>> t = ('튜플', '객체', 1, 2) # 튜플
```

- 문자열은 작은따옴표 `'`나 큰따옴표 `"`로 묶인 문자들의 모임이다.
- 리스트는 대괄호 `[]`안에 둘러 싸인 객체들의 모임이다
- 튜플은 소괄호 `()` 안에 둘러싸인 객체들의 모임이다.

<br>

## 시퀀스 자료형이 가지는 공통적인 연산

시퀀스 자료형이 가지는 공통적인 연산으로는 인덱싱(Indexing), 슬라이싱(Slicing), 연결하기(Concatenation), 반복하기(Repetition), 멤버 검사, 길이 정보 등이 있다.

<br>

### 인덱싱(Indexing)

인덱싱(indexing)은 순서가 있는 데이터에서 오프셋(Offset)으로 하나의 객체를 참조하는 것이다. 오프셋은 정수이며, 0부터 시작한다.

> `[k]` : k번 위치의 값 하나를 취한다.
> 

```bash
>>> s = 'abcdefg'
>>> s[0]
'a'
>>> l = [1, 3, 5]
>>> l[1]
3
>>> l[2]
5
```

<br>

### 슬라이싱(Slicing)

슬라이싱(Slicing)은 시퀀스 자료형의 일정 영역에서 새로운 객체를 반환하며, 결과의 자료형은 원래의 자료형과 같다.

> `[s: t: p]` : s(시작 오프셋, ≤)부터 t(끝 오프셋, 〈) 사이 구간의 값을 p 간격으로 취한다.
> 

```bash
>>> s = 'abcdefg'
>>> s[1:4]
'bcd'
>>> s[1:]
'bcdefg'
>>> s[:]
'abcdefg'
>>> s[::2]
'aceg'
>>> s[-100:100] # 범위를 넘어서면 범위 내의 값으로 자동으로 처리한다.
'abcdefg'

>>> l = [1, 3, 5]
>>> l[:-1] # 맨 오른쪽 값을 제외하고 모두이다.
[1, 3]
```

<br>

### 연결하기(Concatenation)

연결하기(Concatenation)는 `+` 연산자로 같은 시퀀스 자료형인 두 객체를 연결하는 것이다. 일반적으로 두 객체의 자료형이 동일해야 하며, 새로 만들어지는 객체도 같은 자료형이다.  

```bash
>>> s = 'abcd' + 'efgh'
>>> s
'abcdefgh'

>>> a = [1, 3, 5] + [7, 9, 11]
>>> a
[1, 3, 5, 7, 9, 11]
```

<br>

### 반복하기(Repetition)

반복하기(Repetition)는 `*`연산자를 사용하여 시퀀스 자료형인 객체를 여러 번 반복해서 붙이는 것이다. 새로운 객체를 반환한다. 

```bash
>>> a = 'aBc'
>>> a * 3
'aBcaBcaBc'

>>> b = [1, 3, 5]
>>> b * 3
[1, 3, 5, 1, 3, 5, 1, 3, 5]
```

<br>

### 멤버 검사

**어떤 객체가 시퀀스 자료형인 객체에 포함된 것인지 검사하는 것**을 멤버 검사라고 한다. 연산자 `in`을 사용한다. 반환 값은 1은 참, 0을 거짓을 의미한다. 

```bash
>>> '리' in '아리랑'
True

>>> t = (1, 3, 5, 7, 9)
>>> 2 in t
False
>>> 2 not in t
True
>>> 3 in t
True
```

<br>

### 길이 정보

시퀀스형 객체 안에 있는 전체 요소의 개수를 얻기 위해서 `len()` 내장 함수를 사용할 수 있다.

```bash
>>> p = '안녕하세요'
>>> len(p)
5

>>> n = (1, 3, 5, 7)
>>> len(n)
4
```

<br>

### Common Sequence Operations

| Operation | Result |
| --- | --- |
| x in s | True if an item of s is equal to x, else False |
| x not in s | False if an item of s is equal to x, else True |
| s + t | the concatenation of s and t |
| s * n or n * s | equivalent to adding s to itself n times |
| s[i] | ith item of s, origin 0 |
| s[i:j] | slice of s from i to j |
| s[i:j:k] | slice of s from i to j with step k |
| len(s) | length of s |
| min(s) | smallest item of s |
| max(s) | largest item of s |
| s.index(x[, i[, j]]) | index of the first occurrence of x in s (at or after index i and before index j) |
| s.count(x) | total number of occurrences of x in s |
```bash
>>> fruits = ['orange', 'apple', 'pear', 'banana', 'kiwi', 'apple', 'banana']
>>> fruits.count('apple')
2
>>> fruits.count('tangerine')
0
>>> fruits.index('banana')
3
>>> fruits.index('banana', 4)  # Find next banana starting a position 4
6
```
<br>

---

### Reference
https://docs.python.org/3.9/library/stdtypes.html#sequence-types-list-tuple-range

<파이썬3 바이블, 이강성, 프리렉>
