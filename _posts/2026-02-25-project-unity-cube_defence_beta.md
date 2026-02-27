---
title: "Cube Defence - Beta"
date: 2026-02-25 00:00:00
categories: [Project, Unity]
tags: []
description: ""
---

# FSD + 헥사고날 아키텍처 설계

## 왜 이 구조를 채택했는가

>알파 버전 1, 2를 거치며 두 가지 문제가 반복됐다.
>
>첫째, **게임오브젝트 단위로 개발하면 코어 로직의 인터페이스가 어디에 연결되는지 직관적이지 않았다.** 하나의 MonoBehaviour 안에 UI 처리, 네트워크 호출, 게임 로직이 섞이면서 "이 기능이 어디서 시작해서 어디로 흘러가는지" 파악하기 어려웠다.
>
>둘째, **오브젝트 간 상호작용이 늘어날수록 복잡도가 급격히 올라갔다.** A 오브젝트가 B를 참조하고, B가 다시 C를 참조하는 식으로 의존성이 꼬이면서, 하나를 수정하면 연쇄적으로 다른 곳이 깨지는 문제가 생겼다.
>
>이를 해결하기 위해 **FSD(Feature-Sliced Design)** 의 레이어 규칙으로 전체 참조 방향을 단방향으로 강제하고, 각 Feature 내부는 **헥사고날(Ports & Adapters)** 패턴으로 외부 기술과 도메인 로직을 분리했다. 이렇게 하면 "이 기능은 Features 폴더에서 시작해서, Port를 통해 Adapter로 나간다"는 흐름이 폴더 구조만 보고도 파악된다. 또한 Feature끼리 직접 참조하지 못하게 막아서 상호작용 복잡도를 통제할 수 있다.

---

## 전체 폴더 구조

```text
Scripts/
├── Entities/                          # 공유 도메인 모델
│   └── Player.cs
│
├── Features/                          # 기능 슬라이스
│   ├── Lobby/
│   │   ├── Application/               # UseCase (비즈니스 로직)
│   │   │   ├── StartHostUseCase.cs
│   │   │   └── StartClientUseCase.cs
│   │   └── Ports/                     # 인터페이스 (계약서)
│   │       └── INetSession.cs
│   └── Battle/
│       ├── Application/
│       └── Ports/
│
└── App/                               # 조립 및 외부 접점
    ├── Composition/                   # DI 등록 (Composition Root)
    │   ├── RootLifetimeScope.cs
    │   └── MenuLifetimeScope.cs
    ├── Adapters/                      # Driven Adapter (안 → 밖)
    │   └── Networking/
    │       └── UnityNetSession.cs
    └── EntryPoints/                   # Driving Adapter (밖 → 안)
        ├── UI/
        │   └── MenuCanvas.cs
        └── Networking/
            └── RootNetworkManager.cs
```

---

## 폴더별 역할

| 폴더                            | 역할                       | 방향           | 의존 대상                | 예시                  |
|-------------------------------|--------------------------|--------------|----------------------|---------------------|
| **Entities**                  | 공유 도메인 모델                | -            | 아무것도 의존하지 않음         | Player, Tower, Wave |
| **Features/{기능}/Ports**       | 인터페이스(계약서) 선언            | -            | Entities만 가능         | INetSession         |
| **Features/{기능}/Application** | UseCase (비즈니스 로직)        | 안쪽           | Ports + Entities만 가능 | StartHostUseCase    |
| **App/Adapters**              | Driven Adapter (안→밖 번역)  | 안에서 밖으로      | Ports 구현 + 외부 라이브러리  | UnityNetSession     |
| **App/EntryPoints**           | Driving Adapter (밖→안 전달) | 밖에서 안으로      | UseCase 호출           | MenuCanvas          |
| **App/Composition**           | DI 조립 (Composition Root) | 모든 것을 알 수 있음 | 전체 바인딩               | RootLifetimeScope   |

---

## 폴더별 상세 설명

### Entities — 공유 도메인 모델

여러 Feature가 공유하는 핵심 모델이 들어간다. Player, Tower 같은 게임 데이터 클래스가 이에 해당한다. 어디에도 의존하지 않는 가장 안쪽 레이어다. 순수 C# 클래스로 작성하며 Unity API 의존을 최소화한다.

### Features — 기능 슬라이스

FSD의 핵심 개념으로, 기능 단위로 슬라이스된 폴더다. 각 Feature(Lobby, Battle 등)는 독립적이며 서로 직접 참조하지 않는다. 내부에 Application과 Ports를 두어 헥사고날 구조를 따른다.

#### Features/{기능}/Ports — 인터페이스(계약서)

Feature가 외부에 "이런 기능이 필요해"라고 선언하는 인터페이스다. 어떤 기술로 구현되는지는 모르고 계약만 정의한다. 예를 들어 `INetSession`은 "호스트를 시작해줘"라는 계약이지, Netcode를 쓰라는 지시가 아니다. 메서드 시그니처에 외부 라이브러리 타입이 노출되면 안 된다.

