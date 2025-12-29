---
title: "Bubble Sort"
date: 2025-03-04 14:00:00
categories: [Common, Algorithm]
tags: [Algorithm]
description: ""
---

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
