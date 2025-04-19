---
layout: single
title: 이득우Framework_03_캐릭터 컨트롤 설정
categories: unrealLecture02
tag: [unreal, unrealLecture]

toc: true
toc_sticky: true
---
## 1. 에디터 Play 에서 수치확인
```
DisplayAll PlayerController ControlRotation
```
![Image](https://github.com/user-attachments/assets/2fa3edcb-f327-4032-9bed-a4aa60ef4c0a)
![Image](https://github.com/user-attachments/assets/272a40f2-db85-4cc5-9aa2-0b18282b978f)

   
   

## 2. Move, Look 함수 분석
```cpp
//Contorller의 회전값을 Update
AddControllerYawInput(LookAxisVector.X);
AddControllerPitchInput(LookAxisVector.Y);
```

```cpp
//특정 방향으로 이동시키는 함수
AddMovementInput(ForwardDirection, MovementVector.X);
AddMovementInput(RightDirection, MovementVector.Y);
```

   
   

## 3. Controller 옵션
<div class="notice--danger" markdown="1">
**<u>사전준비</u>** 

만들어놓은 CharacterPlayer를 상속시킨 Blueprint에서 옵션을 볼 수 있다
</div>

### 3-1. controller의 회전값과 Pawn의 회전값을 동기화
![Image](https://github.com/user-attachments/assets/37cd7283-1c15-407f-bdc6-f00e9a3d3519)

   

### 3-2. controller의 회전값과 spring Arm의 회전값을 동기화
![Image](https://github.com/user-attachments/assets/d52f0419-5fa6-4149-9b26-3fb9875a2b03)

Use Pawn Control Rotation : controller와 회전값 동기화
Inherit ~ : 부모로부터 받은 회전값을 적용

   

![Image](https://github.com/user-attachments/assets/0c17fcee-8b72-497f-a010-ad12191b3b21)

카메라의 충돌이 발생했을 때 카메라를 당겨주는 옵션

   

![Image](https://github.com/user-attachments/assets/94037dbb-eff2-4e7f-bbe9-f7bfe26b059d)

Use Pawn Control Rotation -> controller의 회전값을 사용할 것인지?

   
   

## 4. Data Asset
Controller나 Movement에 쓰일 여러 데이터 수치들을 모아놓을 수 있다
{: .notice--info}

### 4-1. PrimaryDataAsset 상속 받아 Class 생성
![Image](https://github.com/user-attachments/assets/5ec74087-c778-48ec-ba13-0370ff192dda)

   

### 4-2. 원하는 데이터를 삽입
```cpp
//ABCharacterControlData.h
UCLASS()
class ARENABATTLE_API UABCharacterControlData : public UPrimaryDataAsset
{
	...
	UPROPERTY(EditAnywhere, Category = CharacterMovement)
	uint32 bUseControllerDesiredRotation : 1;
	
	UPROPERTY(EditAnywhere, Category = CharacterMovement)
	FRotator RotationRate;
	
	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Input)
	TObjectPtr<class UInputMappingContext> InputMappingContext;
	...
};
```
에디터에서 설정할 데이터들을 Header에 정의

   

### 4-3. 만든 Class 상속시켜 Data Asset 생성
Miscellaneous > Data Asset<br>
![Image](https://github.com/user-attachments/assets/38049c27-8102-4fc7-abf0-3cc895d9140a)

   

### 4-4. 원하는 데이터 셋팅
![Image](https://github.com/user-attachments/assets/3eda7eb6-987f-487f-be93-1e6f8e5742d5)

   

### 4-5. C++ 셋팅
```cpp
//헤더파일
//Enum셋팅 뒤 Map 으로 저장
UPROPERTY(EditAnywhere, Category = CharacterControl, Meta = (AllowPrivateAccess = "true"))
TMap<ECharacterControlType, class UABCharacterControlData*> CharacterControlManager;

//CDO
static ConstructorHelpers::FObjectFinder<UABCharacterControlData> ShoulderDataRef(TEXT("/Script/ArenaBattle.ABCharacterControlData'/Game/ArenaBattle/CharacterControl/ABC_Shoulder.ABC_Shoulder'"));
if (ShoulderDataRef.Object)
{
	CharacterControlManager.Add(ECharacterControlType::Shoulder, ShoulderDataRef.Object);
}

static ConstructorHelpers::FObjectFinder<UABCharacterControlData> QuaterDataRef(TEXT("/Script/ArenaBattle.ABCharacterControlData'/Game/ArenaBattle/CharacterControl/ABC_Quater.ABC_Quater'"));
if (QuaterDataRef.Object)
{
	CharacterControlManager.Add(ECharacterControlType::Quater, QuaterDataRef.Object);
}
```

   

### 4-6. 사용
```cpp
SetCharacterControl(ECharacterControlType::Shoulder);
```
```cpp
void AABCharacterPlayer::SetCharacterControl(ECharacterControlType NewCharacterControlType)
{
	UABCharacterControlData* NewCharacterControl = CharacterControlManager[NewCharacterControlType];
	check(NewCharacterControl);

	SetCharacterControlData(NewCharacterControl);

	APlayerController* PlayerController = CastChecked<APlayerController>(GetController());

	//Input Mapping Context 변경
	if (UEnhancedInputLocalPlayerSubsystem* Subsystem = 
		ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(PlayerController->GetLocalPlayer()))
	{
		//기존에 존재하던 Mapping Clear
		Subsystem->ClearAllMappings();
		UInputMappingContext* NewMappingContext = NewCharacterControl->InputMappingContext;
		if (NewMappingContext)
		{
			Subsystem->AddMappingContext(NewMappingContext, 0);
		}
	}

	CurrentCharacterControlType = NewCharacterControlType;
}
```

```cpp
void AABCharacterPlayer::SetCharacterControlData(const UABCharacterControlData* CharacterControlData)
{
	// Pawn
	bUseControllerRotationYaw = CharacterControlData->bUseControllerRotationYaw;
	// CharacterMovement
	GetCharacterMovement()->bOrientRotationToMovement = CharacterControlData->bOrientRotationToMovement;
	...
	//Camera
	CameraBoom->TargetArmLength = CharacterControlData->TargetArmLength;
	CameraBoom->SetRelativeRotation(CharacterControlData->RelativeRotation);
	...
}
```

   

### 4-7. 정리
![Image](https://github.com/user-attachments/assets/f12347c4-8fba-4989-ba5c-1fbfdc933f04)