---
title: "Insertion Sort"
date: 2025-03-03 14:00:00
categories: [Common, Algorithm]
tags: [Algorithm]
description: ""
---

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
