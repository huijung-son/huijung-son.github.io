---
title: "Quick Sort"
date: 2025-03-01 14:00:00
categories: [Algorithm, Sort]
tags: []
description: ""
---

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define LEN 20

void Print(int _arr[])
{
    for (int i = 0; i < LEN; ++i)
    {
        printf("%d - ", _arr[i]);
    }
    printf("\n");
}

void QuickSort(int _arr[], const int _left_idx, const int _right_idx)
{
    // 시작부터 교차 되있으면 바로 종료
    if (_left_idx >= _right_idx) return;
    // 기준 제일 왼쪽
    const int pivot = _left_idx;
    // 왼쪽 커서 : 기준 바로 다음 인덱스
    int left_cursor_idx = pivot + 1;
    // 오른쪽 커서 : 맨 오른쪽 인덱스
    int right_cursor_idx = _right_idx;

    while (left_cursor_idx < right_cursor_idx)
    {
        // 왼쪽 커서 오른쪽 커서 값이 똑같아도 증가 왜냐하면 루프를 종료 시키는 조건으로
        while (_arr[pivot] >= _arr[left_cursor_idx])
        {
            ++left_cursor_idx;  // 왼쪽 커서 증가
        }

        while (_arr[pivot] < _arr[right_cursor_idx])
        {
            --right_cursor_idx;  // 오른쪽 커서 감소
        }

        // 증가 감소로 교차되면 바로 종료
        if (left_cursor_idx > right_cursor_idx) break;
        // 여기까지 왔을때 값을 판단하여 스왑
        if (_arr[left_cursor_idx] > _arr[right_cursor_idx]) {
            const int tmp = _arr[left_cursor_idx];
            _arr[left_cursor_idx] = _arr[right_cursor_idx];
            _arr[right_cursor_idx] = tmp;
        }

        printf("right = %d, left = %d\n", right_cursor_idx, left_cursor_idx);
        Print(_arr);
    }
    // 고정인 기준값이 다음 재귀를 돌기전에 비교후 스왑
    if (_arr[pivot] > _arr[right_cursor_idx]) {
        const int tmp = _arr[right_cursor_idx];
        _arr[right_cursor_idx] = _arr[pivot];
        _arr[pivot] = tmp;
        Print(_arr);
    }

    QuickSort(_arr, _left_idx, right_cursor_idx - 1);
    QuickSort(_arr, right_cursor_idx + 1, _right_idx);
}

void main()
{
    srand((int)time(NULL));
    int arr[LEN] = { 0 };
    for (int i = 0; i < LEN; ++i)
    {
        arr[i] = rand() % 50;
    }

    Print(arr);

    QuickSort(arr, 0, LEN - 1);

    Print(arr);
}
```
