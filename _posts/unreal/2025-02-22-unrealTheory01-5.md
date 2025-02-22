---
layout: single
title: 프로젝트 셋팅 참고
categories: unrealTheory
tag: [unreal]

toc: true
toc_sticky: true
---

## 1. 프로젝트 기본 셋팅
### 1-1. Level 생성 및 설정
### 1-2. Gamemode 생성
### 1-3. Player 생성
### 1-4. Blueprint로 Gamemode 및 Player 상속 후 생성

UPROPERTY 참고
{: .notice--warning} 

```
VisibleAnywhere : 블루프린트 에디터, Hierarchy에서 값을 확인가능
EditAnywhere : 블루프린트 에디터, Hierarchy에서 값을 확인 및 수정 가능
BlueprintReadWrite : 블루프린트에서 해당 변수를 읽고, 쓸 수 있음
Category=Camera : Editor의 Detail 패널에서 해당 이름으로 카테고리 명칭가능
meta = (AllowPrivateAccess = "true") : private 변수여도 Editor에서 보이게 함
```

   
   

## 2. Player 기본셋팅
### 2-1. Player에 Camera Arm 추가
### 2-2. Player에 Camera Component 추가
### 2-3. Camera, Camera Arm 셋팅
### 2-4. Player Movement 기본 셋팅
   
이동과 관련된 bool값 참고
{: .notice--warning} 

```cpp
CameraArm->bUsePawnControlRotation = true; //Controller의 회전을 카메라 암에 적용시킬것인가?
Camera->bUsePawnControlRotation = false; //Controller의 회전을 카메라에 적용시킬것인가?

//Controller의 회전을 Pawn에 적용시킬 것인가?
bUseControllerRotationPitch = false;
bUseControllerRotationYaw = false;
bUseControllerRotationRoll = false;

//Controller 방향으로 Pawn을 천천히 회전시킬 것인가?
GetCharacterMovement()->bUseControllerDesiredRotation = true;
//Pawn의 이동방향으로 Pawn을 회전시킬 것인가?
GetCharacterMovement()->bOrientRotationToMovement = true;
```

   
   
## 3. Enhanced Input 
### 2-1. Input Action 생성
### 2-2. Input Mapping Context 생성 후 키 할당
### 2-3. Being Player 에서 Player Controller로 SubSystem을 이용한 AddMappingContext
### 2-4. SetupPlayerInputComponent에서 Input Action과 함수 Binding
### 2-5. Binding된 함수들 기능 설정

1) 이동
![Image](https://github.com/user-attachments/assets/31370c63-f89f-44e7-9c5b-1e744a26f83e)

```cpp
void AReview1Player::Move(const FInputActionValue& Value)
{
	FVector2D MovementVector = Value.Get<FVector2D>();
	if (MovementVector.X != 0)	// WS
	{
		FRotator Rotation = GetControlRotation();
		FVector Direction = UKismetMathLibrary::GetForwardVector(FRotator(0, Rotation.Yaw, 0));
		AddMovementInput(Direction, MovementVector.X);
	}

	if (MovementVector.Y != 0)	//AD
	{
		FRotator Rotation = GetControlRotation();
		FVector Direction = UKismetMathLibrary::GetRightVector(FRotator(0, Rotation.Yaw, 0));
		AddMovementInput(Direction, MovementVector.Y);
	}
}
```

2) 회전
```cpp
void AReview1Player::Look(const FInputActionValue& Value)
{
	FVector2D LookAxisVector = Value.Get<FVector2D>();

	if(LookAxisVector.X != 0)
		AddControllerYawInput(LookAxisVector.X);

	if (LookAxisVector.Y != 0)
		AddControllerPitchInput(-LookAxisVector.Y);
}
```
