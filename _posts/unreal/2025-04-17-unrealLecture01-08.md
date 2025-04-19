---
layout: single
title: 이득우C++_08_메모리관리
categories: unrealLecture01
tag: [unreal, unrealLecture]

toc: true
toc_sticky: true
---
Unreal의 가비지 컬렉션에 대한 문서
{: .notice--primary} 

## 1. 가비지 컬렉션
### 1-1. 마크스윕(Mark-Sweep)방식
![Image](https://github.com/user-attachments/assets/553235d5-9161-49ce-8d66-76a03e5622f8)
   

### 1-2. 언리얼의 가비지 컬렉터
![Image](https://github.com/user-attachments/assets/ff72ca22-b502-44e2-aa4b-ca538803c756)

GUObjectArray에 모든 객체 Flag가 저장되어있고 해당 Flag를 확인해서 메모리에서 제거
   
## 1-3. 그 외 특징
* 지정된 시간에 자동적으로 메모리 회수(기본 60초)
* 언리얼 오브젝트를 삭제하기 위해서는 Delete로 직접삭제가 아니라 Ref를 다 끊어주고 가비지 컬렉터가 메모리를 회수할 때까지 기다려야 된다. 따라서 한번 생성된 언리얼 오브젝트는 바로 삭제가 불가능하다
* AddToRoot 함수를 호출해서 루트셋 플래그를 설정하면 메모리회수로부터 보호받는다
   

1. 메모리 누수문제 : 가비지 컬렉터로 해결
2. 댕글링 포인터 : IsValid() 로 검사 가능
3. 초기화되지 않은 와일드 포인터 : UPROPERTY 속성을 지정하면 자동 nullptr 로 초기화
   

<div class="notice--danger" markdown="1">
**<u>언리얼오브젝트 관리 원칙</u>** 

* 언리얼 오브젝트 내의 언리얼 오브젝트 : UPROPRTY 사용
* 일반 c++ 오브젝트 내의 언리얼 오브젝트 : FGCObject 상속 후 구현
</div>
   
   
## 2. GC 이용방법
### 2-1. UObject의 경우
```cpp
	TObjectPtr<class UStudent> NonPropStudent;   // X
	
	UPROPERTY()
	TObjectPtr<class UStudent> PropStudent;   // O

	TArray<TObjectPtr<class UStudent>> NonPropStudents;   // X

	UPROPERTY()
	TArray<TObjectPtr<class UStudent>> PropStudents;  // O
```
UPROPERTY() 리플렉션을 붙이면 자동으로 GC관리가 등록된다
   
### 2-2. 일반 c++ 클래스의 경우
```cpp
class UNREALMEMORY_API FStudentManager : public FGCObject		//FGCObject를 상속함으로서 언리얼이 GC 관리
{
public:
	FStudentManager(class UStudent* InStudent) : SafeStudent(InStudent) {}

	virtual void AddReferencedObjects(FReferenceCollector& Collector) override;  // 맴버변수의 GC를 위해서 추가

	virtual FString GetReferencerName() const { return TEXT("FStudentManager"); }  // 맴버변수의 GC를 위해서 추가 - 클래스 이름 반환

	const class UStudent* GetStudent() const { return SafeStudent; }

private:
	class UStudent* SafeStudent = nullptr;
};

...

void FStudentManager::AddReferencedObjects(FReferenceCollector& Collector)
{
	if (SafeStudent->IsValidLowLevel())
	{
		Collector.AddReferencedObject(SafeStudent);
	}
}
```
일반클래스의 경우 FGCObject를 상속해서 AddReferencedObjects(), GetReferencerName() 2개 함수를 정의하면 GC 관리가 가능
   
   

<div class="notice--danger" markdown="1">
**<u>IsValidLowLevel</u>** 

해당 객체가 유효한지 검사해주는 함수.
보통 IsValid()를 많이 사용한다. 이 함수는 더 세밀하게 확인
</div>

```cpp
void CheckObjectIsValid(const UObject* InObject, const FString& InTag)
{
	if (InObject->IsValidLowLevel())
	...
```
