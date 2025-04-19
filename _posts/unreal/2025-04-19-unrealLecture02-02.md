---
layout: single
title: 이득우Framework_02_캐릭터와 입력 시스템
categories: unrealLecture02
tag: [unreal, unrealLecture]

toc: true
toc_sticky: true
---
   
## 1. 액터의 구조
* 다수의 컴포넌트를 가지고 있다
* 대표하는 Component를 Root Component라고 함
* Actor의 transform은 Root Component의 Transform과 같다

   
   

## 2. 액터 CDO 셋팅
### 2-1. Header 셋팅
![Image](https://github.com/user-attachments/assets/c47130b7-46ea-4e68-9848-ec03b1b3d275)

```cpp
//객체,에디터에서 확인가능 = VisibleAnyWhere
//블루프린트에서 읽고 쓸 수 있음 = BlueprintReadWrite
//Category = Mesh : UHT에서 자동으로 mesh타입임을 명시해주고 mesh 카테고리에 편집할 수 있게 제공
UPROPERTY(VisibleAnyWhere, BlueprintReadWrite, Category = Mesh)
TObjectPtr<class UStaticMeshComponent> Body;
```

   

### 2-2. CDO Component 추가 및 셋팅
```cpp
//CDO에서 추가할 때 CreateDefaultSubobject 사용
Body = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Body"));
Water = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Water"));

RootComponent = Body;	//Root Component 지정
Water->SetupAttachment(Body);	//Attachment 지정
Water->SetRelativeLocation(FVector(0.f, 0.f, 132.f));	//Relative 좌표 지정

//Static Mesh 불러오기 - copy reference에서 추가적으로 안지워도 됨
static ConstructorHelpers::FObjectFinder<UStaticMesh> BodyMeshRef(TEXT("/Script/Engine.StaticMesh'/Game/ArenaBattle/Environment/Props/SM_Plains_Castle_Fountain_01.SM_Plains_Castle_Fountain_01'"));
if (BodyMeshRef.Object)
{
	Body->SetStaticMesh(BodyMeshRef.Object);
}

static ConstructorHelpers::FObjectFinder<UStaticMesh> WaterMeshRef(TEXT("/Script/Engine.StaticMesh'/Game/ArenaBattle/Environment/Props/SM_Plains_Fountain_02.SM_Plains_Fountain_02'"));
if (WaterMeshRef.Object)
{
	Water->SetStaticMesh(WaterMeshRef.Object);
}
```

   
   

## 3. BluePrint 에 상속
BluePrint 객체에서 C++ 파일을 상속받을 수 있음

1. Class Settings<br>
![Image](https://github.com/user-attachments/assets/07a91707-a154-415c-9069-4274cdff2353)

2. Parent Class 에서 만든 c++ 파일을 지정<br>
![Image](https://github.com/user-attachments/assets/900fe334-f2b6-4178-9073-bd8147635d7c)

   
   

## 4. 폰과 캐릭터의 기능
### 4-1. Pawn
![Image](https://github.com/user-attachments/assets/7874bdb6-0c90-454d-b8c0-c0638b287b3c)
* Transform이 있는 Component는 SceneComponent라고 함

   

### 4-2. Character
![Image](https://github.com/user-attachments/assets/f932248d-7419-4008-a1ee-1d1efc8421a8)

   

### 4-3. Character Base 설정
```cpp
// Pawn
bUseControllerRotationPitch = false;
bUseControllerRotationYaw = false;
bUseControllerRotationRoll = false;

// Capsule
GetCapsuleComponent()->InitCapsuleSize(42.f, 96.0f);
GetCapsuleComponent()->SetCollisionProfileName(TEXT("Pawn"));

// Movement
GetCharacterMovement()->bOrientRotationToMovement = true;
GetCharacterMovement()->RotationRate = FRotator(0.0f, 500.0f, 0.0f);
GetCharacterMovement()->JumpZVelocity = 700.f;
GetCharacterMovement()->AirControl = 0.35f;
GetCharacterMovement()->MaxWalkSpeed = 500.f;
GetCharacterMovement()->MinAnalogWalkSpeed = 20.f;
GetCharacterMovement()->BrakingDecelerationWalking = 2000.f;

// Mesh
GetMesh()->SetRelativeLocationAndRotation(FVector(0.0f, 0.0f, -100.0f), FRotator(0.0f, -90.0f, 0.0f));
GetMesh()->SetAnimationMode(EAnimationMode::AnimationBlueprint);
GetMesh()->SetCollisionProfileName(TEXT("CharacterMesh"));

static ConstructorHelpers::FObjectFinder<USkeletalMesh> CharacterMeshRef(TEXT("/Script/Engine.SkeletalMesh'/Game/Characters/Mannequins/Meshes/SKM_Quinn_Simple.SKM_Quinn_Simple'"));
if (CharacterMeshRef.Object)
{
	GetMesh()->SetSkeletalMesh(CharacterMeshRef.Object);
}

static ConstructorHelpers::FClassFinder<UAnimInstance> AnimInstanceClassRef(TEXT("/Game/Characters/Mannequins/Animations/ABP_Quinn.ABP_Quinn_C"));
if (AnimInstanceClassRef.Class)
{
	GetMesh()->SetAnimInstanceClass(AnimInstanceClassRef.Class);
}
```
모르는 함수가 있어도 우선 참고

   

### 4-4. CharacterPlayer Camera 설정
```cpp
// Camera Section
protected:
	//AllowPrivateAccess = "true" -> private 으로 선언된 Unreal Object들을 Blueprint에서도 접근가능하게 함
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera, Meta = (AllowPrivateAccess = "true"))
	TObjectPtr<class USpringArmComponent> CameraBoom;

	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera, Meta = (AllowPrivateAccess = "true"))
	TObjectPtr<class UCameraComponent> FollowCamera;
```

```cpp
// Camera
CameraBoom = CreateDefaultSubobject<USpringArmComponent>(TEXT("CameraBoom"));
CameraBoom->SetupAttachment(RootComponent);
CameraBoom->TargetArmLength = 400.0f;
CameraBoom->bUsePawnControlRotation = true;

FollowCamera = CreateDefaultSubobject<UCameraComponent>(TEXT("FollowCamera"));
FollowCamera->SetupAttachment(CameraBoom, USpringArmComponent::SocketName);	//Socket 이름 지정
FollowCamera->bUsePawnControlRotation = false;
```

   
   

## 5. Enhanced Input System
![Image](https://github.com/user-attachments/assets/ec539b91-62ae-4d9a-ab5e-f4a95f0a25d2)

다음과 같은 순서로 입력을 받게 됨

   

### 5-1. Editor에서 IMC 및 Input Action 생성
![Image](https://github.com/user-attachments/assets/b31ba55d-f26e-4cad-aae7-5ee69b362414)
![Image](https://github.com/user-attachments/assets/13da33d5-2a78-4e65-b63e-21e82e4e18d2)

* IA : Key를 어떤 값으로 취급할 것인지?(bool, vector2D 등)
* IMC : binding 된 IA를 어떤 키를 눌렀을 때 반응하게 할 것인지?(키보드 W키, 게임패드 A키 등)

   

### 5-1. Mapping Context, InputAction Header선언
```cpp
// Input Section
protected:
	//다른 에셋으로 변경할 수 있게 하기 위해 EditAnywhere로 선언
	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Input, Meta = (AllowPrivateAccess = "true"))
	TObjectPtr<class UInputMappingContext> DefaultMappingContext;

	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Input, Meta = (AllowPrivateAccess = "true"))
	TObjectPtr<class UinputAction> JumpAction;

	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Input, Meta = (AllowPrivateAccess = "true"))
	TObjectPtr<class UinputAction> MoveAction;

	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Input, Meta = (AllowPrivateAccess = "true"))
	TObjectPtr<class UinputAction> LookAction;
```

   

### 5-2. InputAction 함수 선언
```cpp
#include "InputActionValue.h"
...
void Move(const FInputActionValue& Value);
void Look(const FInputActionValue& Value);
```

```cpp
//Build.cs
...
//모듈에 EnhancedInput추가
PublicDependencyModuleNames.AddRange(new string[] { "Core", "CoreUObject", "Engine", "InputCore", "EnhancedInput" });
...
```

   

### 5-3. BeginPlay, SetupPlayerInputComponent 선언
```cpp
protected:
	//Input Mapping Context 할때 사용
	virtual void BeginPlay() override;

public:
	//입력 Action과 기능함수(Move, Look..)들을 Mapping
	virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override;
```

   

### 5-4. SetupPlayerInputComponent 에서 바인딩 코드 추가
```cpp
void AABCharacterPlayer::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
	Super::SetupPlayerInputComponent(PlayerInputComponent);

	//CastCheck : cast에 실패하면 에러 발생
	UEnhancedInputComponent* EnhancedInputComponent = CastChecked<UEnhancedInputComponent>(PlayerInputComponent);

	//Action과 키를 누르면 실제 동작하는 Function들을 Bind
	EnhancedInputComponent->BindAction(JumpAction, ETriggerEvent::Triggered, this, &ACharacter::Jump);		//Unreal 함수
	EnhancedInputComponent->BindAction(JumpAction, ETriggerEvent::Completed, this, &ACharacter::StopJumping);	//Unreal 함수
	EnhancedInputComponent->BindAction(MoveAction, ETriggerEvent::Triggered, this, &AABCharacterPlayer::Move);
	EnhancedInputComponent->BindAction(LookAction, ETriggerEvent::Triggered, this, &AABCharacterPlayer::Look);
}
```

   

### 5-5. Add Mapping Context
```cpp
void AABCharacterPlayer::BeginPlay()
{
	Super::BeginPlay();

	//CastCheck : cast에 실패하면 에러 발생
	APlayerController* PlayerController = CastChecked<APlayerController>(GetController());
	//subsystem이라는 Inputsystem을 가져와서 mapping context를 추가하는 작업
	if (UEnhancedInputLocalPlayerSubsystem* SubSystem = ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(PlayerController->GetLocalPlayer()))
	{
		SubSystem->AddMappingContext(DefaultMappingContext, 0);
		
		//Mapping Context를 삭제할 수도 있다.
		//SubSystem->RemoveMappingContext(DefaultMappingContext);
	}
}
```

   

### 5-6. Binding 된 함수들 정의
```cpp
void AABCharacterPlayer::Move(const FInputActionValue& Value)
{
	//Value가 yxz 순으로 들어온다. 이 부분은 에디터에서 변경가능
	FVector2D MovementVector = Value.Get<FVector2D>();

	const FRotator Rotation = Controller->GetControlRotation();
	const FRotator YawRatation(0, Rotation.Yaw, 0);

	const FVector ForwardDirection = FRotationMatrix(YawRatation).GetUnitAxis(EAxis::X);
	const FVector RightDirection = FRotationMatrix(YawRatation).GetUnitAxis(EAxis::Y);

	AddMovementInput(ForwardDirection, MovementVector.X);
	AddMovementInput(RightDirection, MovementVector.Y);
}

void AABCharacterPlayer::Look(const FInputActionValue& Value)
{
	FVector2D LookAxisVector = Value.Get<FVector2D>();

	AddControllerYawInput(LookAxisVector.X);
	AddControllerPitchInput(LookAxisVector.Y);
}
```

<div class="notice--info" markdown="1">
**<u>Value가 yxz 순으로 들어오는 이슈대응</u>** 

![Image](https://github.com/user-attachments/assets/99d2b9ed-15b2-498f-895e-220f58788cbc)

에디터에서 Input Action 파일에서 Modifiers를 추가 후<br>
Swizzle Input Axis Values > YXZ를 선택
</div>






