---
layout: single
title: 이득우C++_10_패키지
categories: unrealLecture01
tag: [unreal, unrealLecture]

toc: true
toc_sticky: true
---
패키지에 대해서 알아보고 패키지나 에셋을 불러오는 구조에 관해 서술
{: .notice--primary}

## 1. 패키지
![Image](https://github.com/user-attachments/assets/12b70058-f103-44ae-9e6f-e4a7235e378c)
기본적으로 에디터에서는 노출되지 않음
   

![Image](https://github.com/user-attachments/assets/487cd4ec-178d-485c-bb4e-9a2c98b936c9)
UPakage형태로 에디터에서는 대표 Asset만 보여지고 여러 개의 SubObject가 들어갈 수 있다.
   
   
## 2. 패키지 저장
패키지 경로 참고
{: .notice--primary} 

```cpp
// Game -> 에셋을 모아놓은 폴더(content), Temp -> Saved 폴더가 맵핑되어있음
const FString UMyGameInstance::PackageName = TEXT("/Game/Student");
const FString UMyGameInstance::AssetName = TEXT("TopStudent");
```
   
### 2-1. 패키지 생성
```cpp
//저장하기 전에 이미 패키지가 있다면 로드하고 저장하는 것이 좋다
//:: <-- namespace global scope 전역함수만 제한하여 호출가능
UPackage* StudentPackage = ::LoadPackage(nullptr, *PackageName, LOAD_None);
if (StudentPackage)
{
	StudentPackage->FullyLoad();
}

//Package 생성
StudentPackage = CreatePackage(*PackageName);
//Package Flag 설정
EObjectFlags ObjectFlag = RF_Public | RF_Standalone;
```
   
### 2-2. Package에 Object 추가
```cpp
//오브젝트 생성하고 StudentPackage에 추가(대표 Asset)
//인자값 없이 생성하면 "Transient Package" 에 Default로 들어감
UStudent* TopStudent = NewObject<UStudent>(StudentPackage, UStudent::StaticClass(), *AssetName, ObjectFlag);
```
   
### 2-3. Package에 SubObject 추가
```cpp
const int32 NumOfSubs = 10;
for (int32 ix = 0; ix < NumOfSubs; ++ix)
{
	FString SubObjectName = FString::Printf(TEXT("Student%d"), ix);
	//오브젝트 생성하고 SubObject로 추가
	UStudent* SubStudent = NewObject<UStudent>(TopStudent, UStudent::StaticClass(), *SubObjectName, ObjectFlag);
	SubStudent->SetName(FString::Printf(TEXT("학생%d"), ix));
	SubStudent->SetOrder(ix);
}
```
   
### 2-4. Save Package
```cpp
#include "UObject/SavePackage.h"	//FSavePackageArgs를 위해 추가

...

//LongPackageNameToFilename : Package의 File이름을 생성하는 함수
//GetAssetPackageExtension : UAsset 확장자
const FString PackageFileName = FPackageName::LongPackageNameToFilename(PackageName, FPackageName::GetAssetPackageExtension());

//저장할 때 저장 옵션
FSavePackageArgs SaveArgs;
SaveArgs.TopLevelFlags = ObjectFlag;

//SavePackage(패키지, 패키지안에 들어갈 어셋, 패키지파일경로이름, 저장옵션) : 패키지 저장함수
if (UPackage::SavePackage(StudentPackage, nullptr, *PackageFileName, SaveArgs))
{
	UE_LOG(LogTemp, Log, TEXT("패키지가 성공적으로 저장되었습니다"));
}
```
   

### 2-5. 저장된 패키지
![Image](https://github.com/user-attachments/assets/f89afaf2-30c3-42e8-8e43-16dd0c13d787)

다음과 같은 형태로 Content 폴더에 저장됨

   
   

## 3. 패키지 불러오기
### 3-1. Loading Package
```cpp
//LoadPackage(로드해서 넣을 패키지, 패키지이름, Load 옵션) : 패키지 로드함수
UPackage* StudentPackage = ::LoadPackage(nullptr, *PackageName, LOAD_None);
if (nullptr == StudentPackage)
{
	UE_LOG(LogTemp, Warning, TEXT("패키지를 찾을 수 없습니다."));
	return;
}

//패키지 안에 있는 모든 어셋을 로딩
StudentPackage->FullyLoad();
```
   
### 3-2. Package 안에 있는 어셋 가져오기
```cpp
//FindObject(Package, Assetname) : package 내부에서 Assetname의 어셋을 찾아 반환하는 함수
UStudent* TopStudent = FindObject<UStudent>(StudentPackage, *AssetName);
```
   
   
## 4. Asset 불러오기
### 4-1. Asset 경로
![Image](https://github.com/user-attachments/assets/42a3f569-c86b-45ac-a2be-937a652d7fe7)

```cpp
//{패키지이름}.{에셋이름}
const FString TopSoftObjectPath = FString::Printf(TEXT("%s.%s"), *PackageName, *AssetName);
```
   
### 4-2. Asset Loading
```cpp
//오브젝트 경로를 활용해서 에셋 로드
UStudent* TopStudent = LoadObject<UStudent>(nullptr, *TopSoftObjectPath);
PrintStudentInfo(TopStudent, TEXT("LoadObejct Asset"));
```
   
### 4-3. 생성자에서 Asset Loading
```cpp
UMyGameInstance::UMyGameInstance()
{
	//생성자에서 에셋 로드
	const FString TopSoftObjectPath = FString::Printf(TEXT("%s.%s"), *PackageName, *AssetName);
	
	//ConstructorHelpers::FObjectFinder<> -> 생성자에서 Asset Loading 시 사용
	static ConstructorHelpers::FObjectFinder<UStudent> UASSET_TopStudent(*TopSoftObjectPath);
	if (UASSET_TopStudent.Succeeded())	//Loading의 성공유무
	{
		//FObjectFinder로 뽑힌객체는 .Object를 통해 접근가능
		PrintStudentInfo(UASSET_TopStudent.Object, TEXT("Constructor"));
	}
}
```
<div class="notice--danger" markdown="1">
**<u>생성자에서 에셋로딩할 때 주의점</u>** 

Asset이 폴더 내에 존재하지 않으면 오류발생
</div>
   
   

### 4-4. Asset Async Load
#### 4-4-1. StreamableManager 선언
```cpp
#include "Engine/StreamableManager.h"
...
FStreamableManager StreamableManager;  //Pointer를 사용할 수 없어서 Header에 include 추가
TSharedPtr<FStreamableHandle> Handle;
```
   
#### 4-4-2. RequestAsyncLoad 호출
```cpp
const FString TopSoftObjectPath = FString::Printf(TEXT("%s.%s"), *PackageName, *AssetName);
Handle = StreamableManager.RequestAsyncLoad(TopSoftObjectPath,	//Async 함수 람다로 완료되었을 때 처리
	[&]()
	{
		if (Handle.IsValid() && Handle->HasLoadCompleted())
		{
			UStudent* TopStudent = Cast<UStudent>(Handle->GetLoadedAsset());
			if (TopStudent)
			{
				PrintStudentInfo(TopStudent, TEXT("AsyncLoad"));

				//Handle 닫기 및 종료
				Handle->ReleaseHandle();
				Handle.Reset();
			}
		}
	}
);
```
