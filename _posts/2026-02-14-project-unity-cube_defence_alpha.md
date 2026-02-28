---
title: "Cube Defence - Alpha"
date: 2026-02-14 00:00:00
categories: [Project, Unity]
tags: [CubeDefence]
description: ""
---

# CubeDefence Alpha 버전 분석

---

## 1. 알파 버전의 아키텍처 구조

### 전체 평가

```text
구현 시점: 
1. 메뉴씬에서 멀티 연결후 게임씬으로 이동 
2. 게임을 시작하는 버튼클릭후 각 라인에서 몬스터 생성
3. 플레이어는 타워를 건설하면 타워는 공격범위 안의 몬스터 공격,
4. 발사체와 몬스터 타워는 풀링 생성 관리

평가: 
리팩토링을 통해 알파 버전 1을 개선하고자 알파 버전 2를 새로 진행했으나, 구현이 진행된 시점부터 설계 구조의 복잡도가 급격히 증가했다.
오브젝트별 폴더 기준으로 설계된 아키텍처에서 의존성/참조 규칙이 누적되며 구조적 제약이 커졌고, 리팩토링만으로는 근본 문제를 해소하기 어려워 개발을 중단했다.
```

### 폴더 구조

```
CubeDefence_alpha/
├── Core/                        # 인터페이스 모음
│   ├── ICubeFieldEntryPoint.cs
│   ├── IGameSystemEntryPoint.cs
│   ├── INetworkManagerEntryPoint.cs
│   └── IPlayerEntryPoint.cs
├── GameCanvas/                  # 게임오브젝트 단위 폴더
│   └── GameCanvas.cs
├── GameCubeField/
│   ├── CubeFieldRoute.cs
│   └── GameSurfaceUnit.cs
├── GameLifetimeScope/
│   └── GameLifetimeScope.cs
├── GamePlayerInputAction/
│   ├── GamePlayerInputAction.cs
│   └── GameRayAction.cs
├── GameSystem/
│   └── GameSystemRoute.cs
├── MenuCanvas/
│   └── MenuCanvas.cs
├── RootLifetimeScope/
│   └── RootLifetimeScope.cs
├── RootNetworkManager/
│   └── NetworkManagerRoute.cs
└── RootPlayerAvatar/
    └── PlayerAvatarRoute.cs
```

### 구조적 특징

알파 버전은 **게임오브젝트 단위로 폴더가 나뉘어** 있다. GameCanvas, GameCubeField, GamePlayerInputAction 등 Unity Hierarchy에 있는 오브젝트 이름이 그대로 폴더명이 된다. 인터페이스는 Core 폴더에 모여 있지만, 이 인터페이스들이 실제로 어디서 구현되고 어디서 사용되는지는 폴더 구조만으로 파악할 수 없다.

---

## 2. 스크립트 의존성 문제

### 문제 1: Core 인터페이스가 외부 라이브러리를 노출한다

```csharp
// Core/INetworkManagerEntryPoint.cs
public interface INetworkManagerEntryPoint
{
    NetworkManager GNetworkManager { get; }  // ← Netcode 타입이 인터페이스에 직접 노출
}
```

`INetworkManagerEntryPoint`가 `NetworkManager`(Netcode 타입)를 그대로 반환한다. 인터페이스를 만든 의미가 사라진다. 인터페이스를 거쳐도 결국 Netcode에 직접 접근하게 되므로, 네트워크 라이브러리를 교체하면 Core 인터페이스와 이를 사용하는 모든 스크립트를 수정해야 한다.

### 문제 2: 인터페이스를 통해 외부 라이브러리에 간접 의존한다

```csharp
// GameRayAction.cs
networkManagerEntryPoint.GNetworkManager.IsClient  // 인터페이스를 거쳐서 Netcode 직접 사용

// GameCanvas.cs
networkManagerEntryPoint.GNetworkManager.IsHost     // 동일한 패턴 반복

// CubeFieldRoute.cs
_networkManagerEntryPoint.GNetworkManager.IsServer  // 동일한 패턴 반복

// GameSystemRoute.cs
_networkManagerEntryPoint.GNetworkManager.LocalClient.PlayerObject.GetComponent<PlayerAvatarRoute>()
```

거의 모든 스크립트가 `INetworkManagerEntryPoint`를 주입받아 `.GNetworkManager`로 `NetworkManager`에 직접 접근한다. 인터페이스가 추상화 역할을 하지 못하고 **NetworkManager를 전달하는 통로**로만 사용되고 있다.

### 문제 3: MenuCanvas가 DI 없이 전역 싱글턴에 직접 접근한다

```csharp
// MenuCanvas.cs
public void StartHost()
{
    NetworkManager.Singleton.StartHost();          // 전역 싱글턴 직접 참조
}

public void GameStart()
{
    NetworkManager.Singleton.SceneManager.LoadScene("GameScene_alpha", LoadSceneMode.Single);
}
```

`MenuCanvas`는 VContainer를 사용하지 않고 `NetworkManager.Singleton`에 직접 접근한다. 다른 스크립트들은 DI를 사용하는데 `MenuCanvas`만 싱글턴 패턴을 사용하고 있어 일관성이 없다.

