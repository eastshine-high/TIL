### 인수

함수 호출 문장에서 함수에 전달하는 값으로, 매개변수를 통해 인수를 전달한다.

형식 매개변수(parameter) : 인수를 전달받기 위해 함수에 선언된 매개변수이다.

실 매개변수(argument) : 함수 호출 문장에서 함수의 형식 매개변수에 전달할 값이다.

[Cf. 인수, 인자 용어에 대한 갑론을박](https://medium.com/@kyle_seongwoo_jun/argument-%EB%8A%94-%EC%9D%B8%EC%88%98%EC%9D%B8%EA%B0%80-%EC%9D%B8%EC%9E%90%EC%9D%B8%EA%B0%80-41e12e8b1750)

인수 전달 방식 : 2가지 방식이 있다. (1) Call by value(값 호출), (2) Call by reference(참조 호출)

<br>

### Call by value(값 호출)

- 실 매개변수(argument)의 값을 형식 매개변수(parameter)에 복사한다.
- 함수에서 형식 매개변수의 값을 변경해도 실 매개변수의 값은 변하지 않는다.
- 실 매개변수의 값을 형식 매개변수에 복사하는 인수 전달 방식으로, 함수 내에서 형식 매개변수의 값을 수정하는 것은 복사본을 수정하는 것이므로 원본인 실 매개변수의 값에는 영향이 없다.

```c
#include <stdio.h>

int Add(int a, int b)
{
    return a + b;
}

int main(int argc, char* argv[])
{
    printf("%d\n", Add(3, 4);
    return 0;
}
```

<br>

### Call by reference(참조 호출)

- 실 매개변수의 참조를 형식 매개변수에 전달한다.
- 형식 매개변수는 실 매개변수에 대한 참조이므로 함수에서 형식 매개변수의 값을 변경하는 것은 실 매개변수의 값을 변경하는 것과 같다.

<br>

교환

```c
#include <stdio.h>

int Swap(int *pLeft, int *pRight)
{
    int nTmp = *pLeft;
    *pLeft = *pRight;
    *pRight = nTmp;
}

int main(int argc, char* argv[])
{
    int x = 10, y = 20;
    Swap(&x, &y);
    printf("%d, %d\n", x, y);
    return 0;
}
```

Call by reference의 핵심은 매개변수가 포인터라는 점입니다. 따라서 호출자는 반드시 메모리의 주소를 인수로 넘겨야 합니다. 그리고 피호출자 함수는 이 매개변수를 간접 지정함으로써 포인터가 가리키는 대상에 접근할 수 있습니다.

Call by reference 방식이 Call by value에 비해 가장 다른 점은 **주소를 통해 호출자 메모리에 접근할 수 있는 방법을 제시**함으로써 두 함수가 좀 더 강력하게 결합될 수 있다는 점입니다. 그리고 가장 큰 장점은 배열처럼 덩치가 큰 메모리를 매개변수로 전달할 수 있다는 점입니다.

<br>

사용자로부터 이름을 입력받기

```c
#include <stdio.h>

char* GetName(void)
{
    char *pszName = NULL;

    pszName = (char*)calloc(32, sizeof(char));
    printf("이름을 입력하세요. : ");

    gets_s(pszName, sizeof(char)* 32);

    return pszName;
}

int main(int argc, char* argv[])
{
    char *pszName = NULL;

    pszName = GetName();
    printf("당신의 이름은 %s입니다.\n", pszName);

    free(pszName);
    return 0;
}

실행결과
이름을 입력하세요. : (입력)eastshine
당신의 이름은 eastshine입니다.
계속하려면 아무 키나 누르십시오 . . .
```

포인터의 가장 큰 문제는 가리키는 대상의 실제 크기를 포인터 자체만으로는 알 수 없다는 점입니다. 그러므로 피호출함수의 매개변수가 포인터인 경우 반드시 대상 메모리의 크기를 함께 매개변수로 받아야 합니다.

매개변수가 포인터일 때 포인터가 가리키는 대상 메모리의 크기를 인수로 받는 것은 보안적으로나 설계적으로 매우 중요합니다.

<br>

문자열 길이 측정 함수

```c
#include <stdio.h>

int GetLength(const char *pszParam)
{
    int nLength = 0;
    while (pszParam[nLength] != '\0')
        nLength++;

    return nLength;
}

int main(int argc, char* argv[])
{
    char *pszData = "Hello";

    printf("%d\n", GetLength("Hi"));
    printf("%d\n", GetLength(pszData));
    return 0;
}

실행결과
2
5
```

매개 변수 char *pszParam 대신 char pszParam[]라고 써도 의미가 같습니다. 문법적으로 어떻게 기술하든 둘 다 포인터입니다. 그러나 후자처럼 기술하면 별 다른 주석이 없더라도 "배개변수로 배열의 이름이 전달"된다고 추론하기 쉽습니다.

const는 형한정어(type qualifier)이며 char*인 매개변수 pszParam을 통해 간접지정한 대상을 상수화한다는 의미입니다. 즉, 간접지정한 대상에서 정보를 읽을 수는 있지만 쓸 수는 없다는 뜻입니다.

---

### Reference

독하게 시작하는 C프로그래밍, 루비페이퍼, 최호성