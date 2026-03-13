---
title: "Cube Defence - Gamma"
date: 2026-03-13 00:00:00
categories: [Project, Unity]
tags: [CubeDefence]
description: ""
---

# CubeDefence Gamma Architecture Guide

## 1. 아키텍처의 명칭

이 구조는 하나의 단일 정식 명칭만 가진 패턴이라기보다, 여러 검증된 구조를 목적에 맞게 조합한 형태입니다.

가장 가까운 명칭은 다음입니다.

**Feature-First Modular Clean Architecture**

조금 더 풀어서 말하면 다음 4가지를 조합한 구조입니다.

- **Clean Architecture**
- **Hexagonal Architecture (Ports & Adapters)**
- **Vertical Slice / Feature-First Architecture**
- **DDD의 일부 개념 (Domain 중심 분리)**

즉, 감마 구조는  
`기능 단위로 나누고(Feature-First)`  
`핵심 로직을 Unity로부터 분리하며(Clean Architecture)`  
`외부 시스템과는 Port를 통해 연결하고(Hexagonal)`  
`규칙과 상태를 Domain/Application에 배치하는(DDD-lite)`  
방향으로 설계한 구조입니다.

---

## 2. 어떤 구조를 참고했는가

### 2-1. Clean Architecture에서 참고한 점

- 핵심 로직이 엔진이나 프레임워크에 종속되지 않아야 한다
- UI, 네트워크, 저장소는 바깥 계층이다
- 의존성 방향은 항상 바깥에서 안쪽으로 향해야 한다

감마에서 이것은 다음으로 반영됩니다.

- `Domain`과 `Application`은 Unity를 모른다
- `Presentation`과 `Infrastructure`가 Unity/Netcode를 가진다
- `Application`은 `Ports`만 알고 실제 구현은 모른다

---

### 2-2. Hexagonal Architecture에서 참고한 점

- 외부 시스템은 모두 Adapter로 취급한다
- 핵심 로직은 Port(인터페이스)를 통해 외부와 통신한다

감마에서 이것은 다음으로 반영됩니다.

- 네트워크, 저장, UI 반영, 씬 전환 같은 외부 시스템은 `Ports`로 추상화한다
- 실제 구현은 `Infrastructure` 또는 `Presentation`에서 한다

예시:
- `IPlacementNetworkGateway`
- `ISaveRepository`
- `IWaveHudOutputPort`

---

### 2-3. Vertical Slice / Feature-First에서 참고한 점

- 프로젝트를 오브젝트 종류가 아니라 기능 중심으로 나눈다
- “이 기능은 어디에 넣지?”라는 질문에 답이 쉬워진다

감마에서 이것은 다음으로 반영됩니다.

- `CubeField`, `Canvas`, `Player`처럼 오브젝트 기준이 아니라
- `BuildMode`, `TowerPlacement`, `Wave`, `Economy`, `Combat`처럼 기능 기준으로 나눈다

즉,
- 알파는 “어떤 오브젝트를 수정할까?” 구조
- 감마는 “어떤 기능이 이 규칙을 소유하나?” 구조

---

### 2-4. DDD에서 참고한 점

DDD 전체를 도입한 것은 아니지만, 다음 개념은 적극 참고했습니다.

- 규칙은 Domain이 소유한다
- 상태 전이와 유스케이스는 Application이 소유한다
- 값 개념은 Value Object로 다룬다

예시:
- 타일 좌표
- 타워 타입
- 웨이브 상태
- 비용 정책
- 배치 가능 여부

---

## 3. 왜 이런 구조로 설계했는가

이 구조는 단순히 “예쁘게 정리된 폴더 구조”를 만들려는 목적이 아닙니다.  
알파에서 드러난 문제를 구조적으로 막기 위해 설계한 것입니다.

---

## 4. 알파의 문제와 감마의 대응

### 문제 1. 게임오브젝트 컨트롤러에 로직이 몰리면 복잡도가 급격히 증가한다

알파에서는 하나의 컨트롤러가 쉽게 다음 역할을 동시에 갖게 됩니다.

- 입력 처리
- 상태 전이
- 조건 검사
- 타겟 판정
- UI 갱신
- 네트워크 호출
- 다른 오브젝트와 직접 연결

이렇게 되면 기능 하나를 추가할 때마다
컨트롤러의 `if`, `switch`, 참조, 이벤트 연결이 계속 증가합니다.

#### 감마의 대응

감마에서는 책임을 다음처럼 분리합니다.

- 입력 전달: `Presentation`
- 규칙 판단: `Domain`
- 기능 흐름 제어: `Application`
- 외부 시스템 연결: `Ports + Infrastructure`
- 모듈 연결: `App/Bootstrap`

즉, `MonoBehaviour`는 더 이상 기능의 주인이 아니라 **입력/출력 통역기**가 됩니다.

---

### 문제 2. 구조가 직관적이지 않아서 어디서 시작할지 불명확하다

알파 같은 구조에서는 새 기능이 들어올 때 이런 질문이 생깁니다.

- 이건 `Player`에 넣어야 하나?
- `Canvas`에서 처리해야 하나?
- `GameSystem`이 가져야 하나?
- `CubeField`가 이 로직도 알아야 하나?

#### 감마의 대응

감마는 시작점을 항상 고정합니다.

1. 먼저 기능 이름을 정한다
2. `Scripts/Features/<FeatureName>`에 들어간다
3. 규칙은 `Domain`
4. 유스케이스는 `Application`
5. 외부 요구사항은 `Ports`
6. Unity 연결은 `Presentation/Infrastructure`
7. 조립은 `Bootstrap`

즉, “무엇을 어디서 시작해야 하는가”에 대한 패턴을 구조 자체가 제공하도록 설계했습니다.

---

## 5. 감마 구조의 핵심 철학

### 핵심 원칙

- **기능이 구조의 기준이다**
- **Unity는 핵심 로직의 중심이 아니다**
- **컨트롤러는 얇아야 한다**
- **외부 시스템은 Port를 통해 연결한다**
- **테스트 가능한 계층과 Unity 의존 계층을 분리한다**
- **새 기능은 기존 컨트롤러에 끼워 넣지 말고 기능 모듈로 추가한다**

---

## 6. 의존성 방향

```text
App/Bootstrap
    -> Features/*/Presentation
    -> Features/*/Infrastructure
    -> Features/*/Application
    -> Features/*/Domain

Presentation -> Application + Ports + Shared
Infrastructure -> Application + Ports + Shared
Application -> Domain + Ports + Shared
Domain -> Shared only
```
