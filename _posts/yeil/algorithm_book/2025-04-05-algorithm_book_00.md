---
title: 이것이 자료구조+ 알고리즘이다 (목차)
author: cotes
date: 2025-04-05 10:00:00
categories: [Yeil, Algorithm_Book]
tags: []
description: 
  Algorithm_Book
---

### 0. 알아두면 쓸 데 있는 자료구조와 알고리즘

- 자료구조
- 알고리즘
- C언어로 메모리를 다루는 방법
  - 포인터 복습
  - 구조체 복습
  - 메모리 레이아웃 복습
  - 스택에서 데이터를 다루는 방법
  - 힙에서 데이터를 다루는 방법

---

### 1. 리스트

- 리스트 ADT
  - 리스트의 개념
  - 리스트와 배열 비교
- 링크드 리스트
  - 링크드 리스트의 노드 표현
  - 링크드 리스트의 주요 연산
  - 링크드 리스트 예제 프로그램
  - 링크드 리스트의 장단점
- 더블 링크드 리스트
  - 더블 링크드 리스트의 주요 연산
  - 더블 링크드 리스트 예제 프로그램
- 환형 링크드 리스트
  - 환형 더블 링크드 리스트의 주요 연산
  - 환형 더블 링크드 리스트 예제 프로그램

---

### 2. 스택

- 스택 ADT
  - 스택의 개념
  - 스택의 핵심 기능: 삽입과 제거 연산
- 비열로 구현하는 스택
  - 배열 기반 스택과 스택의 노드 표현
  - 배열 기반 스택의 기본 연산
  - 배열 기반 스택 예제 프로그램
- 링크드 리스트로 구현하는 스택
  - 링크드 리스트 기반 스택과 스택의 노드 표현
  - 링크드 리스트 기반 스택의 기본 연산
  - 링크드 리스트 기반 스택 예제 프로그램
- 스택의 응용: 사직 연산 계산기
  - 수식의 중위 표기법과 후위 표기법
  - 후위 표기식을 계산하는 알고리즘
  - 중위 표기식을 후위 표기식으로 바꾸는 알고리즘
  - 사칙 연산 계산기 예제 프로그램

---

### 3. 큐

- 큐 ADT
  - 큐의 개념
  - 큐의 핵심 기능: 삽입과 제거 연산
- 순환 큐
  - 공백 상태와 포화 상태
  - 순환 큐의 기본 연산
  - 순환 큐 예제 프로그램
- 링크드 큐
  - 링크드 큐의 기본 연산
  - 링크드 큐 예제 프로그램

---

### 4. 트리

- 트리 ADT
  - 트리의 개념
  - 트리의 구성 요소
  - 트리 표현 방법
  - 노드 표현 방법
  - 트리의 기본 연산
  - 트리 예제 프로그램
- 이진 트리
  - 이진 트리의 종류
  - 이진 트리의 순회
  - 이진 트리의 기본 연산
  - 이진 트리 예제 프로그램
- 수식 트리
  - 수식 트리 구축 방법
  - 수식 트리의 구현
  - 수식 트리 예제 프로그램
- 분리 집합
  - 분리 집합 표현
  - 분리 집합의 기본 연산
  - 분리 집합 얘재 프로그램

---

### 5. 정렬

- 정렬 알고리즘의 개요
- 버블 정렬
  - 버블 정렬의 성능 측정
  - 버블 정렬 예제 프로그램
- 삽입 정렬
  - 삽입 정렬의 성능 측정
  - 삽입 정렬 예제 프로그램
- 퀵 정렬
  - 퀵 정렬 사용 전 해결해야 하는 2가지 문제
  - 퀵 정렬 예제 프로그램
  - 퀵 정렬의 성능 측정
- C 언어 표준 라이브러리의 퀵 정렬 함수: qsort()
  - qsort() 함수 예제 프로그램
  - qsort() 응용 문제

---

### 6. 탐색

- 탐색 알고리즘 개요
- 순차 탐색
  - 전진 이동법
  - 전위법
  - 계수법
- 이진 탐색
  - 이진 탐색의 성능 측정
  - 이진 탐색의 구현
  - 이진 탐색 예제 프로그램: 두번째 최종 시험 문제
  - C 언어 표쥰 라이브러리의 이진 탐색 함수: bsearch()
  - bsearch() 함수 예제 프로그램
- 이진 탐색 트리
  - 이진 탐색 트리 표현
  - 이진 탐색 트리의 기본 연산
  - 이진 탐색 트리 예제 프로그램
  - 이진 탐색 트리의 문제점
- 레드 블랙 트리
  - 레드 블랙 트리의 구현 규칙
  - 레드 블랙 트리의 기본 연산
  - 레드 블랙 트리 예제 프로그램

---

### 7. 우선순위 큐와 힙

- 우선순위 큐
  - 우선순위 큐의 삽입/제거 연산
  - 우선순위 큐의 구현
