---
title: "Cube Defence"
date: 2026-01-08 00:00:00
categories: [Project, Unity]
tags: []
description: ""
---

### 프로토 타입 개발 설정

- 필드 만들기 CubeField - Surface - Quad
- Raycast 최적화
- SpawnManager & Spline 적용
- 몬스터 테스트
- Unity InputActions 활용

![img](/assets/post/cube_defence/cube_defence_prototype.png)

### 타워 구현

- 유니티 인풋 액션과 바인딩 설정

![img](/assets/post/cube_defence/cube_defence_prototype_1.png)

### 타워 공격 구현

- 발사체 스폰 매니저
- 발사체 오브젝트 풀링

![img](/assets/post/cube_defence/cube_defence_prototype_2.png)

### 핵심 구현 포인트

- 타워 스크립트

```csharp
private void Update()
        {
            _attackCool += Time.deltaTime;
            if (_attackCool > _attackSpeed)
            {
                Attack();
                _attackCool = 0f;
            }
        }

private void Attack()
{
    int numHits = Physics.OverlapSphereNonAlloc(transform.position, 3, _hits, _layerMask);
    if (numHits > 0)
    {
        pool.Spawn(transform.position, _hits[0].transform, 10);
    }
}
```

> 타워 오브젝트는 3의 범위내에 _attackCool 공격속도동안 10의 속도를 가지는 총알을 발사
> 
> 거리기반 시간이 지나면 적오브젝트에 데미지 호출

```csharp
public void AttackSpawn(Vector3 position, Transform target, float attackSpeed)
        {
            _compareTime = 0f;
            _lifeTime = Vector3.Distance(position, target.position) / attackSpeed;
            transform.position = position;
            Target = target;
        }

private void Update()
{
    if (Target == null)
    {
        if (gameObject.activeSelf) Pool.Release(this);
        
        return;
    }

    if (_lifeTime <= _compareTime)
    {
        Pool.Release(this);
        return;
    }

    _compareTime += Time.deltaTime;
    float t = Mathf.Clamp01(_compareTime / _lifeTime);
    transform.position = Vector3.Lerp(transform.position, Target.position, t);
}
```

> 풀링으로 스폰된 총알은 공격시점 적의 오브젝트 위치와 발사시점 자기 위치의 거리기반 도착 시간 계산
> 
> 도착 시간 만큼 Lerp 하며 공격 연출만 수행
> 
> 도착시간이 끝나면 총알은 자동 풀 반환
