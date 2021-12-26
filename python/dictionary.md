# Dictionary

- 사전은 임의 객체의 집합적 자료형인데, 데이터의 저장 순서가 없다. **집합적이라는 의미에서 리스트나 튜플과 동일한다. 하지만, 데이터의 순서를 정할 수 없는 매핑형**이다. 시퀀스 자료형은 데이터의 순서를 정할 수 있어서 정수형 오프셋에 의한 인덱싱이 가능하지만, **매핑형에서는 키(key)를 이용해 값(value)에 접근한다.**
- 사전은 변경 가능한 자료형으로 값을 저장할 때도 키를 사용한다. 키가 사전에 등록되어 있지 않으면 새로운 항목이 만들어지며, 키가 이미 사전에 등록되어 있으면 이 키에 해당하는 값이 변경된다.
- 사전을 출력하면 당연히 어떤 순서에 의해서 입력 값들이 표현된다. 그러나 이런 순서는 고정된 것이 아니다.(3.7 버전 부터는 LIFO 순서가 보장된다) 키에 의한 검색 속도를 빠르게 하기 위해 사전은 내부적으로 해시 기법을 이용하여 데이터를 저장한다. 이 기법은 데이터의 크기가 증가해도 빠른 속도로 데이터를 찾을 수 있게 해준다.

<br>

## Dictionary의 생성

- Dictionary는 중괄호 안에 쉼표로 구분된 `키:값` 쌍 목록을 배치하여 만들 수 있다.
- 또는 dict 생성자에 의해 만들 수 있다.
- 값은 임의의 객체가 될 수 있지만
- 키는 해시 가능하고 변경 불가능한 자료형이어야 한다. 예를 들어 문자열과 숫자, 튜플은 키가 될 수 있지만, 리스트와 사전은 키가 될 수 없다.

```bash
>>> a = dict(one=1, two=2, three=3)
>>> b = {'one': 1, 'two': 2, 'three': 3}
>>> c = dict(zip(['one', 'two', 'three'], [1, 2, 3]))
>>> d = dict([('two', 2), ('one', 1), ('three', 3)])
>>> e = dict({'three': 3, 'one': 1, 'two': 2})
>>> f = dict({'one': 1, 'three': 3}, two=2)
>>> a == b == c == d == e == f
True
```

[https://docs.python.org/3.10/library/stdtypes.html#mapping-types-dict](https://docs.python.org/3.10/library/stdtypes.html#mapping-types-dict)

<br>

## 사전의 뷰(Dictionary view objects)

`[dict.keys()](https://docs.python.org/3.10/library/stdtypes.html#dict.keys)`, `[dict.values()](https://docs.python.org/3.10/library/stdtypes.html#dict.values)`, `[dict.items()](https://docs.python.org/3.10/library/stdtypes.html#dict.items)` 에 의해 반환되는 객체들은 뷰 객체들인다. 여기서 뷰란 사전 항목들을 동적으로 볼 수 있는 객체이다. 동적이란 의미는 사전의 내용이 바뀌어도 뷰를 통해서 내용의 변화를 읽어 낼 수 있다는 의미이다.

An example of dictionary view usage

```bash
>>> keys = dishes.keys()
>>> values = dishes.values()

>>> # iteration
>>> n = 0
>>> for val in values:
...     n += val
>>> print(n)
504

>>> # keys and values are iterated over in the same order (insertion order)
>>> list(keys)
['eggs', 'sausage', 'bacon', 'spam']
>>> list(values)
[2, 1, 1, 500]

>>> # view objects are dynamic and reflect dict changes
>>> del dishes['eggs']
>>> del dishes['sausage']
>>> list(keys)
['bacon', 'spam']

>>> # set operations
>>> keys & {'eggs', 'bacon', 'salad'}
{'bacon'}
>>> keys ^ {'sausage', 'juice'}
{'juice', 'sausage', 'bacon', 'spam'}

>>> # get back a read-only proxy for the original dictionary
>>> values.mapping
mappingproxy({'eggs': 2, 'sausage': 1, 'bacon': 1, 'spam': 500})
>>> values.mapping['spam']
500

>>> # items
>>> knights = {'gallahad': 'the pure', 'robin': 'the brave'}
>>> for k, v in knights.items():
...     print(k, v)
...
gallahad the pure
robin the brave
```

[https://docs.python.org/3.10/library/stdtypes.html#dictionary-view-objects](https://docs.python.org/3.10/library/stdtypes.html#dictionary-view-objects)

키와  항목에 대한 사전 뷰들은 항목들이 유일하고 중복되지 않는다는 점에서 집합과 유사한 특성이 있다. 사전의 뷰 객체들은 집합 연산을 허용한다.

<br>

## 사전의 메서드

파이썬 docs([https://docs.python.org/3.10/library/stdtypes.html#mapping-types-dict](https://docs.python.org/3.10/library/stdtypes.html#mapping-types-dict))를 참조하자.

<br>

## 사전 내장(Dictionary Comprehension)

리스트 내장과 유사한 방식으로 동작하지만 사전을 만들어 낸다. 사전 내장은 중괄호 `{}`를 사용하며 `키:값` 형식으로 항목을 표현한다.

```bash
>>> {w:1 for w in 'abc'}
{'a': 1, 'b': 1, 'c': 1}

>>> a1 = 'cdef'
>>> a2 = (3,4,5,6)
>>> {x:y for x,y in zip(a1, a2)}
{'c': 3, 'd': 4, 'e': 5, 'f': 6}

>>> {w:k for k, w in [(1, 'one'), (2, 'two'), (3, 'three')]}
{'one': 1, 'two': 2, 'three': 3}
>>> {w:k+1 for k, w in enumerate(['one', 'two', 'three'])}
{'one': 1, 'two': 2, 'three': 3}
```

`enumerate()` 함수는 시퀀스형 데이터를 (위치, 요소값) 튜플로 넘겨주는 반복자를 반환한다. 이 함수는 주로 for 문과 연계해서 사용한다.

```bash
>>> for k, ele in enumerate(['일', '이', '삼']): print(k, ele)
0 일
1 이
2 삼
```

다음 예는 사전 내장을 이용하여 어떤 사전의 값들을 키로, 또 키들을 값으로 하는 사전을 만든다.

```bash
>>> c = {'one':1, 'two':2, 'three':3}
>>> {v:k for k,v in c.items()}
{1: 'one', 2: 'two', 3: 'three'}
```

다중 for 문을 이용한 사전 내장의 예이다.

```bash
>>> {(k, v): k + v for k in range(3) for v in range(3)}
{(0, 0): 0, (0, 1): 1, (0, 2): 2, (1, 0): 1, (1, 1): 2, (1, 2): 3, (2, 0): 2, 
(2, 1): 3, (2, 2): 4}
```

<br>

### Reference

[https://docs.python.org/3/tutorial/datastructures.html#dictionaries](https://docs.python.org/3/tutorial/datastructures.html#dictionaries)

[https://docs.python.org/3/tutorial/datastructures.html#looping-techniques](https://docs.python.org/3/tutorial/datastructures.html#looping-techniques)

<파이썬3 바이블, 이강성, 프리렉>
