---
layout: single
title: C++Porting_UMG
categories: unrealTheory
tag: [unreal]

toc: true
toc_sticky: true
---

## 1. 개요
![Image](https://github.com/user-attachments/assets/c0ee8ffd-0da8-468f-97fe-411c8df4f396)
전체적인 작업의 큰 틀은 다음 3단계로 이루어진다
   
   
## 2. UMG Blueprint 배치
### 2-1. Widget BluePrint를 생성
![Image](https://github.com/user-attachments/assets/71e2dcb1-be76-45cc-9544-5c7eef57549d)

우클릭 후 Widget Blueprint 생성

   

![Image](https://github.com/user-attachments/assets/dedee1b5-8e71-4f57-ab06-64ff0e34683d)

여기서 그냥 User Widget 선택

   

![Image](https://github.com/user-attachments/assets/f233d237-330f-427f-b4f2-85f6dae8d081)

누른 뒤에 여러 아이템들을 추가한다.

   

데이터 변경을 C++과 연동할 아이템들은 C++에서도 변수명이 같아야 한다!
{: .notice--danger} 

   
   
## 3. C++에서 UI 포팅 작업
### 3-1. UserWidget을 상속받아서 C++ 클래스 생성
![Image](https://github.com/user-attachments/assets/bd4bb75c-a377-4984-9d55-44bd4f2a24f5)
   
### 3-2. 위에서 만든 아이템과 동일한 변수이름으로 선언
<div class="notice--danger" markdown="1">
**<u>Widget 의 item과 동기화 문구</u>** 

UPROPERTY(meta = (BindWidget))
</div>

```cpp
UCLASS()
class D1_API UUI_MainHUD : public UUserWidget
{
	GENERATED_BODY()
public:
	UPROPERTY(meta = (BindWidget))
	TObjectPtr<class UUI_StatBar> HealthBar; //블루프린트와 이름이 같아야 함
	UPROPERTY(meta = (BindWidget))
	TObjectPtr<class UUI_StatBar> StaminaBar; //블루프린트와 이름이 같아야 함
};
```
   
```cpp
UCLASS()
class D1_API UUI_StatBar : public UUserWidget
{

	GENERATED_BODY()
protected:
	virtual void NativeConstruct() override;
public:	//Delegate
	void StatBarStatValueUpdated(EStats stat, float value);
public:
	UPROPERTY(EditAnywhere, BlueprintReadWrite, meta = (BindWidget))
	class UProgressBar* StatBar; //블루프린트와 이름이 같아야 함

	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category=Initialization , meta=(AllowPrivateAccess="true"))
	EStats StatType; //블루프린트에서 수정 가능

	UPROPERTY(visibleAnywhere, BlueprintReadWrite, Category=Initialization , meta=(AllowPrivateAccess="true"))
	TObjectPtr<UStatsComponent> StatsComponent; // 블루프린트에서 수정 가능
};
```
   
### 3-3. Widget의 기능 셋팅
```cpp
//Widget 생성 시 호출
void UUI_StatBar::NativeConstruct()
{
	APawn* pplayer = GetOwningPlayerPawn();
	if (pplayer)
	{
		StatsComponent = pplayer->GetComponentByClass<UStatsComponent>();
		if (IsValid(StatsComponent))
			StatsComponent->OnCurrentStatValueUpdated.AddUObject(this, &UUI_StatBar::StatBarStatValueUpdated);
	}
}

//Widget에서 bind할 Delegate 함수
void UUI_StatBar::StatBarStatValueUpdated(EStats stat, float value)
{
	if (StatType != stat)
		return;

	if (!IsValid(StatsComponent))
		return;

	float fPercent = value / StatsComponent->GetMaxStatValue(StatType);
	StatBar->SetPercent(fPercent);
}

```

   
   

## 4. BluePrint UI에서 부모 변경
### 4-1. C++ 파일로 부모를 변경
![Image](https://github.com/user-attachments/assets/53c6798c-60c2-431f-a18d-71e607b3cdb4)

부모를 변경해서 C++에서 정의한 기능을 사용할 수 있게 됨

   
   

## 5. 화면 띄우는 방법
### 5-1. Player Controller에서 설정
```cpp
UCLASS()
class D1_API ACombatPlayerController : public APlayerController
{
	GENERATED_BODY()
	
public:
	virtual void BeginPlay() override;

private:
	UPROPERTY(EditDefaultsOnly, Category = UI)
	TSubclassOf<class UUI_MainHUD> MainHUDClass;
};
```
   
```cpp
#include "CombatPlayerController.h"
#include "UI/UI_MainHUD.h"

void ACombatPlayerController::BeginPlay()
{
	if (MainHUDClass)
	{
		UUI_MainHUD* mainHUD = CreateWidget<UUI_MainHUD>(this, MainHUDClass);
		mainHUD->AddToViewport();
	}
}
```
만든 Widget을 여기서 생성하는 코드를 넣어주고 AddToViewport를 호출

   

### 5-2. Blueprint로 PlayerController 상속 후 작업
C++로 만든 PlayerController를 Blueprint로 상속해서 Blueprint로 변경
그 후에 설정해주었던 TSubclassOf를 이용해서

![Image](https://github.com/user-attachments/assets/6e3baf97-c97b-4184-b958-4503d517bb5b)

다음과 같이 UI 셋팅을 해준다
   
### 5-3. World Setting
![Image](https://github.com/user-attachments/assets/ddf50872-96e0-4517-9d2f-e94708ffc892)

World Setting에서 사용하는 PlayerController를 셋팅

   
   
## 6. HUD 설정
### 6-1. HUD 란?
다른 UI들을 포함하는 Root UI를 따로 Actor로 만들어서 관리가능
Player Controller에서 해주는 방법 말고 이 방법으로도 가능하다
   
### 6-2. HUD 생성
![Image](https://github.com/user-attachments/assets/24845740-16fc-4da8-b9d2-9f382aca9bf8)

Class선택 시에 Actor 산하에 있는 HUD를 상속받아서 만들면 됨
   
```cpp
UCLASS()
class D1_API ACombatHUD : public AHUD
{
	GENERATED_BODY()
	
protected:
	virtual void BeginPlay() override;
private:
	UPROPERTY(EditDefaultsOnly, Category = UI)
	TSubclassOf<class UUI_MainHUD> MainUI;
};
```

```cpp
#include "UI/CombatHUD.h"
#include "UI/UI_MainHUD.h"

void ACombatHUD::BeginPlay()
{
	Super::BeginPlay();

	UWorld* World = GetWorld();
	if (World)
	{
		APlayerController* Contorller = World->GetFirstPlayerController();
		if (Contorller && MainUI)
		{
			UUI_MainHUD* outputUI = CreateWidget<UUI_MainHUD>(Contorller, MainUI);
			outputUI->AddToViewport();
		}
	}
}
```
   
### 6-3. Blueprint로 작업
![Image](https://github.com/user-attachments/assets/932d7b9f-d627-4195-8832-928db2f0ec42)

BluePrint로 만든 C++ 파일을 상속하고 TSubclassOf를 이용해서 Blueprint Widget UI를 셋팅
   
### 6-4. World 셋팅
![Image](https://github.com/user-attachments/assets/95a9c53b-385e-44a6-95cd-3f5431a28f48)

World 셋팅에서 만든 Blueprint HUD를 셋팅
