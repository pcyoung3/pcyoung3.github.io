---
layout: single
title: 이득우C++_03_리플렉션시스템
categories: unrealLecture01
tag: [unreal, unrealLecture]

toc: true
toc_sticky: true
---
언리얼 오브젝트의 리플렉션 시스템에 관해서 서술
{: .notice--primary} 

## Unreal Object Reflection?
### 1. Reflection?
```cpp
UENUM(), UCLASS(), USTRUCT(), UFUNCTION(), UPROPERTY()
```
다음과 같이 특정 변수나 클래스 위에 표시되는 Unreal에서 정의한 매크로들을 리플렉션이라고 함<br>
(프로퍼티라고도 함)
   

c++로 구현할 때 해당 Reflection을 사용하지 않고 구현할 수도 있지만<br>
그 경우 가비지 컬렉터라던지 언리얼에서 제공하는 여러 에디터 기능을 사용할 수 없기 때문에<br>
고려해서 사용해야 함
   

### 2. Reflection의 빌드
Reflection의 코드를 빌드하게 되면 컴파일 과정 이전에 UHT(Unreal Header Tool)가<br>
정의된 함수, 변수 등에 관해서 추가적인 코드를 생성한다.<br>
그 다음에 정식 빌드가 실행
   
### 3. Reflection 의 데이터
Reflection의 데이터를 가져와서 여러 작업이 가능<br>
UClass를 사용하여 자신이 가진 프로퍼티와 함수 정보를 컴파일/런타임에서 조회할 수 있다.
   

또한 UObject를 생성하기 위해서는 new()가 아니라 NewObject()를 이용해서 생성해야 됨
```cpp
UClass* ClassRunTime = GetClass();   //런타임에서 클래스 정보를 가져옴
UClass* ClassCompile = UMyGameInstance::StaticClass();   //컴파일된 클래스 정보를 가져옴
```
   
## CDO(Class Default Object)
<div class="notice--danger" markdown="1">
**<u>CDO란 컴파일 과정에서 생성되는 UObject의 초기 인스턴스이다.</u>** 

1. 언리얼 오브젝트를 만들 때마다 생성 및 초기화 작업을 하지 않고 
기본 인스턴스를 미리 만들어 놓고 복제해서 사용하기 위함(프로토타입 패턴)
2. CDO는 생성자에서 정의한다.
3. 플레이를 누를 때 생성되는 것이 아니라 에디터를 컴파일 할 때 생성
</div>

언리얼 클래스 정보에는 클래스 기본 오브젝트가 함께 포함<br>
CDO는 컴파일 과정에서 기본적인 데이터를 정의한다.<br>
그렇게 해서 다수의 물체를 에디터에 배치할 때 일관성 있게 기본값을 조정 가능
   

![Image](https://github.com/user-attachments/assets/3f5bc0f0-d4ca-4fb4-ab86-ddc219c8af2e)
GetDefaultObject() 로 CDO를 얻어올 수 있다
```cpp
	UE_LOG(LogTemp, Log, TEXT("학교이름 기본값1: %s"), *GetClass()->GetDefaultObject<UMyGameInstance>()->SchoolName);
	UE_LOG(LogTemp, Log, TEXT("학교이름 기본값2: %s"), *UMyGameInstance::StaticClass()->GetDefaultObject<UMyGameInstance>()->SchoolName);
```
   
   

<div class="notice--danger" markdown="1">
**<u>빌드관련 주의점</u>** 

UObject에서 생성자는 에디터가 빌드하는 과정중에서 호출
따라서 헤더정보를 변경하거나 생성자 정보를 변경하면 에디터를 끄고 컴파일해야 함
</div>
   

리플렉션의 데이터와 CDO는 엔진 초기화 과정에서 생성
   
   

## 각 코드 사용법
### 1. Reflection 지정
```cpp
	UPROPERTY()
	FString Name;
	
	UPROPERTY()
	int32 Year;
```
   

### 2. Reflection을 이용한 Get, Set
```cpp
UTeacher* Teacher = NewObject<UTeacher>();
FString CurrentTeacherName;
FString NewTeachername = TEXT("홍길동");
FProperty* NameProp = UTeacher::StaticClass()->FindPropertyByName(TEXT("Name"));  //변수 이름이 Name인 변수를 찾음
if (NameProp)
{
	NameProp->GetValue_InContainer(Teacher, &CurrentTeacherName);   //UTeacher클래스의 Name 프로퍼티를 Get
	UE_LOG(LogTemp, Log, TEXT("현재 선생님 이름 %s"), *CurrentTeacherName);

	NameProp->SetValue_InContainer(Teacher, &NewTeachername);   //UTeacher클래스의 Name 프로퍼티를 Set
	UE_LOG(LogTemp, Log, TEXT("새로운 선생님 이름 %s"), *Teacher->GetName());
}
```
FProperty 자료형
   
### 3. Reflection을 이용한 함수호출
```cpp
UTeacher* Teacher = NewObject<UTeacher>();
UFunction* DoLessonFunc = Teacher->GetClass()->FindFunctionByName(TEXT("DoLession"));  //DoLession 함수를 찾음
if (DoLessonFunc)
{
	Teacher->ProcessEvent(DoLessonFunc, nullptr);   //UFunction 변수를 실행, 인자가 없으면 nullptr을 넘김
}
```
UFunction 자료형
   
   

<div class="notice--danger" markdown="1">
**<u>정리</u>** 

각 Reflection 함수는 설계에 따라서 사용을 결정하면 됨
중요한건 내부가 Reflection으로 돌아가고 있다는 것을 숙지
</div>
