---
layout: single
title: 이득우C++_06_델리게이트
categories: unrealLecture01
tag: [unreal, unrealLecture]

toc: true
toc_sticky: true
---
발행 구독(옵저버)패턴을 이용하기 위해 델리게이트를 사용
{: .notice--primary} 

## 발행구독 패턴
![Image](https://github.com/user-attachments/assets/03d64d1f-5135-4daa-9d86-da73269fcbea)
![Image](https://github.com/user-attachments/assets/01a1ca6a-a7f4-4299-b366-7e8dbb4967c2)

콘텐츠 제작자와 구독자
학사정보와 학생은 서로 알지 않아도 변경사항을 체크할 수 있다.
   
   
## 코드
### 1. 델리게이트 선언
```cpp
DECLARE_{델리게이트유형}_DELEGATE_{함수정보}
```

| 델리게이트 유형                       | 코드 |
| :-------------------------- | :---- |
| 1:1 형태로 c++만 지원      |   DECLARE   |
| 1:N 형태로 c++만 지원      |   DECLARE_MULTICAST   |
| 1:1 형태로 블루프린트 지원 |    DECLARE_DYNAMIC  |
| 1:N 형태로 블루프린트 지원 |   DECLARE_DYNAMIC_MULTICAST   |

<br>

| 함수정보        | 코드               |
|:--------------- |:------------------ |
| 인자 X 반환값 X | X                  |
| 인자 1 반환값 X | OneParam           |
| 인자 3 반환값 O | RetVal_ThreeParams |

함수정보는 총 9개가 있음
   

<div class="notice--danger" markdown="1">
즉, 1:N형태로 c++만 지원하면서 인자3개 반환값이 있는 델리게이트는<br>
DECLARE_MULTICAST_DELEGATE_RetVal_ThreeParams 이 된다
</div>
   
   

### 2. 코드 구조
![Image](https://github.com/user-attachments/assets/58b7efb2-bad2-4941-a7d6-d219a9097ce5)
#### 2-1) Delegate 자료형 선언
```cpp
DECLARE_MULTICAST_DELEGATE_TwoParams(FCourseInfoOnChangedSignature, const FString&, const FString&);
```
   
#### 2-2) Delegate 자료형 맴버변수 선언
```cpp
FCourseInfoOnChangedSignature OnChanged;
```
   
#### 2-3) Broadcast 호출함수
```cpp
//header file
void ChangeCourseInfo(const FString& InSchoolName, const FString& InNewContents);
...
//cpp file
void UCourseInfo::ChangeCourseInfo(const FString& InSchoolName, const FString& InNewContents)
{
	Contents = InNewContents;

	UE_LOG(LogTemp, Log, TEXT("[CourseInfo] 학사 정보가 변경되어 알림을 발송합니다."));
	
	//multicast일 경우 Broadcast, multicast가 아니면 ExecuteIfBound
	OnChanged.Broadcast(InSchoolName, Contents);
}
```
   
#### 2-4) Delegate와 동일한 인자값의 함수 선언(Student)
```cpp
void GetNotification(const FString& School, const FString& NewCourseInfo);
...
void UStudent::GetNotification(const FString& School, const FString& NewCourseInfo)
{
	UE_LOG(LogTemp, Log, TEXT("[Student] %s님이 %s로부터 받은 메시지 : %s"), *Name, *School, *NewCourseInfo);
}
```
   
#### 2-5) Delegate를 호출할 곳에 객체 생성
```cpp
CourseInfo = NewObject<UCourseInfo>(this);
UStudent* Student1 = NewObject<UStudent>();
```
   
#### 2-6) Binding
```cpp
//multicast 일 경우 AddUObject
CourseInfo->OnChanged.AddUObject(Student1, &UStudent::GetNotification);

//이 아래는 예시코드들
//multicast가 아닐 경우 BindUObject 
FOnMontageEnded EndDelegate;
EndDelegate.BindUObject(this, &AABCharacterBase::ComboActionEnd);

//또는 multicast가 아닐 경우 CreateUObject
StreamableDelegate = FStreamableDelegate::CreateUObject(this, &AABCharacterNonPlayer::NPCMeshLoadCompleted);

//multicast + dynamic일 경우 AddDynamic
FOnCombatToggled OnCombatToggled;
OnCombatToggled.AddDynamic(this, &ACombatCharacter::CharacterCombatToggled);
```
      
#### 2-7) Notification
```cpp
CourseInfo->ChangeCourseInfo(SchoolName, TEXT("변경된 학사 정보"));
```
   
### 3. 결과
```
LogTemp: [CourseInfo] 학사 정보가 변경되어 알림을 발송합니다.
LogTemp: [Student] 학생3님이 학교로부터 받은 메시지 : 변경된 학사 정보
LogTemp: [Student] 학생2님이 학교로부터 받은 메시지 : 변경된 학사 정보
LogTemp: [Student] 학생1님이 학교로부터 받은 메시지 : 변경된 학사 정보
```

