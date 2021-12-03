# 알고리즘

알고리즘은 **어떠한 문제를 해결하기 위한 절차**를 말한다.

<br>

### 알고리즘은 다음의 조건을 만족해야 한다.

- 입력(input) : 외부에서 제공되는 자료가 0개 이상 있어야 한다.
- 출력(output) : 적어도 1개 이상의 결과를 만들어야 한다.
- 명확성(definiteness) : 각 명령은 모호하지 않고 단순 명확해야 한다.
- 유한성(finiteness) : 한정된 수의 단계를 거친 후에는 반드시 종료해야 한다.
- 유효성(effectiveness) : 모든 명령들은 실행 가능해야 한다. 예를 들어 0으로 나누는 연산과 같이 실행할 수 없는 연산을 포함해서는 안 된다.

<br>

### 알고리즘의 성능 분석

알고리즘 분석 기준으로 가장 대표적인 것은 공간 복잡도(Space Complexity)와 시간 복잡도(Time Complexity)가 있다. 보통 알고리즘의 성능 분석에서 시간 복잡도가 공간 복잡도보다 더 중요한 평가 기준이기 때문에 알고리즘의 성능 분석은 대부분 시간 복잡도를 대상으로 한다.

- 공간 복잡도(Space Complexity) : 알고리즘 실행에 필요한 저장 공간(메모리 혹은 디스크)이 얼마만큼 필요한가.
- 시간 복잡도(Time Complexity) : 입력 값에 따른 실행 연산의 빈도수.

<br>

### 점근적 실행 시간(Asymptotic Running Time)

알고리즘의 수행 시간은 입력의 크기 n이 커질수록 증가하며, 한편으로는 그에 따라 각 알고리즘의 성능 차이가 확연하게 나타난다. 따라서 **입력 크기 n이 작은 경우가 아닌 데이터의 양이 무한히 많아지는 상황을 전제로 알고리즘의 논리구조를 파악하여 알고리즘의 성능을 표현**하는 것이 바람직하다.

점근 성능을 표기하는 대표적인 방법으로 `Big-omega`, `Big-theta`, `Big-oh`가 있다. 복잡한 함수 f(n)이 있을 경우, 이 함수의 실행 상한(Upper-Bound)과 하한(Lower-Bound)을 의미한다.

- Big-oh O(점근적 상한) : 알고리즘이 가장 늦게 실행될 때이다.  <!-- $f(n) \le c\cdot g(n)$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=f(n)%20%5Cle%20c%5Ccdot%20g(n)">
- Big-omega Ω(점근적 하한) : 알고리즘이 가장 빨리 실행될 때이다. <!-- $f(n) \ge c\cdot g(n)$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=f(n)%20%5Cge%20c%5Ccdot%20g(n)">
- Big-theta Θ(점근적 상-하한) : 알고리즘이 평균적으로 실행될 때이다. <!-- $C1\cdot g(n) \le f(n) \le C2\cdot g(n)$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=C1%5Ccdot%20g(n)%20%5Cle%20f(n)%20%5Cle%20C2%5Ccdot%20g(n)">

**상한과 최악**

한 가지 중요한 점은 상한(Upper-Bound)을 최악의 경우와 혼동하는 것인데, 빅오 표기법은 정확하게 쓰기에는 너무 길고 복잡한 함수를 '적당히 정확하게' 표현하는 방법일 뿐, 최악의 경우/평균적인 경우와 시간 복잡도와는 아무런 관계가 없는 개념이라는 점에 유의해야 한다.

빅오 표기는 복잡한 함수 f(n)이 있을 경우, 이 함수의 실행 상한(Upper-Bound)과 하한(Lower-Bound)을 의미한다. 즉, 빅오 표기법은 주어진(최선/최악/평균) 경우의 수행 시간의 상한을 나타낸다.

<br>

### 빅오(O)

빅오는 점근적 실행 시간(Asymptotic Running Time)을 표기할 때 가장 널리 쓰이는 수학적 표기법 중 하나다. 빅오 표기법의 종류는 크게 다음과 같다.

