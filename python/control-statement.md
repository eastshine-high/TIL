# 제어문(Control Statement)

다른 언어에 비해서 파이썬은 제어문이 간결하다. **파이썬은 크게 세 개의 제어문밖에는 없다. `if` 문과 `for` 문, `while` 문이 그것이다.**

<br>

## if 문

### if문을 조건문이라고 한다. 일반적인 형식은 다음과 같다.

```python
if <조건식1>:
    <문1>
elif <조건식2>:
    <문2>
else:
    <문3>
```

<br>

### 연습

```python
a = 5
if a > 5 :
    print("hi")
else :
    print("hi2")
결과
hi2

# if 블록 내의 문이 1개만 있을 때는 한 줄에 붙여서 사용할 수 있다.
if False : print("hi")
else : print("hi2")

결과
hi2
```

<br>

## for 문

### for문을 반복문이라고 한다. 일반적인 형식은 다음과 같다.

```python
for <타깃> in <객체>:
    <문1>
else:
    <문>
```

<객체>는 시퀀스형 데이터여야 한다. <객체>의 각 항목은 <타깃>에 치환되어 <문1>이나 <문2>를 실행한다. 반복 횟수는 <객체>의 크기가 된다. for문의 내부 블록에서 continue를 만나면 시작 부분으로 이동하고 break를 만나면 for문의 내부 블록을 완전히 빠져나온다. for 문에서 else 블록은 for 문의 break문으로 중단되지 않고 종료되었을 때 실행된다. break문으로 중단되면 for문의 내부 블록 밖으로 제어가 이동한다.

<br>

### 순차적으로 숫자를 반복하는 경우에는 `range()` 함수를 사용한다.

파이썬 내장 함수 range는 정수 수열을 생성하는 객체를 만든다. range(stop)처럼 인수 하나를 넘겨줄 때 수열은 0부터 시작해서 stop바로 전 정수까지이다.

```python
>>>range(10)
range(0, 10)
```

파이썬의 **range 타입**은 루프를 사용해 수열 내 각 수를 한 번에 하나씩 접근할 수 있다.

```python
for num in range(10):
    print(num, end='')

실행결과
0123456789
```

수열 내 모든 수를 한 번에 가져오고 싶을 때 내장 함수인 list를 사용하면 수 리스트를 생성할 수 있다.

```python
>>> list(range(10))
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9,]
```

<br>

## while 문

for 문보다 일반적인 반복문이다. 시작 부분의 <조건식>을 검사해서 결과가 참(True)인 동안 내부 블록이 반복적으로 실행된다. 다음은 일반적인 형식이다. else 블록은 <조건식>이 거짓(False)이 되어 while 문을 빠져나올 때 실행된다. break로 빠져나올 때는 else 블록을 실행하지 않는다.

```python
while <조건식>:
    <문1>
else:
    <문2>
```

다음은 1부터 9를 새는 코드이다. while문도 for문에서와 같이 break와 continue, else를 사용할 수 있다.

```python
a = 0
while a < 10:
    a = a + 1
    if a < 1 : continue
    if a >= 10 : break
    print(a, end='')
else:
    print('else block')
print('done')

실행결과
123456789done
```

<br>
<hr>

### Reference
<파이썬3 바이블, 이강성, 프리렉>
