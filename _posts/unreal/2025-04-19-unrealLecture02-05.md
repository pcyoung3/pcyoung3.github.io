---
layout: single
title: 이득우Framework_05_애니메이션 몽타주
categories: unrealLecture02
tag: [unreal, unrealLecture]

toc: true
toc_sticky: true
---
## 1. 애니메이션 몽타주란?
![Image](https://github.com/user-attachments/assets/f4eb0dcd-eac4-4f11-b2d5-d42c3139b25b)

   
   

## 2. 애니메이션 몽타주 생성
1. Asset browser 에 있는 Animation Asset을 몽타주 그래프에 드래그<br>
![Image](https://github.com/user-attachments/assets/7919b652-d888-45ec-a800-2de149440b88)<br>

2. 그래프 우클릭 후 Section 생성<br>
![Image](https://github.com/user-attachments/assets/2ead3a75-d61f-4eea-ae92-787d2754f0f6)<br>

3. Section 의 이름, 링크 등을 셋팅<br>
![Image](https://github.com/user-attachments/assets/2ecde267-d867-4a09-aeb1-70ec0e09b78c)<br>

<div class="notice--info" markdown="1">
**<u>링크</u>** 

Section의 Link가 처음에는 다 이어져있는데 이러면 애니메이션이 자동으로 전부 재생된다
끊으면 해당 구간만 반복하게 됨
</div>

   
<ol start="4">      <!-- 번호 중간부터 시작하는 법-->
    <li>ABP에 최종 결과물에 몽타주가 나올 수 있게 추가<br>
        <img src="https://github.com/user-attachments/assets/3cad449a-3855-4f10-9b02-376c4173a3b2"><br>
    </li>
</ol>
   
   

## 3. C++ Animation Montage
### 3-1. Header 선언
```cpp
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Animation)
TObjectPtr<class UAnimMontage> ComboActionMontage;
```

   

### 3-2. Play
```cpp
// Animation Setting
const float AttackSpeedRate = 1.0f;
UAnimInstance* AnimInstance = GetMesh()->GetAnimInstance();

//Montage_Play(UAnimMontage* 몽타주, 속도)
AnimInstance->Montage_Play(ComboActionMontage, AttackSpeedRate);
```

   

### 3-3. Section 이동
```cpp
UAnimInstance* AnimInstance = GetMesh()->GetAnimInstance();

CurrentCombo = FMath::Clamp(CurrentCombo + 1, 1, ComboActionData->MaxComboCount);
FName NextSection = *FString::Printf(TEXT("%s%d"), *ComboActionData->MontageSectionNamePrefix, CurrentCombo);

//montage section 이동 함수
//Montage_JumpToSection(FName 섹션이름, UAnimMontage* 몽타주)
AnimInstance->Montage_JumpToSection(NextSection, ComboActionMontage);
```

   

### 3-4. 종료 시 Delegate
```cpp
FOnMontageEnded EndDelegate;

//AABCharacterBase::ComboActionEnd 함수 binding
EndDelegate.BindUObject(this, &AABCharacterBase::ComboActionEnd);

//Montage_SetEndDelegate(Delegate 변수, Montage 변수)
AnimInstance->Montage_SetEndDelegate(EndDelegate, ComboActionMontage);
```

   
   

## 4. C++ Timer 
특정 시간마다 함수를 실행
{: .notice--info} 

### 4-1. 선언
```cpp
FTimerHandle ComboTimerHandle;
```

   

### 4-2. 타이머 셋팅
```cpp
//SetTimer
//1.TimerHandle, 2.Timer 함수 호출하는 오브젝트, 3.타이머가 발동될 때의 함수
//4.타이머가 호출될 시간, 5.반복여부
GetWorld()->GetTimerManager().SetTimer(ComboTimerHandle, this, &AABCharacterBase::ComboCheck, ComboEffectiveTime, false);
```

   

### 4-3. 타이머 비활성화
```cpp
ComboTimerHandle.Invalidate()
```