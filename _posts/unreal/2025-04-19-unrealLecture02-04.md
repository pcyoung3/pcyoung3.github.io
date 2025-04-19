---
layout: single
title: 이득우Framework_04_캐릭터 애니메이션 설정
categories: unrealLecture02
tag: [unreal, unrealLecture]

toc: true
toc_sticky: true
---
## 1. 전체 구조
![Image](https://github.com/user-attachments/assets/3fae9847-8fa6-424d-9f8e-53343d5fd21c)

1. C++에서 UABAnimInstance를 상속하고 필요한 데이터를 선언 및 정의
2. Animation Blueprint에서 만든 AnimInstance 기반의 C++ 을 상속하고 본격적인 애니메이션을 정의

   
   

## 2. ABP 구조
![Image](https://github.com/user-attachments/assets/da9589a4-b013-43cf-8786-452c9568039b)

1. ABP 내부에서 State machine을 만듦
2. State Machine 내에서 State를 생성하고 애니메이션을 지정
3. 필요에 따라 State Alias를 사용해서 State를 지정(if문이라고 생각하면 편함)

   
   

## 3. AnimInstance C++ 생성
![Image](https://github.com/user-attachments/assets/82f8976f-2d15-487c-9350-c5847196fab3)

```cpp
//Header
UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Character)
TObjectPtr<class ACharacter> Owner;

UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Character)
TObjectPtr<class UCharacterMovementComponent> Movement;

//Cpp
Owner = Cast<ACharacter>(GetOwningActor());
if (Owner)
{
	Movement = Owner->GetCharacterMovement();
}
```
다음과 같이 Character를 가져오고 반대로 Character에서 Animation을 가져올 수 있다

   

생성 시 다음 함수를 기억
{: .notice--success}

```cpp
// Animation init 시 호출
virtual void NativeInitializeAnimation() override;

// Animation 매 프레임 호출
virtual void NativeUpdateAnimation(float DeltaSeconds) override;
```

   
   

## 4. ABP 상세설정
### 4-1. State Machine
![Image](https://github.com/user-attachments/assets/4b82a91b-7356-4d86-a9cc-db9ee65ee2fc)<br>
1. 가장 바깥쪽인 AnimGraph에서 생성가능
2. 결과물을 Cache로 만들어 낼 수 있다.
	1. New Save Cache Pose 로 저장
	2. Use cached pose 로 사용 가능

   

### 4-2. Bland Space(1D)
1. Animation > Legacy > Bland Space 1D로 생성<br>
![Image](https://github.com/user-attachments/assets/b6cb0a0d-22a7-4e60-a575-0daa2bd26518)

2. 간선에 몇개의 애니메이션을 추가해서 특정 값에 따라 Blend 가능<br>
![Image](https://github.com/user-attachments/assets/b37819fb-f51e-4856-abec-2f59ac411620)

3. 특정 값은 Horizontal Axis에서 설정 가능하다

### 4-3. State
1. State Machine을 만들면 그 안에서 State를 생성할 수 있다.<br>
![Image](https://github.com/user-attachments/assets/abc42b08-ca5b-4bc3-9134-30610d730c16)

2. State 내부에서는 Blue Print로 작업 가능<br>
![Image](https://github.com/user-attachments/assets/a2914713-96ef-4c0d-b277-7a8916f581a0)

Apply Additive를 사용하면 두 애니메이션을 합칠 수 있음
{: .notice--warning}
![Image](https://github.com/user-attachments/assets/1a582e47-f5e8-48f8-a79a-41e7d59df5f8)

### 4-4. State Alias
State Alias를 사용하여 상태를 지정하여 해당 상태일 때 조건을 만족하면 다른 상태로 넘어가게 할 수 있다.

1. 노드 생성<br>
![Image](https://github.com/user-attachments/assets/2d68284c-7af4-4323-80ef-b850a8ab28fd)

2. 진입점(Locomotion 상태에서만 들어올 수 있음)<br>
![Image](https://github.com/user-attachments/assets/72f17a20-1bf1-4cbf-bd60-c65e012b5188)

3. 이동 조건 처리(bIsJumping 이 True 면 상태 이동)<br>
![Image](https://github.com/user-attachments/assets/16958266-9142-4911-b627-e2b4953fbf46)<br>
조건을 만족하지 않으면 State가 변경되지 않음
