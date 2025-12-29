---
title: "Selection Sort"
date: 2025-03-01 14:00:00
categories: [Common, Algorithm]
tags: [Algorithm]
description: ""
---

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
