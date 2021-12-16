# Float-Point(실수형의 저장방식)

실수형은 값을 부동소수점수(Float-Point)의 형태로 저장한다. 부동소수점수는 실수를 <!-- $\pm M \times 10^E$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=%5Cpm%20M%20%5Ctimes%2010%5EE">와 같은 형태로 표현하는 것을 말하며, 부동소수점수는 부호(Sign), 지수(Exponent), 가수(Mantissa), 모두 세부분으로 이루어져 있다.

<!-- $`\pm M \times 10^E`$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=%60%5Cpm%20M%20%5Ctimes%2010%5EE%60">

### 부호(Sign bit)

'S'는 부호비트를 의미하며 1 bit이다. 이 **값이 0이면 양수**를, **1이면 음수**를 의미한다. 정수형과 달리 '2의 보수법'을 사용하지 않기 때문에 양의 실수를 음의 실수로 바꾸려면 그저 부호비트만 0에서 1로 변경하면 된다.

### 지수(Exponent)

'E'는 지수를 저장하는 부분으로 float의 경우, Java에서는 8 bit의 저장공간을 갖는다. 지수는 '부호 있는 정수'이고 8 bit로는 <!-- $2^8$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=2%5E8">(=256)개의 값을 저장할 수 있으므로, '-127~128'의 값이 저장된다. 이 중에서 127과 128은 '숫자 아님(NaN Not a Number)'이나 '양의 무한대(POSITIVE_INFINITY)', 음의 무한대(NEGATIVE_INFINITY)'와 같이 특별한 값의 표현을 위해 예약되어 있으므로 실제로 사용가능한 지수의 범위는 '-126~127'이다. 그래서 지수의 최대값이 127이므로 float타입으로 표현할 수 있는 최대값은 <!-- $2^{127}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=2%5E%7B127%7D">이고, 10진수로 <!-- $10^{38}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=10%5E%7B38%7D">이다. 그러나 float의 최소값은 가수의 마지막 자리가 <!-- $2^{-23}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=2%5E%7B-23%7D">이므로 지수의 최소값보다 <!-- $2^{23}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=2%5E%7B23%7D">배나 더 작은 값, 약 <!-- $10^{-45}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=10%5E%7B-45%7D">이다.

### 가수(Mantissa)

'M'은 실제 값인 가수를 저장하는 부분으로 float의 경우, 2진수 23자리를 저장할 수 있다. 2진수 23자리로는 약 7자리의 10진수를 저장할 수 있는데 이것이 바로 float의 정밀도가 된다. double은 가수를 저장할 수 있는 공간이 52자리로 float보다 약 2배이므로 double이 float보다 약 2배의 정밀도를 갖는 것이다.

### 부동소수점의 오차

실수 중에는 파이( )와 같은 무한소수가 존재하므로, 정수와 달리 실수를 저장할 때는 오차가 발생할 수 있다. 게다가 10진수가 아닌 2진수로 저장하기 때문에 10진수로는 유한소수이더라도, 2진수로 변환하면 무한소수가 되는 경우도 있다. 2진수로는 10진 소수를 정확히 표현하기 어렵기 때문이다.

`9.1234567` = `1001.000111111001101011011011...`

비록 2진수로 유한소수라도, 가수를 저장할 수 있는 자리수가 한정되어 있으므로 저장되지 못하고 버려지는 값들이 있으면 오차가 발생한다.

2진수로 변환된 실수를 저장할 때는 먼저 '1.xxx' <!-- $\times 2^{n}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=%5Ctimes%202%5E%7Bn%7D">의 형태로 변환하는데, 이 과정을 정규화라고 한다.

`1001.000111111001101011011011...` → `1.001000111111001101011011011...` <!-- $\times 2^3$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=%5Ctimes%202%5E3">

정규화된 2진 실수는 항상 '1.'으로 시작하기 대문에, '1.'을 제외한 23자리의 2진수가 가수(mantissa)로 저장되고 그 이후는 잘려나간다. 지수는 기저법으로 저장되기 때문에, 지수인 3에 기저인 127을 더한 130이 2진수로 변환되어 저장된다. 10진수 130은 2진수로 '10000010'이다. 

이 때 잘려나간 값들에 의해 발생할 수 있는 최대오차는 약 <!-- $2^{-23}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=2%5E%7B-23%7D">인데, 이 값은 가수의 마지막 비트의 단위와 같다. <!-- $2^{-23}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=2%5E%7B-23%7D">은 10진수로 0.0000001192(약 <!-- $10^{-7}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=10%5E%7B-7%7D">)이므로 float의 정밀도가 7자리라고 하는 것이다. 또는 '소수점이하 6자리'라고 표현할 수도 있다.

### 기저법

기저법은 '2의 보수법'처럼 부호있는 정수를 저장하는 방법이다. 저장할 때 특정값(기저)을 더했다가 읽어올때는 다시 뺀다.

---

### Reference

Java의 정석, 도우출판, 남궁성
