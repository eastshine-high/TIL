# 비대칭키 암호(Asymmetric Cryptography)

## 키 배송 문제

대칭키 암호를 사용하려고 하면 키 배송 문제(key distribution problem)가 발생한다. 키를 보내지 않으면 수신자는 수신한 암호문을 복호화할 수 없다. 그렇다고 암호화되지 않은 키를 보내면 도청자도 복호화할 수 있다. 키를 보내지 않으면 안 되는데 키를 보내서도 안 된다. 이것이 대칭키 암호의 키 배송 문제이다.

키 배송 문제를 해결하기 위한 방법에는 몇 가지가 있다.

- 키의 사전 공유에 의한 해결
- 키배포 센터에 의한 해결(온라인 키 분배)
- Diffie-Hellman 키 교환에 의한 해결
- 공개키 암호에 의한 해결

대칭키 암호에서 암호화키와 복호화키는 같은 것이다. 그러나 **공개키 암호에서는 암호화키와 복호화키는 다른 것이다.** **수신자는 미리 암호화키를 송신자에게 알려 준다.** 이 암호화키는 도청자에게 알려져도 괜찮다. 송신자는 암호화키로 암호화하여 수신자에게 보낸다. 복호화할 수 있는 것은 복호화키를 가지고 있는 사람(수신자)뿐이다. 이렇게 하면 복호화키를 수신자에게 배송할 필요가 없어진다.

## 공개키 암호(public-key-cryptography)

대칭키 암호는 평문을 복잡한 형태로 변환해서 기밀성은 유지한다. **공개키 암호는 수학적으로 해결하기 곤란한 문제를 토대로 해서 기밀성을 유지한다.** 공개키 암호에서는 암호화키와 복호화키가 분리되어 있다. 송신자는 암호화키를 사용하여 메시지를 암호화하고, 수신자는 복호화키를 사용하여 암호문을 복호화한다. 키쌍을 이루고 있는 2개의 키는 서로 밀접한 관계(수학적 관계)가 있다. 이 때문에 공개키와 개인키를 각각 별개로 만들 수는 없다.

## RSA 암호시스템

RSA는 공개키 암호 알고리즘 중의 하나이며, 세계적으로 사실상의(de facto) 표준이다.

### 암호화와 복호화

RSA에서는 두 개의 지수 `e`와 `d`를 사용한다. 여기서 `e`는 공개하는 값이고 `d`는 비밀로 유지하는 값이다. `P`는 평문이고 `C`는 암호문이다.

**암호화 -** 평문: P, 암호문: C =  <!-- $P^e$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=P%5Ee"> mod n

**복호화 -** 암호문 : C, 평문: P = <!-- $C^d$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=C%5Ed"> mod n

송신자는 'C =<!-- $P^e$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=P%5Ee">mod n'를 이용하여 평문 P에서 암호문 C를 생성한다. 수신자는 암호문 C에서 'P =<!-- $C^d$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=C%5Ed">mod n'을 구하여 송신자가 보낸 평문을 얻는다. 모듈러 n은 매우 큰 수이고 키 생성 프로세스를 통해서 만들어진다.

### 키 생성

*algorithm*

- p와 q라고 하는 두 개의 서로 다른 소수를 고른다.
- 두 수를 곱하여 N=pq을 찾는다.
- Φ(N)=(p-1)(q-1)를 구한다.
- Φ(N)보단 작고, Φ(N)과 서로소인 정수 `e`를 찾는다.
- 확장된 유클리드 호제법을 이용하여 d x e를 Φ(N)로 나누었을 때 나머지가 1인 정수 `d`를 구한다. (de ≡ 1 (mod Φ(N)))

만약 공개키 n과 e로부터 비밀키 d를 구할 수 있따면 RSA는 해독되게 된다. 이렇게 되기 위해서는 공개키 n으로부터 Φ(n) = (p - 1)(q - 1)을 구해 내야 한다. Φ(n)을 구하게 되면 유클리드 알고리즘을 이용하여 쉽게 d를 구할 수가 있게 되므로 RSA 알고리즘의 안정성은 n의 소인수 분해, 즉 p와 q를 구해 내는 것에 달려 있다.

안전을 위해서 권장되는 각 소수 p와 q의 크기는 512비트이다. 이 크기는 10진수로 약 154자리수이다. 이런 소수를 사용하면 모듈러로 사용할 n은 1024비트로 십진수 309자리가 된다.

### RSA 알고리즘 예

- 두 소수 p = 17과 q = 11을 선택한다.
- N = p x q = 17 x 11 = 187을 계산한다.
- Φ(N) = (p-1)(q-1) = 16x10 = 160을 계산한다.
- Φ(N) = 160보다 작으면서 Φ(N)과 서로소인 수 `e`를 선택한다. 여기서는 e=7을 선택한다.
- d < 160이면서 de mod 160 = 1인 수 `d`를 결정한다. 여기에 적합한 수는 d = 23이다. 왜냐하면 23 x 7 = 161 = 1 x 160 + 1이기 때문이다.
- 결과로 얻어지는 공개키 PU = {7, 187}이고, 개인키는 PR = {23, 187}이다.

평문(88)—>암호화: <!-- $88^7mod187=11$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=88%5E7mod187%3D11"> ——> 복호화: <!-- $11^{23}mod187=88$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=11%5E%7B23%7Dmod187%3D88"> —>평문(88)

## 참조

<조현준, 2021 정보보안기사 필기, 탑스팟>
