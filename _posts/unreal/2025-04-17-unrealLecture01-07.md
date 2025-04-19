---
layout: single
title: 이득우C++_07_컨테이너 라이브러리
categories: unrealLecture01
tag: [unreal, unrealLecture]

toc: true
toc_sticky: true
---
TArray, TSet의 특징에 대한 문서
{: .notice--primary}

## 1. TArray와 TSet 비교
![Image](https://github.com/user-attachments/assets/ee0656d2-9b1d-4ab1-92e5-fff364d60ad3)

TArray는 동적배열 -> Vector와 유사
   

TSet은 Unordered Set을 생각하면 된다.
1. 중복허용 안함
2. 헤시테이블 형태로 되어있음
3. TSet의 경우 처음 채워질 때는 순서대로 채워지지만 원소 삭제 후 다시 Add를 하면
	순서대로 채워지지 않고 정렬도 되지 않는다.
4. 검색의 용도로 사용하면 좋다.
   
   

## 2. TArray 코드
```cpp
#include "MyGameInstance.h"
#include "Algo/Accumulate.h"		//알고리즘 헤더

void UMyGameInstance::Init()
{
	Super::Init();

	const int32 ArrayNum = 10;
	TArray<int32> Int32Array;
	
	Int32Array.SetNum(10);		//원소 공간확보 후 기본값으로 초기화
	
	for (int32 ix = 1; ix <= ArrayNum; ++ix)
		Int32Array.Add(ix);	// 원소 추가

	Int32Array.RemoveAll([](int32 Val)	// 람다를 이용해서 조건에 맞는 원소 전부 삭제 가능
		{
			return Val % 2 == 0;
		});

	Int32Array += {2, 4, 6, 8, 10};		// 중괄호로 단체로 값 추가 가능

	TArray<int32> Int32ArrayCompare;
	int32 CArray[] = { 1, 3, 5, 7, 9, 2, 4, 6, 8, 10 };
	Int32ArrayCompare.AddUninitialized(ArrayNum);		// 초기화 없이 공간확보
	FMemory::Memcpy(Int32ArrayCompare.GetData(), CArray, sizeof(int32) * ArrayNum);		// Memcpy 사용법

	ensure(Int32Array == Int32ArrayCompare);	// 조건만족하지 않으면 로그 상에 에러문구 띄움
	//check(Int32Array == Int32ArrayCompare);	// 조건만족하지 않으면 프로그램 중지시킴

	int32 Sum = 0;
	for (const int32& Int32Elem : Int32Array)	//범위지정 for문 사용가능
		Sum += Int32Elem;
	ensure(Sum == 55);

	int32 SumByAlgo = Algo::Accumulate(Int32Array, 0);	//Int32Array 의 Index 0번 부터의 모든 원소 합계
	ensure(SumByAlgo == 55);
}
```
   
   
## 3. TSet 코드
```cpp
TSet<int32> Int32Set;
for (int32 ix = 1; ix <= ArrayNum; ++ix)
	Int32Set.Add(ix);

Int32Set.Remove(2);		//Set은 TArray처럼 일괄적으로 지우는 함수가 없음
Int32Set.Remove(4);
Int32Set.Remove(6);
Int32Set.Remove(8);
Int32Set.Remove(10);	//1 3 5 7 9
Int32Set.Add(2);
Int32Set.Add(4);
Int32Set.Add(6);
Int32Set.Add(8);
Int32Set.Add(10);	//1 10 3 8 5 6 7 4 9 2
```
   
   
## 4. UStruct

| 특성 | 구조 |
| ---- | ---- |
|   ![Image](https://github.com/user-attachments/assets/724e78e8-04c4-498d-9fdd-1d7d3ee0b1dd)   |   ![Image](https://github.com/user-attachments/assets/a3ec7bb3-c230-4b31-9af6-f8bb4400e021)   |

### 4-1. 선언
```cpp
USTRUCT()
struct FStudentData
{
	GENERATED_BODY()
	
	FStudentData()
	{
		Name = TEXT("홍길동");
		Order = -1;
	}
	
	//UObject와 다르게 인자있는 생성자를 만들 수 있다.
	FStudentData(FString InName, int32 InOrder) : Name(InName), Order(InOrder) {}

	...
	
	UPROPERTY()
	FString Name;

	UPROPERTY()
	int32 Order;
};
```
   
### 4-2. 구조체 사용
```cpp
private:
	TArray<FStudentData> StudentsData;	//값타입이라서 리플렉션이 필요없음
	
	
	UPROPERTY()  //UObject의 포인터를 맴버변수로 갖는경우 무조건 UPROPERTY() 필수
	TArray<TObjectPtr<class UStudent>> Students;
```
UStruct는 값형식으로 메모리관리가 필요없기 때문에 리플렉션 선언이 없어도 됨
   
### 4-3. 구조체 TArray로 채우기
```cpp
FString MakeRandomName()
{
	TCHAR FirstChar[] = TEXT("김이박최");
	TCHAR MiddleChar[] = TEXT("상혜지성");
	TCHAR LastChar[] = TEXT("수은원연");

	TArray<TCHAR> RandArray;
	RandArray.SetNum(3);
	RandArray[0] = FirstChar[FMath::RandRange(0, 3)];    //범위 안에서 랜덤 숫자 뽑는 함수
	RandArray[1] = MiddleChar[FMath::RandRange(0, 3)];
	RandArray[2] = LastChar[FMath::RandRange(0, 3)];

	return RandArray.GetData();
}

...

const int32 StudentNum = 300;
for (int32 ix = 1; ix <= StudentNum; ++ix)
{
	StudentsData.Emplace(FStudentData(MakeRandomName(), ix));
}

TArray<FString> AllStudentsNames;
Algo::Transform(StudentsData, AllStudentsNames,   //Algo::Transform -> 1번 컨테이너를 2번 컨테이너로 복사하는 함수. 복사하는 값은 람다 이용
	[](const FStudentData& Val)
	{
		return Val.Name;
	}
);

//LogTemp: 모든 학생 이름의 수 : 300
UE_LOG(LogTemp, Log, TEXT("모든 학생 이름의 수 : %d"), AllStudentsNames.Num());
```
   
### 4-4. TSet에 중복검사
```cpp
TSet<FString> AllUniqueNames;
Algo::Transform(StudentsData, AllUniqueNames,
	[](const FStudentData& Val)
	{
		return Val.Name;
	}
);

//LogTemp: 중복 없는 학생 이름의 수 : 63
UE_LOG(LogTemp, Log, TEXT("중복 없는 학생 이름의 수 : %d"), AllUniqueNames.Num());
```
   
### 4-5. TSet이나 TMap에 구조체 사용
```cpp
struct FStudentData
{
	...
	
	//Set과 Map에서 쓰기 위해서 Operator==추가 Hash 함수 추가
	bool operator==(const FStudentData& InOther) const
	{
		return Order == InOther.Order;
	}
	
	//Set과 Map에서 쓰기 위해서 GetTypeHash 함수 추가
	friend FORCEINLINE uint32 GetTypeHash(const FStudentData& InStudentData)
	{
		return GetTypeHash(InStudentData.Order);
	}
	
	...
};
...

TSet<FStudentData> StudentsSet;
for (int32 ix = 1; ix <= StudentNum; ++ix)
{
	StudentsSet.Emplace(FStudentData(MakeRandomName(), ix));
}
```
Hash를 사용하는 컨테이너에 사용자 정의 구조체를 넣기 위해서는 operator== 와 GetTypeHash() 함수 구현필요
   
   
## 5. TMap
<div class="notice--danger" markdown="1">
**<u>특징</u>** 

1. Key와 Value가 쌍으로 pair를 이루는 언리얼 컨테이너
2. STL unordered_map 과 유사
3. TSet과 동일하게 key가 Hash로 되어있어서 사용자 정의 구조체를 추가할 경우 GetTypeHash() 구현필요
4. TMap의 경우 key 중복 불가 
5. TMultiMap의 경우 Key 중복허용
</div>

   

### 5-1. TMap 사용
```cpp
TMap<int32, FString> StudentsMap;

...

Algo::Transform(StudentsData, StudentsMap,
	[](const FStudentData& Val)
	{
		return TPair<int32, FString>(Val.Order, Val.Name);
	}
);

// LogTemp: 순번에 따른 학생 맵의 레코드 수 : 300
UE_LOG(LogTemp, Log, TEXT("순번에 따른 학생 맵의 레코드 수 : %d"), StudentsMap.Num());

TMap<FString, int32> StudentsMapByUniqueName;

Algo::Transform(StudentsData, StudentsMapByUniqueName,
	[](const FStudentData& Val)
	{
		return TPair<FString, int32>(Val.Name, Val.Order);
	}
);

// LogTemp: 이름에 따른 학생 맵의 레코드 수 : 63
UE_LOG(LogTemp, Log, TEXT("이름에 따른 학생 맵의 레코드 수 : %d"), StudentsMapByUniqueName.Num());
```
   
### 5-2. TMultiMap 사용
```cpp
TMultiMap<FString, int32> StudentMapByName;
Algo::Transform(StudentsData, StudentMapByName,
	[](const FStudentData& Val)
	{
		return TPair<FString, int32>(Val.Name, Val.Order);
	}
);

// LogTemp: 이름에 따른 학생 멀티맵의 레코드 수 : 300
UE_LOG(LogTemp, Log, TEXT("이름에 따른 학생 멀티맵의 레코드 수 : %d"), StudentMapByName.Num());

const FString TargetName(TEXT("이혜은"));
TArray<int32> AllOrders;
StudentMapByName.MultiFind(TargetName, AllOrders);   //MultiFind로 조건에 맞는 TArray를 뽑아올 수 있다.

// LogTemp: 이름이 이혜은인 학생 수 : 3
UE_LOG(LogTemp, Log, TEXT("이름이 %s인 학생 수 : %d"), *TargetName, AllOrders.Num());
```
   