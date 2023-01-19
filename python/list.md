# List

리스트는 **순서를 가지는 객체들의 집합**으로, 파이썬 자료형들 중에서 가장 유용하게 활용된다. 리스트는 [시퀀스 자료형](https://github.com/eastshine-high/til/tree/main/python/sequence-types)이면서 변경 가능(mutable) 자료형**이**다. 따라서 [시퀀스 자료형](https://github.com/eastshine-high/til/tree/main/python/sequence-types)의 특성에 더하여 **가변 시퀀스 타입의 특성(Mutable Sequence Types)**을 가진다. 데이터의 크기를  동적으로 그리고 임의로 조절하거나, 내용을 치환하여 변경할 수 있다.

## 파이썬 리스트의 특징

파이썬의 모든 자료형은 원시 타입이 아닌 객체로 되어있다. 리스트는 객체로 되어 있는 모든 자료형을 포인터로 연결한다. 파이썬은 모든 것이 객체며, 파이썬의 리스트는 객체에 대한 포인터 목록을 관리하는 형태로 구현되어 있다. **사실상 연결 리스트에 대한 포인터 목록을 배열 형태로 관리하고 있으며,** 그 덕분에 파이썬의 리스트는 **배열과 연결 리스트를 합친 듯이 강력한 기능을 자랑**한다.

각 자료형의 크기는 저마다 서로 다르기 때문에 이들을 연속된 메모리 공간에 할당하는 것은 불가능하다. 결국 각각의 객체에 대한 참조로 구현할 수밖에 없다. 당연히 인덱스를 조회하는 데에도 모든 포인터의 위치를 찾아가서 타입 코드를 확인하고 값을 일일이 살펴봐야 하는 등 추가적인 작업이 필요하기 때문에, 속도 면에서도 훨씬 더 불리하다. 이처럼 **파이썬은 강력한 기능을 위해 리스트와 객체에 대한 참조를 택했으며, 이로 인해 부득이하게 속도를 희생한 측면이 있다.**

리스트는 다음과 같이 **선언**할 수 있다.

```python
>>> a = list()

>>> a = []
```

**다른 언어들은 동적 배열에 삽입할 수 있는 자료형을 동일한 타입으로 제한하는 경우가 많은 데 비해, 파이썬은 자유롭게 삽입할 수 있다.** 파이썬의 리스트는 숫자 외에도 다양한 자료형을 단일 리스트에 관리할 수 있어 매우 편리하다.

```python
a = [1,2,3,5,4,'안녕', True]
```

존재하지 않는 인덱스를 조회할 경우 다음과 같이 IndexError가 발생한다. 또 try 구문으로 에러에 대한 예외 처리를 할 수 있다.

```python
try:
    print(a[9])
except IndexError:
    print('존재하지 않는 인덱스')
```

## 가변 시퀀스 타입들의 연산자들(Mutable Sequence Types's operations)

| Operation | Result |
| --- | --- |
| s[i] = x | item i of s is replaced by x |
| s[i:j] = t | slice of s from i to j is replaced by the contents of the iterable t |
| del s[i:j] | same as s[i:j] = [] |
| s[i:j:k] = t | the elements of s[i:j:k] are replaced by those of t |
| del s[i:j:k] | removes the elements of s[i:j:k] from the list |
| s.append(x) | appends x to the end of the sequence (same as s[len(s):len(s)] = [x]) |
| s.clear() | removes all items from s (same as del s[:]) |
| s.copy() | creates a shallow copy of s (same as s[:]) |
| s.extend(t) or s += t | extends s with the contents of t (for the most part the same as s[len(s):len(s)] = t) |
| s *= n | updates s with its contents repeated n times |
| s.insert(i, x) | inserts x into s at the index given by i (same as s[i:i] = [x]) |
| s.pop() or s.pop(i) | retrieves the item at i and also removes it from s |
| s.remove(x) | remove the first item from s where s[i] is equal to x |
| s.reverse() | reverses the items of s in place |

Mutable Sequence Types : [https://docs.python.org/3.9/library/stdtypes.html#mutable-sequence-types](https://docs.python.org/3.9/library/stdtypes.html#mutable-sequence-types)

리스트 활용 예시

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
>>> fruits.reverse()
>>> fruits
['banana', 'apple', 'kiwi', 'banana', 'pear', 'apple', 'orange']
>>> fruits.append('grape')
>>> fruits
['banana', 'apple', 'kiwi', 'banana', 'pear', 'apple', 'orange', 'grape']
>>> fruits.sort()
>>> fruits
['apple', 'apple', 'banana', 'banana', 'grape', 'kiwi', 'orange', 'pear']
>>> fruits.pop()
'pear'
```

## 삭제하기

리스트에서 요소를 삭제하는 기본적인 방법은 2가지가 있다.

- 인덱스로 삭제하기 : `del array[index]`
- 값으로 삭제하기 : `remove(value)`

```python
// del 키워드를 사용하면 인덱스의 위치에 있는 요소를 삭제할 수 있다.
>>> del a[1]
[1, 3, 5, 4, '안녕', True]

//remove() 함수를 사용하면 값에 해당하는 요소를 삭제할 수 있다.
a.remove(3)
[5, 4, '안녕']

// 일부 값을 삭제할 수 있다.
>>> a[0:2] = []
[5, 4, '안녕', True]
```

- `pop()` 함수를 사용하면 스택의 pop 연산처럼 추출로 처리된다. 즉 삭제될 값을 리턴하고 삭제가 진행된다.

```bash
>>> a
[1, 3, 5, '안녕', True]
>>> a.pop(3)
'안녕'
>>> a
[1, 3, 5, True]

# append()와 pop() 메서드를 사용하면 '리스트'를 '큐', '스택'으로 이용할 수 있다.
# 아래는 큐로 활용한 예제이다.
>>> a = [1, 2, 3, 4, 5]
>>> a.append(6)
>>> a
[1, 2, 3, 4, 5, 6]
>>> a.pop(0)
1
>>> a
[2, 3, 4, 5, 6]
```

## 정렬하기

### `sort` 메서드

sort(***, *key=None*, *reverse=False*) 메서드는 리스트 안에서 정렬을 수행한다.

```python
>>> L = [1, 6, 3, 7, 8, 4, 2]
>>> L.sort()
>>> L
[1, 2, 3, 4, 6, 7, 8]
```

만일 역순으로 정렬하기를 원하면 다음과 같이 `reverse` 파라미터를 사용한다.

```python
>>> L.sort(reverse = True)
>>> L
[8, 7, 6, 4, 3, 2, 1]
```

**`key` 파라미터는 비교를 하기 전에 각 리스트 요소에 호출할 함수를 지정한다.** 

다음은 대소문자 구분없이 문자열을 비교하는 예시이다.

```python
>>> sorted("This is a test string from Andrew".split(), key=str.lower)
['a', 'Andrew', 'from', 'is', 'string', 'test', 'This']
```

복잡한 객체를 정렬하는 일반적인 패턴은 객체의 인덱스 중 일부를 키로 사용하여 정렬하는 것이다.

```python
>>> student_tuples = [
...     ('john', 'A', 15),
...     ('jane', 'B', 12),
...     ('dave', 'B', 10),
... ]
>>> sorted(student_tuples, key=lambda student: student[2])   # sort by age
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
```

같은 방법으로 이름 속성이 있는 객체들을 정렬할 수 있다.

```python
>>> class Student:
...     def __init__(self, name, grade, age):
...         self.name = name
...         self.grade = grade
...         self.age = age
...     def __repr__(self):
...         return repr((self.name, self.grade, self.age))

>>> student_objects = [
...     Student('john', 'A', 15),
...     Student('jane', 'B', 12),
...     Student('dave', 'B', 10),
... ]
>>> sorted(student_objects, key=lambda student: student.age)   # sort by age
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
```

### `sorted()` 함수를 사용한 정렬

sorted(*iterable*, key=*key*, reverse=*reverse*)

만일 리스트 내부의 순서는 변경하지 않고 그대로 두고 새로 정렬된 리스트만 원할 때 `sorted()` 내장 함수를 사용할 수 있다. 리스트 정렬에 있어서 초보자가 흔히 범하는 실수는 `sort()`메소드를 사용하여 정렬된 결과를 반환 값으로 얻으려 하는 것이다.

```python
>>> L = [1, 6, 3, 7, 8, 4, 2]
>>> sorted(L)
[1, 2, 3, 4, 6, 7, 8]
>>> sorted(L, reverse = True)
[8, 7, 6, 4, 3, 2, 1]
>>> L = ['33', '120', '55', '300']
>>> sorted(L, key = int)
['33', '55', '120', '300']

>>> d = {'first':1, 'second':2, 'third':3, 'fourth':4}
# sorted() 내장 함수는 사전에도 사용할 수 있다.
>>> for key in sorted(d):
...   print(key)
first
fourth
second
third

# 값을 중심으로 정렬하기
>>> for key in sorted(d, key = lambda a:d[a]):
...   print(key)
first
second
third
fourth
```

### `reversed()` 함수를 사용한 정렬

**리스트를 역순으로 참조**하려 한다면 `reverse()` 메소드를 사용했을 것이다. 하지만 리스트의 순서를 직접 변경시키는 이와 같은 처리 방식은 매우 비효율적이다. `reversed()`는 시퀀스 자료형에서 **직접 역순으로 참조**하는 내장 함수이다. `reversed()` 내장 함수가 반환하려는 객체는 반복자(Iterator)로서 **리스트를 재구성하거나 복사하지 않기 때문에 메모리나 처리 시간의 낭비가 없다.**

```python
>>> L = ['33', '120', '55', '300']
>>> for a in reversed(L):
...   print(a)
300
55
120
33
```

## **Looping Techniques**

**(**인덱스를 통해 값을 확인하는 방법**)**

### 1. `enumerate`

`enumerate` 함수는 시퀀스형 데이터를 (위치, 요솟값) 튜플로 넘겨주는 반복자를 반환한다. 이 함수는 주로 for 문과 연계해서 사용한다(When looping through a sequence, the position index and corresponding value can be retrieved at the same time using the [enumerate()](https://docs.python.org/3/library/functions.html#enumerate) function).

```python
>>> for i, v in enumerate(['tic', 'tac', 'toe']):
...     print(i, v)
...
0 tic
1 tac
2 toe
```

### 2. `range`

순차적인 정수 리스트를 만들 때는 손으로 직접 입력하기 보다는 `range()` 함수를 사용하면 편리하다. 이 함수는 for 문과 같이 사용한다.

```python
def toLinkedList(lst: List) -> ListNode:
    head = ListNode()
    node = head

    for i in range(len(lst)):
        node.val = lst[i]
        node = node.next

    return head
```

- range(x, y)인 경우 x부터 y보다 작은 수까지의 리스트를 1 간격으로 만든다.

```python
def solution(A, B):
    a, b = deque(A), deque(B)
    for cnt in range(0, len(A)):
        if a == b:
            return cnt
        a.rotate(1)
    return -1
```

- range(x, y, z)인 경우 range(x, y)와 같지만 간격을 z으로 한다.

## List Comprehensions(리스트 내장|내포)

리스트 내장(List Comprehensions)은 시퀀스 자료형으로부터 **리스트를 만드는 간결한 방법을 제공**한다.

List comprehensions provide a concise way to create lists. Common applications are to make new lists where each element is the result of some operations applied to each member of another sequence or iterable, or to create a subsequence of those elements that satisfy a certain condition.

리스트 내장을 이용하여 0부터 9까지 수의 제곱의 리스트를 만들어 보자.

```python
>>> L = [k * k for k in range(10)]
>>> print(L)
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

for 문에 의해 리스트로 만들어진 결과를 출력 수식이 반환한다.

- `k * k` : 출력 수식
- `range(10)` : 입력 시퀀스

앞의 코드는 다음과 같이 for 문을 이용하여 리스트를 만드는 것과 같다.

```python
>>> L = []
>>> for k in range(10): 
        L.append(k * k)
>>> L
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

리스트 내장은 다음의 형식을 취한다.

```python
[ <식> for <타깃1> in <객체1>
      for <타깃2> in <객체2>
      ...
      for <타깃N> in <객체N>
      (if <조건식>) ]
```

for ~ in 절은 <객체>의 항목을 반복한다. 여기서 <객체>는 시퀀스형 데이터여야 한다. for 문으로 취해지는 각각의 값은 <식>에서 사용한다. 마지막 if 절은 선택 요소이다. 만일 if 절이 있으면, <식>은 <조건식>이 참일 때만 값을 계산하고 결과에 추가한다.

앞서 설명한 리스트 내장은 다음 파이썬 코드와 동등하다.

```python
for <타깃1> in <객체1>:
    for <타깃2> in <객체2>:
        ...
        for <타깃3> in <객체3>:
            # 식의 값을 결과 리스트에 추가한다.
```

다음 예는 10보다 작은 정수 중 홀수의 제곱만을 반환한다.

```python
>>> L = [k * k for k in range(10) if k % 2 == 1]
>>> L
[1, 9 ,25, 49, 81]
```

다음 예는 2의 배수와 3의 배수 중 두 수의 합이 7의 배수가 되는 두 수와 곱의 리스트를 만들어 낸다.

```python
>>> [(i, j, i * j) for i in range(2, 100, 2)
    for j in range(3, 100, 3)
    if (i + j) % 7 == 0]
[(2, 12, 24), (2, 33, 66), (2, 54, 108), (2, 75, 150), (2, 96, 192), (4, 3, 12)
, (4, 24, 96), (4, 45, 180), ...
```

다음 예는 두 시퀀스 자료형의 모든 데이터의 조합을 만들어 낸다.

```python
>>> seq1 = 'abc'
>>> seq2 = (1, 2, 3)
>>> [(x, y) for x in seq1 for y in seq2]
[('a', 1), ('a', 2), ('a', 3), ('b', 1), ('b', 2), ('b', 3), ('c', 1), ('c', 2), ('c', 3)]
```

리스트 컴프리헨션 응용 문제

J는 보석이며, S는 갖고 있는 돌이다. S에는 보석이 몇 개나 있을까? 대소문자는 구분한다.

입력 : `J = "aA", S = "aAAbbbb"`

출력 : `3`

```python
def numJewelsInStones(J, S):
    return sum([s in J for s in S])
```

**참조**

[https://docs.python.org/3/library/stdtypes.html#sequence-types-list-tuple-range](https://docs.python.org/3/library/stdtypes.html#sequence-types-list-tuple-range)

[https://docs.python.org/3.9/library/stdtypes.html#mutable-sequence-types](https://docs.python.org/3.9/library/stdtypes.html#mutable-sequence-types)

<파이썬3 바이블, 이강성, 프리렉>
