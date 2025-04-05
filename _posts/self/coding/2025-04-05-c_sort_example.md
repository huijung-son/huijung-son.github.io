---
title: SelectionSort, InsertionSort, BubbleSort
author: cotes
date: 2025-04-05 10:00:00
categories: [Self, Coding]
tags: []
description: 
  Coding
---

### 1. SelectionSort 연습코드

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define LEN 10

// 이중 for문으로 하나씩 비교를 다하면서 정렬한다
void selection_sort(int _arr[]) {
    // 첫 반복에서 값을 바꾸는게 아니라 배열의 모든 요소를 하나씩 확인하기 위한 반복일뿐
    for (int i = 0; i < LEN; ++i) {

        // 일단 제일작은 값은 첫번째 값이 라고 가정하는 초기값 주기
        int min_idx = i;
        // 작은값은 i로 줬기때문에 j는 i 의 다음값으로
        // 사실 0으로 해도 상관은 없다 (이미 안좋은 정렬방식이라 조금 안좋아진다고 해서 차이 나는거 아님;;)
        for (int j = i + 1; j < LEN; ++j) {
            // j값이 기준값보다 작으면 인덱스 교환
            if (_arr[j] < _arr[min_idx]) {
                min_idx = j;
            }
        }

        // 처음부터 끝까지 제일작은 값을 찾았기때문에 반복을 중간에 끊지않고 진행 함
        // 무조건 교환
        int tmp = _arr[i];
        _arr[i] = _arr[min_idx];
        _arr[min_idx] = tmp;
    }
}

void sort_print(int _arr[]) {
    for (int i = 0; i < LEN; ++i) {
        printf("%d - ", _arr[i]);
    }
    printf("\n");
}

int main()
{
    srand((unsigned int)time(NULL));
    int arr[10] = {0};
    for (int i = 0; i < 10; ++i) {
        arr[i] = rand() % 50;
    }

    sort_print(arr);
    selection_sort(arr);
    sort_print(arr);

    return 0;
}
```

---

### 2. InsertionSort 연습코드

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define LEN 10

// 1번 인덱스부터 시작해서 왼쪽으로 이동하는데 왼쪽 값이 작으면 해당 값을 오른쪽으로 밀면서(스왑) 비교한다
void insertion_sort(int _arr[]) {
    // 배열 길이 만큼 반복
    for (int i = 1; i < LEN; ++i) {
        // 감소할 j 인덱스 왜 갑소하는지는 밑에서 설명
        int j = i;
        // 기준 값
        int value = _arr[i];
        // j 가 감소 하면서 작은값이면
        while (j > 0) {
            // 기준값이 바로 옆에 값보다 크면 종료 해당 왼쪽
            if (value > _arr[j - 1]) {break;}
            // 위의 조건을 지나 쳤다는건 기준값보다 왼쪽값이 크다
            // 기준값 자리에 왼쪽값을 바로 삽입
            _arr[j] = _arr[j - 1];
            --j;
        }
        // 반복이 끝나면 감소한 j자리에 기준값 삽입
        _arr[j] = value;
    }
}

void sort_print(int _arr[]) {
    for (int i = 0; i < LEN; ++i) {
        printf("%d - ", _arr[i]);
    }
    printf("\n");
}

int main()
{
    srand(time(NULL));
    int arr[LEN] = {0};
    for (int i = 0; i < LEN; ++i) {
        arr[i] = rand() % 50;
    }

    sort_print(arr);
    insertion_sort(arr);
    sort_print(arr);

    return 0;
}
```

---

### 3. BubbleSort 연습코드

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define LEN 10

// 0번째 1번째 비교후 교환 1번째 2번째 비교후 교환 ...
void bubble_sort(int _arr[]) {
    // 첫번째 포문은 하나식 감소하는게 특징
    for (int i = LEN - 1; i > 0; --i) {
        // 비교후 교환은 해당 for 문이 다 함
        for (int j = 0; j < i; ++j) {
            if (_arr[j] > _arr[j + 1]) {
                int tmp = _arr[j];
                _arr[j] = _arr[j + 1];
                _arr[j + 1] = tmp;
            }
        }
    }
}

void sort_print(int _arr[]) {
    for (int i = 0; i < LEN; ++i) {
        printf("%d - ", _arr[i]);
    }
    printf("\n");
}

int main()
{
    srand(time(NULL));
    int arr[LEN] = {0};
    for (int i = 0; i < LEN; ++i) {
        arr[i] = rand() % 50;
    }

    sort_print(arr);
    bubble_sort(arr);
    sort_print(arr);

    return 0;
}
```
