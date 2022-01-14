# Merge Sort(병합 정렬)

병합 정렬은 컴퓨터 과학 역사상 최고의 천재라 일컬어지는 존 폰 노이만John von Neumann이 1945년에 고안한 알고리즘으로, 분할 정복(Divide and Conquer)의 진수를 보여주는 알고리즘이다. **최선과 최악 모두 O(n log n)**인 사실상 완전한 θ(n log n)으로 일정한 알고리즘이며, 대부분의 경우 퀵 정렬 보다는 느리지만 일정한 실행 속도뿐만 아니라 무엇보다도 **안정 정렬(Stable Sort)**이라는 점에서 여전히 상용 라이브러리에 많이 쓰이고 있다.

<br>

### 알고리즘

**병합 정렬(Merge Sort)은 같은 개수의 원소를 가지는 부분 집합으로 기존 자료를 분할(Divide)하고 분할된 각 부분 집합을 병합(Merge)하면서 정렬 작업을 완성(Conquer)하는 분할 정복(Divide and Conquer)기법에 의한 정렬 방식이다.** 즉, 병합 정렬은 여러 개의 정렬된 부분집합을 결합하여 정렬된 집합을 만드는 알고리즘이다. 2개의 정렬된 자료 집합을 병합하는 경우는 2-way 병합이라고 하며, n개의 정렬된 자료 집합을 결합하는 경우는 n-way 병합이라고 한다.

<br>

(1) 분할 단계

**병합 정렬은 먼저 기존 자료를 동일한 개수의 원소를 가지는 부분 집합으로 분할해야 한다.** 여기서는 2-way 병합을 사용하기 때문에, 초기 전체 자료를 2개의 부분 집합으로 나눈다. 또한, 나뉜 2개의 부분집합에 대해서도 각각 2개의 부분 집합으로 나누며, **이러한 분할 단계를 더 이상 나눌 수 없을 때 까지 (여기서는 1개) 계속하여 부분 집합으로 나눈다.**

![https://media.geeksforgeeks.org/wp-content/cdn-uploads/Merge-Sort-Tutorial.png](https://media.geeksforgeeks.org/wp-content/cdn-uploads/Merge-Sort-Tutorial.png)

[https://media.geeksforgeeks.org/wp-content/cdn-uploads/Merge-Sort-Tutorial.png](https://media.geeksforgeeks.org/wp-content/cdn-uploads/Merge-Sort-Tutorial.png)

(2) 병합 단계

**병합 단계는 기존의 분할된 부분 집합의 자료를 합치면서 정렬을 실행하는 단계이다.** 여기서는 2-way 병합을 이용하기 때문에, 나누어진 2개의 부분 집합을 각각 병합한다.

<br>

### 구현(C)

```c
#include <stdio.h>
#include <stdlib.h>

void printArray(int value[], int count);
void mergSortInternal(int value[], int buffer[], int start, int middle, int end);

// 병합 정렬.
void mergeSort(int value[], int buffer[], int start, int end)
{
	int middle = 0;
	if (start < end) {
		middle = (start + end) / 2;
		mergeSort(value, buffer, start, middle);
		mergeSort(value, buffer, middle+1, end);
		mergSortInternal(value, buffer, start, middle, end);
	}
}

void mergSortInternal(int value[], int buffer[], int start, int middle, int end)
{
	int i = 0, j = 0, k = 0, t = 0;
	i = start;
	j = middle + 1;
	k = start;

	while(i <= middle && j <= end) {
		if (value[i] <= value[j]) {
			buffer[k] = value[i];
			i++;
		}
		else {
			buffer[k] = value[j];
			j++;
		}
		k++;
	}

	if (i > middle) {
		for(t = j; t <= end; t++, k++) {
			buffer[k] = value[t];
		}
	}
	else {
		for(t = i; t <= middle; t++, k++) {
			buffer[k] = value[t];
		}
	}
	for(t = start; t <= end; t++) {
		value[t] = buffer[t];
	}

	printf("start-%d,middle-%d,end-%d,", start, middle, end);
	for(t = start; t <= end; t++) {
		printf("%d ", value[t]);
	}
	printf("\n");
}

int main(int argc, char *argv[])
{
	int values[] = {80, 50, 70, 10, 60, 20, 40, 30 };
	int *pBuffer = NULL;

	printf("Before Sort\n");
	printArray(values, 8);

	pBuffer = (int *)malloc(sizeof(int) * 8);
	if (pBuffer != NULL) {
		mergeSort(values, pBuffer, 0, 7);

		free(pBuffer);
	}

	printf("After Sort\n");
	printArray(values, 8);

	return 0;
}

// 배열 내용 출력
void printArray(int value[], int count)
{
	int i = 0;
	for(i = 0; i < count; i++) {
		printf("%d ", value[i]);
	}
	printf("\n");
}
```
