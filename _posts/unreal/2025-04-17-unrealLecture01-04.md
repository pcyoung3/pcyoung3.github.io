---
layout: single
title: 이득우C++_04_인터페이스
categories: unrealLecture01
tag: [unreal, unrealLecture]

toc: true
toc_sticky: true
---
Unreal에서 지원하는 Interface에 관한 문서
{: .notice--primary} 

## 인터페이스 
### 1. 인터페이스란?
언리얼에서 인터페이스는 규약<br>
인터페이스를 상속하면 자식에서는 인터페이스에 있는 함수들의 구현을 강제한다<br>
(구현하지 않을 경우 오류)
   

하지만 사용에 따라서 자식에서 구현을 강제하지 않을 수도 있다.

   

### 2. 생성
![Image](https://github.com/user-attachments/assets/9b3881a0-0c62-4c24-a2b1-7c774ddaa9d8)

에디터에서 Unreal Interface를 상속해서 생성가능
   
### 3. 정의부
```cpp
// This class does not need to be modified.
UINTERFACE(MinimalAPI)
class ULessonInterface : public UInterface
{
	GENERATED_BODY()
};

/**
 * 
 */
class UNREALINTERFACE_API ILessonInterface
{
	GENERATED_BODY()

	// Add interface functions to this class. This is the class that will be inherited to implement this interface.
};
```
생성하면 다음과 같이 생성되는데 UInterface 부분은 클래스 타입정보를 제공하는 부분이라 건드리지 않아도 되고<br>
주로 작업해야 할 부분은 IInterface 부분이다
   
```cpp
public:
	virtual void DoLesson() = 0;
```
여기서 순수가상함수로 인터페이스를 만들 경우 자식 객체에서 해당 함수의 구현을 강제
   
```cpp
virtual void DoLesson()
{
	UE_LOG(LogTemp, Log, TEXT("수업에 입장합니다."));
}
```
이렇게 Interface에서 함수를 Pure를 쓰지않을 경우 자식객체에서 함수의 구현을 강제하지 않음
   
   
### 4. 상속
```cpp
class UNREALINTERFACE_API UStudent : public UPerson, public ILessonInterface
{ ... }

class UNREALINTERFACE_API UTeacher : public UPerson, public ILessonInterface
{ ... }
```
<div class="notice--danger" markdown="1">
**<u>UInterface를 상속하면 에러</u>** 

I\~\~\~Interface 를 상속해야 한다.
</div>
   
   

### 5. 호출부
```cpp
TArray<UPerson*> Persons = { NewObject<UStudent>(), NewObject<UTeacher>(), NewObject<UStaff>() };
for (const auto Person : Persons)
{
	//Cast 함수로 해당 자료형으로 정상적으로 casting 되었는지 확인할 수 있음
	ILessonInterface* LessonInterface = Cast <ILessonInterface>(Person);
	if (LessonInterface)
	{
		UE_LOG(LogTemp, Log, TEXT("%s은 수업에 참여할 수 있습니다"), *Person->GetName());
		LessonInterface->DoLesson();
	}
	else
	{
		UE_LOG(LogTemp, Log, TEXT("%s은 수업에 참여할 수 없습니다"), *Person->GetName());
	}
}
```
