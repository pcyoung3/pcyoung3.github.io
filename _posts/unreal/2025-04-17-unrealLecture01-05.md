---
layout: single
title: 이득우C++_05_컴포지션
categories: unrealLecture01
tag: [unreal, unrealLecture]

toc: true
toc_sticky: true
---
어떤 오브젝트에 다른 오브젝트를 has-a 관계로 추가하는 것을 컴포지션이라고 함
{: .notice--primary} 

## 구조(예시)
![Image](https://github.com/user-attachments/assets/81bc7e51-a9d2-475c-ab2e-81095a6b8aee)

상속 관계에서 Card(출입증) 클래스를 Person에게 has-a 방식으로 추가
   
      
## 사용법
![Image](https://github.com/user-attachments/assets/621de956-49ff-4fd9-a72d-ea5f60a9c1e6)

1. CDO에서 시작하자마자 필수적으로 포함하려면 CreateDefaultSubobject 함수를 이용
2. Runtime에서 선택적으로 포함시키려면 NewObject()로 추가
   

## 코드
### 1. 선언부
```cpp
UPROPERTY()
TObjectPtr<class UCard> Card;   //전방선언 + Unreal5 스타일
class UCard* Card;              //Unreal4 스타일
```
   
### 2. CDO에서 추가
```cpp
Card = CreateDefaultSubobject<UCard>(TEXT("NAME_Card"));
```
텍스트의 경우 FName 형식으로 추가되기 때문에 "NAME_"을 추가
   
### 3. Runtime에 추가
```cpp
CourseInfo = NewObject<UCourseInfo>(this);   //맴버변수로 추가
UStudent* Student1 = NewObject<UStudent>();  //지역변수로 추가
```
   
   
## 언리얼 UEnum 사용방법
### 1. 선언
```cpp
UENUM()    //UClass 처럼 리플렉션을 추가
//c++ 11부터 생긴 enum class문법
enum class ECardType : uint8  //해당 Enum Type을 uint8(byte)으로 지정
{
	Student = 1 UMETA(DisplayName = "For Student"),//Enum값에 메타정보를 지정할 수 있다
	Teacher UMETA(DisplayName = "For Teacher"),
	Staff UMETA(DisplayName = "For Staff"),
	Invalid
};
```
   
### 2. 사용
```cpp
const UEnum* CardEnumType = FindObject<UEnum>(nullptr, TEXT("/Script/UnrealComposition.ECardType"));
if (CardEnumType)
{
	FString CardMetaData = CardEnumType->GetDisplayNameTextByValue((int64)CardType).ToString();
	UE_LOG(LogTemp, Log, TEXT("%s님이 소유한 카드 종류 %s"), *Person->GetName(), *CardMetaData);
}
```
2-1) FindObject : 해당 함수로 Enum 타입 찾음<br>
2-2) "/Script/UnrealComposition.ECardType"<br>
	> Script : c++에 생성된 객체들은 script라는 절대주소를 가짐<br>
	> UnrealComposition : 프로젝트 이름<br>
	> ECardType : 타입이름<br>
2-3) CardEnumType->GetDisplayNameTextByValue((int64)CardType).ToString()<br>
	> 선언부에서 지정한 UMETA 이름을 가져옴<br>
   
#### 결과창
```
학생님이 소유한 카드 종류 For Student
```