- 힙
  - 힙의 삽입 연산
  - 힙의 최솟값 삭제 연산
  - 힙의 구현
  - 힙 예제 프로그램
- 힙 기반 우선순위 큐의 구현

---

### 8. 해시 테이블

- 해시 테이블의 개요
  - 해시
  - 해시 테이블
- 해시 함수
  - 나눗셈법
  - 자릿수 접기
  - 해시 함수의 한계: 충돌
- 충돌 해결 기법
  - 체이닝
  - 개방 주소법

---

### 9. 그래프

- 그래프의 개요
  - 그래프의 탄생 배경: 오일러의 문제 해결 도구
  - 그래프의 정의
- 그래프 표현 방법
  - 인접 행렬
  - 인접 리스트
- 그래프 순회 기법
  - 깊이 우선 탐색
  - 너비 우선 탐색
  - 그래프 순회 예제 프로그램
- 위상 정렬
  - 위상 정렬의 동작 방식
  - 위상 정렬 얘제 프로그램
- 최소 신장 트리
  - 프림 알고리즘
  - 크루스칼 알고리즘
  - 최소 신장 트리 예제 프로그램
- 최단 경로 탐색: 데이크스트라 알고리즘
  - 데이크스트라 알고리즘 개념
  - 데이크스트라 알고리즘 예제 프로그램

---

### 10. 문자열 탐색

- 문자열 탐색 알고리즘의 개요
- 고지식한 탐색 알고리즘
  - 고지식한 탐색의 동작 방식
  - 고지식한 탐색 알고리즘 예제 프로그램
- 카프-라빈 알고리즘
  - 카프-라빈 날고리즘의 동작 방식
  - 카프-라빈 알고리즘 예제 프로그램
- KMP 알고리즘
  - KMP 알고리즘의 동작 방식
  - 경계 정보 사전 계산 방법
  - KMP 알고리즘 예제 프로그램
- 보이어-무어 알고리즘
  - 나쁜 문자 이동
  - 착한 접미부 이동
  - 보이어-무어 알고리즘의 전처리 과정
  - 보이어-무어 알고리즘 예제 프로그램

---

### 11. 알고리즘 성능 분석

- 알고리즘 성능 측정 기준과 수행 시간
  - 알고리즘 성능 측정 기준
  - 알고리즘 수행 시간 분석
- 점근 표기법
  - O 표기법
  - Ω 표기법
  - Θ 표기법
- 재귀 알고리즘 성능 분석
  - 재귀 방정식과 재귀 알고리즘
  - 퀵 정렬의 성능 분석
  - 마스터 정리

---

### 12. 분할 정복

- 분할 정복 기법의 개요
  - 분할 정복 진술의 탄생 배경: 아우스터리츠 전투
  - 분할 정복 알고리즘의 개념
- 병합 정렬
  - 병합 정렬 동작 방식
  - 병합 정렬 알고리즘의 개념
- 거듭 제곱 계산
  - 거듭 제곱 계산법
  - 거듭 제곱 계산 알고리즘의 구현
- 분할 정복 기반 피보나치 수 구하기
  - 피보나치 수를 구하는 방법
  - 분할 정복으로 피보나치 수를 구하는 방법
  - 분할 정복 기반 피보나치 수 구하기 알고리즘의 구현

---

### 13. 동적 계획법

- 동적 계획법의 개요
  - 동적 계획법의 탄생 배경
  - 동적 계획법의 개념
- 동적 계획법 기반 피보나치 수 구하기
  - 동적 계획법으로 피보나치 수를 구하는 방법
  - 동적 계획법 기반 피보나치 수 구하기 알고리즘의 구현
- 최장 공통 부분 수열
  - LCS 알고리즘 
  - 동적 계획법 기반 LCS 알고리즘의 구현

---

### 14. 탐욕 알고리즘

- 탐욕 알고리즘의 개요
- 거스름돈 줄이기 문제
  - 거스름돈 계산 예제 프로그램
  - 탐욕 알고리즘의 중요한 속성
- 크루스칼 알고리즘 다시 보기
- 허프만 코딩
  - 고정 킬이 코드와 접두어 코드
  - 허프만 트리 구축
  - 데이터 압축
  - 데이터 압축 해제
  - 허프만 코딩 예제 프로그램

---

### 15. 백트래킹

- 백트래킹의 개요
  - 백트래킹의 사례: 테세우스 이야기
  - 백트래킹의 개념
- 미로 탈출로 찾기
  - 재귀호출 기반 백트래킹
  - 미로 탈출 알고리즘의 구현
  - 미로 탈출 알고리즘 예제 프로그램
- 8개의 퀸 문제
  - 8개의 퀸이 만드는 해공간과 백트래킹
  - N개의 퀸 문제 풀이 알고리즘 구현
  - N개의 퀸 문제 풀이 예제 프로그램