#### Features/{기능}/Application — UseCase(비즈니스 로직)

하나의 UseCase 클래스가 하나의 행위를 담당한다. Port 인터페이스에만 의존하며 구체 구현 클래스를 직접 참조하지 않는다. 공개 메서드는 `Execute()` 하나만 갖는다. 예를 들어 `StartHostUseCase.Execute()`는 `INetSession.StartHost()`를 호출할 뿐, Netcode의 존재를 모른다.

### App — 조립 및 외부 접점

전체 시스템에서 유일하게 Features와 외부 기술 양쪽을 모두 아는 레이어다. 내부에 세 개의 하위 폴더를 갖는다.

#### App/Adapters — Driven Adapter(안에서 밖으로)

Port 인터페이스를 받아서 실제 외부 라이브러리(Netcode, 파일 시스템, 광고 SDK 등)로 번역하는 구현체다. Feature가 "이런 기능이 필요해"라고 선언한 계약서를 받아서 "이 기술로 해줄게"라고 실행한다. 클래스명은 기술명 + 인터페이스명으로 짓는다(예: `UnityNetSession`, `PhotonNetSession`). 외부 기술을 교체할 때 이 폴더의 클래스만 교체하면 Feature 코드는 수정하지 않아도 된다.

#### App/EntryPoints — Driving Adapter(밖에서 안으로)

외부 입력(UI 클릭, 네트워크 콜백, 충돌 이벤트 등)이 도메인으로 들어오는 진입점이다. Unity의 MonoBehaviour가 주로 여기에 위치한다. 역할은 오직 UseCase를 호출하는 것뿐이며, 비즈니스 로직을 넣지 않는다. 예를 들어 `MenuCanvas`는 버튼 클릭 이벤트를 받아 `StartHostUseCase.Execute()`를 호출할 뿐, 호스트 시작의 구체적인 로직은 모른다.

#### App/Composition — Composition Root(DI 조립)

LifetimeScope들이 모이는 곳이다. 전체 시스템에서 유일하게 모든 레이어를 알 수 있으며, "누가 뭘 만들어서 누구에게 전달하느냐"를 결정한다. Port와 Adapter를 바인딩하고, UseCase에 필요한 의존성을 연결한다. Root(전역 싱글턴)와 씬별 LifetimeScope로 스코프를 나눈다.

---

## 데이터 흐름

```
[Unity 이벤트] → EntryPoints → UseCase → Port(인터페이스) → Adapters → [외부 라이브러리]
                 (Driving)      (도메인)                      (Driven)
```

실제 코드 흐름:

```
버튼 클릭 → MenuCanvas → StartHostUseCase → INetSession → UnityNetSession → Netcode
```

EntryPoints는 "밖에서 안으로", Adapters는 "안에서 밖으로". 둘 다 외부 기술과의 접점이지만 방향이 반대다.

---

## Adapter 교체 예시

```
Features/Lobby/Ports/INetSession        ← "이런 기능이 필요해" (계약서)
App/Adapters/UnityNetSession            ← "Netcode로 해줄게" (현재 구현)
App/Adapters/PhotonNetSession           ← "Photon으로 해줄게" (미래 교체 가능)
App/Composition/RootLifetimeScope       ← 둘 중 뭘 쓸지 여기서 결정
```

교체 시 Feature 코드는 한 줄도 건드리지 않는다. Composition의 바인딩만 변경하면 된다.

---

## 개발 규칙

### 참조 방향

- 참조 방향은 `App → Features → Entities` 단방향이다. 역방향은 절대 불가.
- 같은 레이어의 Feature 슬라이스끼리 직접 참조하지 않는다.
- Feature 간 통신이 필요하면 `Entities`(공유 모델)를 통하거나, `App` 레이어에서 조합한다.
- `Entities`는 어디에도 의존하지 않는다. 순수 C# 클래스, Unity API 의존 최소화.

### Feature 내부

- Feature 내부는 `Application`(유스케이스) + `Ports`(인터페이스)로 구성한다.
- Port 인터페이스는 Feature 안에 선언하고, 구현(Adapter)은 `App/Adapters`에 둔다.
- UseCase는 Port 인터페이스에만 의존한다. 구체 구현 클래스를 직접 참조하지 않는다.
- UseCase는 하나의 공개 메서드(`Execute`)만 갖는다. 하나의 유스케이스 = 하나의 행위.

### 타워디펜스 실용 기준

- **UseCase로 만들 것** — 타워 배치, 타워 업그레이드, Wave 시작, 게임 포기처럼 사용자 액션에 의한 일회성 명령.
- **UseCase로 만들지 않을 것** — 적 이동, 투사체 충돌, 타워 타겟팅 같은 매 프레임 실행되는 게임플레이 로직. Feature 내부에 시스템 클래스로 둔다.
- **Port/Adapter로 분리할 것** — 네트워크, 저장, 광고, IAP 등 외부 기술 교체 가능성이 있는 부분만.