### 문제 4: GameSystemRoute가 구체 클래스를 직접 참조한다

```csharp
// GameSystemRoute.cs
private PlayerAvatarRoute _localPlayer;  // 구체 클래스 직접 참조

private void Start()
{
    _localPlayer = _networkManagerEntryPoint.GNetworkManager
        .LocalClient.PlayerObject.GetComponent<PlayerAvatarRoute>();  // 구체 클래스에 의존
}
```

### 의존성 그래프

```
MenuCanvas ──── NetworkManager.Singleton (전역 직접 참조)

GameCanvas ──┬── INetworkManagerEntryPoint ──→ NetworkManager (직접 노출)
             └── IGameSystemEntryPoint ──→ GameSystemRoute

GameRayAction ──┬── INetworkManagerEntryPoint ──→ NetworkManager (직접 노출)
                └── ICubeFieldEntryPoint ──→ CubeFieldRoute

CubeFieldRoute ──── INetworkManagerEntryPoint ──→ NetworkManager (직접 노출)

GameSystemRoute ──┬── INetworkManagerEntryPoint ──→ NetworkManager (직접 노출)
                  ├── ICubeFieldEntryPoint ──→ CubeFieldRoute
                  └── PlayerAvatarRoute (구체 클래스 직접 참조)

GamePlayerInputAction ──── INetworkManagerEntryPoint ──→ NetworkManager (직접 노출)
```

결과적으로 거의 모든 스크립트가 `INetworkManagerEntryPoint`를 통해 `NetworkManager`에 도달하며, 인터페이스가 추상화 역할을 하지 못한다.

---

## 3. 알파 버전의 복잡도 증가 원인

### 원인 1: 게임오브젝트 = 폴더 = 스크립트 단위 설계

폴더가 게임오브젝트 단위(`GameCanvas/`, `GameCubeField/`, `GamePlayerInputAction/`)로 나뉘어 있어, **기능의 흐름이 아닌 오브젝트의 물리적 구조**로 코드가 분류된다. "호스트 시작 기능은 어디에 있는가?"라는 질문에 `MenuCanvas/MenuCanvas.cs`를 열어보기 전까지 알 수 없다. 기능이 추가될수록 어떤 오브젝트가 어떤 기능을 담당하는지 파악하기 어려워진다.

### 원인 2: Core 인터페이스가 역할이 아닌 오브젝트 기준으로 설계됨

```
ICubeFieldEntryPoint       ← "큐브 필드 오브젝트가 뭘 가지고 있는가"
IGameSystemEntryPoint      ← "게임 시스템 오브젝트가 뭘 할 수 있는가"
INetworkManagerEntryPoint  ← "네트워크 매니저 오브젝트에 접근하기"
IPlayerEntryPoint          ← "플레이어 오브젝트가 뭘 가지고 있는가"
```

인터페이스가 **기능의 계약**이 아니라 **오브젝트의 프로퍼티 접근자** 역할을 한다. 예를 들어 `INetworkManagerEntryPoint`는 "네트워크 세션을 시작한다"는 행위 계약이 아니라, "NetworkManager 인스턴스를 반환한다"는 데이터 접근 통로일 뿐이다.

### 원인 3: 모든 스크립트가 NetworkManager를 경유해야 한다

`INetworkManagerEntryPoint`를 주입받아 `.GNetworkManager`로 네트워크 상태를 확인하는 패턴이 거의 모든 스크립트에 반복된다. 15개 스크립트 중 7개가 `INetworkManagerEntryPoint`를 주입받거나 `NetworkManager.Singleton`을 직접 사용한다. 네트워크 관련 로직이 모든 곳에 스며들어 있어, 네트워크와 무관한 로직을 분리할 수 없다.

### 원인 4: 구현과 인터페이스의 혼재

`CubeFieldRoute`는 `ICubeFieldEntryPoint`를 구현하면서 동시에 `INetworkManagerEntryPoint`를 주입받는다. 하나의 클래스가 인터페이스 제공자이면서 다른 인터페이스의 소비자 역할을 동시에 하고 있어, **누가 누구에게 의존하는지** 방향이 불명확하다.

```csharp
// CubeFieldRoute는 ICubeFieldEntryPoint를 구현 (제공자)
public sealed class CubeFieldRoute : MonoBehaviour, ICubeFieldEntryPoint

// 동시에 INetworkManagerEntryPoint를 주입받음 (소비자)
[Inject]
private void Construct(INetworkManagerEntryPoint networkManagerEntryPoint)
```

### 원인 5: 새 기능 추가 시 여러 곳을 수정해야 한다

새로운 게임오브젝트를 추가하려면:

1. 새 폴더 생성
2. MonoBehaviour 스크립트 작성
3. Core에 인터페이스 추가 (필요 시)
4. `INetworkManagerEntryPoint`를 주입받아 네트워크 상태 확인 코드 추가
5. LifetimeScope에 등록

어디서든 `NetworkManager`에 접근해야 하므로 새 기능이 추가될 때마다 네트워크 의존성이 자동으로 따라온다.