- <!-- $O(1)$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=O(1)"> : 입력값이 아무리 커도 실행 시간은 일정하다. 최고의 알고리즘이라 할 수 있다. 상수 시간을 갖는 알고리즘은 성배와 같아서 찾을 수만 있다면 놀라울 정도로 가치가 있지만, 그 성배를 찾기 우해 일평생을 보내야 할지도 모른다. 또한 상수 기간에 실행된다 해도 상수값이 상상을 넘어설 정도록 매우 크다면 사실상 일정한 시간의 의미가 없다.
- <!-- $O(\log{n})$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=O(%5Clog%7Bn%7D)"> : 여기서부터 실행 시간은 입력값에 영향을 받는다. 그러나 **로그는 매우 큰 입력값에도 크게 영향을 받지 않는 편**으로 웬만한 n의 크기에 대해서도 매우 견고하다. 대표적으로 이진 검색이 이에 해당한다.
- <!-- $O(n)$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=O(n)"> : 입력값만큼 실행 시간에 영향을 받으며, 알고리즘을 수행하는 데 걸리는 시간은 입력값에 비례한다. 이러한 알고리즘은 선형 시간(Linear-Time)알고리즘이라고 한다. 정렬되지 않은 리스트에서 최댓값 또는 최솟값을 찾는 경우가 이에 해당하며 이 값을 찾기 위해서는 **모든 입력값을 적어도 한 번이상은 살펴봐야 한다**.
- <!-- $O(n\log{n})$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=O(n%5Clog%7Bn%7D)"> : 병합 정렬을 비롯한 대부분의 효율 좋은 정렬 알고리즘이 이에 해당한다. 적어도 모든 수에 대해 한 번 이상은 비교해야 하는 **비교 기반 정렬 알고리즘은 아무리 좋은 알고리즘도 <!-- $O(n\log{n})$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=O(n%5Clog%7Bn%7D)">보다 빠를 수 없다**. 물론 입력값이 최선인 경우, 비교를 건너뛰어 <!-- $O(n)$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=O(n)">이 될 수 있으며 팀소트(Timsort)가 이런 로직을 갖고 있다.
- <!-- $O(n^2)$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=O(n%5E2)"> : 버블 정렬 같은 비효율적인 정렬 알고리즘이 이에 해당한다.
- <!-- $O(2^n)$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=O(2%5En)"> : 피보나치 수를 재귀로 계산하는 알고리즘이 이에 해당한다. 간혹 <!-- $n^2$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=n%5E2">와 혼동하는 경우가 있는데 처음에는 비슷해 보이지만 <!-- $2^n$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=2%5En">이 훨씬 더 크다. 몇 번만 계산해봐도 금방 알 수 있다.
- <!-- $O(n!)$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=O(n!)"> : 각 도시를 방문하고 돌아오는 가장 짧은 경로를 찾는 외판원 문제(Traveling Salesman Problem)를 브루트 포스로 풀이할 때가 이에 해당한다. 가장 느린 알고리즘으로, 입력값이 조금만 커져도 웬만한 다항시간 내에는 계산이 어렵다. n=100만 되어도 n!은 93326215443944152681699238856266700490715968264381621468592963895217599993229915608941463976156518286253697920827223758251185210916864000000000000000000000000이 된다.

빅오는 시간 복잡도 외에도 공간 복잡도를 표현하는 데에도 널리 쓰인다. 또한 알고리즘은 흔히 '시간과 공간이 트레이드오프(Space-Time Tradeoff) 관계다. 이 말은 실행 시간이 빠른 알고리즘은 공간을 많이 사용하고, 공간을 적게 차지하는 알고리즘은 실행 시간이 느리다는 얘기다.

<br>

### 분할 상환 분석(Amortized Analysis)

분할 상환 분석(Amortized Analysis)는 빅오와 함께 함수의 동작을 설명할 대 중요한 분석 방법 중 하나다. 분할 상환 분석이 유용한 대표적인 예로 '동적 배열'을 들 수 있는데, 동적 배열에서 더블링이 일어나는 일은 어쩌다 한 번뿐이지만, 이로 인해 '아이템 삽입 시 시간 복잡도는 O(n)이다.'라고 얘기하는 건 지나치게 비판적이고 정확하지도 않다. 따라서 이 경우 **'분할 상환' 또는 상각'이라고 표현하는, 최악의 경우를 여러 번에 걸쳐 골고루 나눠주는 형태**로 알고리즘의 시간 복잡도를 계산할 수 있다. 이렇게 할 경우 동적 배열의 삽입 시간 복잡도는 O(1)이 된다. 이 방법은 1985년 미국의 수학자이자 컴퓨터과학자인 로버트 타잔이 '상각된 계산 복잡도(Amortized Computational Complexity'라는 논문에서 처음으로 소개했으며, 그 유용함 덕분에 최근에는 시간 복잡도를 분설할 때 매우 보편적으로 널리 사용되는 방법이기도 하다.

<br>

### 병렬화

일부 알고리즘들은 병렬화로 실행 속도를 높일 수 있다. 최근에는 딥러닝의 인기와 함께 병렬화가 큰 주목을 받고 있으며 GPU는 병렬 연산을 위한 대표적인 장치이기도 하다. GPU 각각의 코어는 CPU보다 훨씬 더 느리지만 GPU의 코어는 수천여 개로 구성되어 있어, 많아 봐야 수십여 개에 불과한 CPU보다 수백 배 더 많은 연산을 동시에 수행할 수 있다. 예를 들어 CPU가 비행기로 짐을 여러 번 나르는 것이라면, GPU는 배로 수많은 짐을 한 번에 나르는 것에 비유할 수 있다. 각 배의 운반 속도는 훨씬 느리지만 비행기보다 더 많은 짐을 동시에 나를 수 있는 것처럼, GPU는 결국 같은 시간에 목적지에 훨씬 더 많은 짐을 나를 수 있다. 이처럼 딥러닝 알고리즘을 비롯해 **병렬 연산이 가능한 알고리즘들은 최근에 큰 주목을 받고 있다. 알고리즘 자체의 시간 복잡도 외에도 알고리즘이 병렬화가 가능한지는 근래에 알고리즘의 우수성을 평가하는 매우 중요한 척도 중 하나이기도 하다.**

<br>

### Reference
<파이썬 알고리즘 인터뷰, 책만, 박상길>