---

## 코드 규칙

| 폴더                       | using 참조                                 | DI 주입 (Construct)                                        | MonoBehaviour        | 외부 라이브러리 |
|--------------------------|------------------------------------------|----------------------------------------------------------|----------------------|----------|
| **Entities**             | 없음                                       | 없음                                                       | X                    | X        |
| **Features/Ports**       | Entities만                                | 없음                                                       | X                    | X        |
| **Features/Application** | Ports + Entities                         | Ports 인터페이스만 (생성자 주입)                                    | X                    | X        |
| **App/Adapters**         | Ports + Entities + 외부 라이브러리 + App/Config | Ports 구현에 필요한 것 (생성자 주입)                                 | X                    | O        |
| **App/EntryPoints**      | UseCase + Entities + 외부 라이브러리            | UseCase(행위) + Port 인터페이스(상태 조회) + 다른 EntryPoint(이벤트 구독용) | O                    | O        |
| **App/Config**           | Entities                                 | 없음 (ScriptableObject)                                    | X (SO 상속)            | X        |
| **App/Composition**      | 모든 레이어                                   | 없음 (등록만 함)                                               | X (LifetimeScope 상속) | O        |

---

## 기능 구현 시 체크리스트

### 1단계: 기능 분류

- [ ] 이 기능은 기존 Feature에 추가되는가?
  - Yes → 해당 Feature 폴더에 추가
  - No → 다음 질문
- [ ] 새로운 Feature인가?
  - Yes → `Features/{기능명}/` 폴더 생성, 내부에 `Application/`, `Ports/` 생성
- [ ] 여러 Feature가 공유하는 데이터 모델인가?
  - Yes → `Entities/`에 배치

### 2단계: 외부 기술 판단

- [ ] 이 기능이 외부 라이브러리나 Unity 엔진 API에 의존하는가? (Netcode, 파일 저장, 광고 SDK 등)
  - Yes → 다음 단계 진행
  - No → Port/Adapter 없이 Feature 내부에서 순수 C#으로 구현
- [ ] `Features/{기능}/Ports/`에 인터페이스를 선언했는가?
  - 인터페이스 이름은 기능의 역할을 나타내야 한다 (예: `INetSession`, `IWaveManager`)
  - 메서드 시그니처에 외부 라이브러리 타입이 노출되지 않는가?
- [ ] `App/Adapters/{기술분류}/`에 구현 클래스를 작성했는가?
  - 클래스명은 기술명 + 인터페이스명 (예: `UnityNetSession`, `UnityWaveManager`)

### 3단계: UseCase 판단

- [ ] 사용자 액션에 의한 일회성 명령인가? (버튼 클릭, 이벤트 트리거 등)
  - Yes → `Features/{기능}/Application/`에 UseCase 작성
  - UseCase 이름은 동사 + 명사 + UseCase (예: `StartHostUseCase`, `PlaceTowerUseCase`)
  - `Execute()` 메서드 하나만 공개
- [ ] 매 프레임 또는 주기적으로 실행되는 게임플레이 로직인가?
  - Yes → Feature 내부에 시스템 클래스로 작성 (UseCase로 감싸지 않음)

### 4단계: EntryPoint 판단

- [ ] Unity 이벤트를 받아야 하는가? (UI 클릭, 충돌, 생명주기 콜백 등)
  - Yes → `App/EntryPoints/{분류}/`에 MonoBehaviour 작성
  - EntryPoint는 UseCase를 호출하는 역할만. 비즈니스 로직을 넣지 않는다
- [ ] Unity 이벤트가 필요 없는가?
  - Yes → 순수 C# 클래스로 유지

### 5단계: DI 등록

- [ ] 이 클래스의 생명주기는?
  - 전역 (씬 전환에도 유지) → `RootLifetimeScope`에 등록
  - 특정 씬에서만 유효 → 해당 씬의 LifetimeScope에 등록
- [ ] 인터페이스가 있는 Adapter인가?
  - Yes → `.As<인터페이스>()` 형태로 등록
- [ ] MonoBehaviour인가?
  - Yes → `RegisterComponentInHierarchy` 또는 `RegisterComponentInNewPrefab` 사용

### 6단계: 참조 방향 최종 검증

- [ ] `Entities` 클래스가 다른 레이어를 참조하지 않는가?
- [ ] `Features` 클래스가 Entities만 참조하는가?
- [ ] `Features` 클래스가 다른 Feature 슬라이스를 참조하지 않는가?
- [ ] `App` 클래스가 Features/Entities를 참조하는가? (역방향 없는가?)
- [ ] Adapter 클래스가 Port 인터페이스 외에 다른 Feature 내부를 참조하지 않는가?
