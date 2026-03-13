---
title: "Cube Defence - Beta"
date: 2026-02-25 00:00:00
categories: [Project, Unity]
tags: [CubeDefence]
description: ""
---

# FSD + 헥사고날 아키텍처 설계

## 왜 이 구조를 채택했는가

```text
알파 버전 1, 2를 거치며 두 가지 문제가 반복됐다.

첫째, **게임오브젝트 단위로 개발하면 코어 로직의 인터페이스가 어디에 연결되는지 직관적이지 않았다.**
하나의 MonoBehaviour 안에 UI 처리, 네트워크 호출, 게임 로직이 섞이면서 "이 기능이 어디서 시작해서 어디로 흘러가는지" 파악하기 어려웠다.

둘째, **오브젝트 간 상호작용이 늘어날수록 복잡도가 급격히 올라갔다.**
A 오브젝트가 B를 참조하고, B가 다시 C를 참조하는 식으로 의존성이 꼬이면서, 하나를 수정하면 연쇄적으로 다른 곳이 깨지는 문제가 생겼다.

이를 해결하기 위해 **FSD(Feature-Sliced Design)** 의 레이어 규칙으로 전체 참조 방향을 단방향으로 강제하고,
각 Feature 내부는 **헥사고날(Ports & Adapters)** 패턴으로 외부 기술과 도메인 로직을 분리했다.
이렇게 하면 "이 기능은 Features 폴더에서 시작해서, Port를 통해 Adapter로 나간다"는 흐름이 폴더 구조만 보고도 파악된다.
또한 Feature끼리 직접 참조하지 못하게 막아서 상호작용 복잡도를 통제할 수 있다.
```

---

## 목표
- FSD + 헥사고날 구조로 참조 방향을 단방향으로 고정한다.
- Unity 외부 기술과 도메인 규칙을 분리한다.
- 변경이 잦은 구현 디테일이 아니라, 바뀌지 않는 구조 규칙만 문서화한다.

## 고정 폴더 구조

```text
Scripts/
├── Entities/                                # 공유 도메인 모델 (enum, struct, 값 객체)
│
├── Features/                                # 기능 슬라이스 (도메인명 기준)
│   └── {FeatureName}/
│       ├── Application/                     # 순수 도메인 로직
│       │   └── EntryOnly/                   # (선택) 특정 EntryPoint 전용 순수 로직
│       └── Ports/                           # 외부 의존 계약 인터페이스
│
└── App/                                     # 외부 접점 + 조립
    ├── Composition/                         # DI 조립 (Composition Root)
    ├── EntryPoints/                         # Driving Adapter (Unity 이벤트 수신)
    ├── Adapters/                            # Driven Adapter (Port 구현)
    └── Config/                              # ScriptableObject 설정 데이터
```

## 참조 방향 (불변 규칙)

```text
App -> Features -> Entities
```

- 역방향 참조 금지 (`Features -> App` 금지, `Entities -> *` 금지)
- Feature 간 직접 참조는 지양하고, 필요 시 `Entities` 또는 명시적 공유 Feature로 해결한다.
- Composition만 예외적으로 전체를 조립한다.

## 레이어 책임
- `Entities`: 공통 도메인 모델, 외부 기술 의존 없음
- `Features/Ports`: 외부 요청 계약(인터페이스), 외부 라이브러리 타입 노출 금지
- `Features/Application`: 비즈니스 규칙/상태전이/계산 로직
- `App/EntryPoints`: MonoBehaviour, Unity 이벤트 수신/전달
- `App/Adapters`: Port 구현체, Unity/Netcode/SceneManager 등 외부 기술 호출
- `App/Composition`: DI 등록과 연결
- `App/Config`: SO 기반 설정 데이터

## EntryOnly 규칙 (Application 내부)
- `Application/EntryOnly`는 사용처 범위 표시다.
- 위치 가능 조건:
  - 순수 C# 로직
  - Unity API/MonoBehaviour 의존 없음
- 용도:
  - 특정 EntryPoint에서만 사용하는 상태 규칙/보조 로직 분리
- 승격:
  - 재사용 범위가 넓어지면 `EntryOnly` 밖으로 이동

## 클래스 성격 구분 (핵심 기준)
- `UseCase`: 요청 1건 처리 중심 (일회성 명령)
- `StateMachine/System`: 상태 누적/전이 중심 (반복 호출 규칙)
- 기준은 호출 횟수가 아니라 책임의 성격이다.

## 네이밍 고정 규칙
- Feature 폴더: 도메인명 (`Menu`, `Lobby`, `CubeField`, `Gameplay`)
- EntryPoint MonoBehaviour: `*Controller`
- Adapter 구현체: 기술명 + Port명 (`UnitySceneLoader`, `UnityNetSession`)
- Port 인터페이스: `I*` 계약명 (`ISceneLoader`, `INetStatus`)

## DI 고정 규칙 (VContainer)
- 순수 C# Feature 클래스: `Register`
- Port 구현 Adapter: `Register().As<IPort>()`
- 씬/프리팹 MonoBehaviour: `RegisterComponentInHierarchy` / `RegisterComponentInNewPrefab`
- `Awake()`에서 DI 의존 필드 사용 금지, DI 의존 로직은 `[Inject] Construct(...)`에서 처리
