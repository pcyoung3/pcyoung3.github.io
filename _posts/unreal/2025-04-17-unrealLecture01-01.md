---
layout: single
title: 이득우C++_01_기본타입과 문자열
categories: unrealLecture01
tag: [unreal, unrealLecture]

toc: true
toc_sticky: true
---

## Bool 타입의 선언
<div class="notice--danger" markdown="1">
**<u>Bool을 선언할 때 주의점</u>** 

Bool은 크기가 OS나 다른 플랫폼에서 명확하지 않기 때문에 파일에 Read/Write할 때 위험성이 있다.
</div>

1. Header에서는 가급적 bool 대신 uint8 타입을 사용하되 Bit Field 오퍼레이터를 사용한다.
```cpp
uint8 bNestTemporary:1;  // :1 은 bit field 오퍼레이터로서 해당 비트만 사용하겠다는 표시
```
2. Cpp에서는 자유롭게 bool을 사용해도 된다.
   
   

## Log 찍는 방법
```cpp
UE_LOG(LogTemp, Log, TEXT("Hello Unreal"));
```
   

## 게임화면에 띄우는 디버그 로그 사용법
```cpp
//param1 : int형의 키 값. 메시지의 ID에 해당하는 숫자. 없으면 -1
//param2 : float형의 화면에 출력하고있는 시간
//param3 : Color
//param4 : String
FString str = FString::Printf(TEXT("ComboEffectiveTime : %f "), ComboEffectiveTime);
GEngine->AddOnScreenDebugMessage(-1, 3.0f, FColor::Blue, str);
```
   
   

## Unreal에서 문자열처리
<div class="notice--danger" markdown="1">
**<u>TCHAR("")의 형태를 통해서 문자열을 표시하고 문자열을 다루는 클래스로 FString을 제공</u>** 

플랫폼마다 문자열이 처리되는 방식(유니코드(UTF), 아스키코드 등)이 다르기 때문
</div>

### 1. TCHAR
```cpp
TCHAR LogCharArray[] = TEXT("Hello Unreal");
UE_LOG(LogTemp, Log, LogCharArray);
```
   

### 2. FString
```cpp
FString LogCharString = LogCharArray;
UE_LOG(LogTemp, Log, TEXT("%s"), *LogCharString);
```
   

### 2-1. FString에서 TCHAR 추출
```cpp
FString LogCharString = TEXT("Hello Unreal");

//1. 문자열을 빼와서 Read만 하는 경우(새로운 Write가 불가능)
const TCHAR* LongCharPtr = *LogCharString;   

//2. 문자열을 변경하고 싶을 경우 -> Cello Unreal 출력
TCHAR* LogCharDataPtr = LogCharString.GetCharArray().GetData();
*LogTCharPrt = 'C';
```
   

### 2-2. Strcpy
```cpp
TCHAR LogCharArrayWithSize[100];
FCString::Strcpy(LogCharArrayWithSize, charTest.Len(), *charTest);
```
   

### 2-3. Contains, Find, Mid
```cpp
// ESearchCase::IgnoreCase -> 대문자 소문자 구분 없이 검색
// ESearchCase::CaseSensitive -> 대문자 소문자 구분
FString LogCharString = TEXT("Hello Unreal");
if (LogCharString.Contains(TEXT("unreal"), ESearchCase::IgnoreCase))   //Contains : FStinrg 안에 특정문자열이 있는지 판별
{
	int32 Index = LogCharString.Find(TEXT("unreal"), ESearchCase::IgnoreCase);  //Find : FString 안에 특정문자열의 index 반환
	FString EndString = LogCharString.Mid(Index);  //Mid : Index를 이어받아 그 부분부터의 문자열을 반환
	UE_LOG(LogTemp, Log, TEXT("Find Test : %s"), *EndString); //Output : Unreal
}
```
   

### 2-4. Printf, SanitizeFloat, FromInt, Atoi, Atof
FCString의 경우 C런타임 수준에서 문자열을 처리하는 함수를 제공하는 클래스
{: .notice--primary} 
```cpp
int32 IntValue = 32;
float FloatValue = 3.141592;

FString FloatIntString = FString::Printf(TEXT("Int:%d Float:%f"), IntValue, FloatValue);	//FString::Printf -> 해당 포멧의 FString 반환
FString FloatString = FString::SanitizeFloat(FloatValue);	//FString::SanitizeFloat -> Float > String
FString IntString = FString::FromInt(IntValue);	//FString::FromInt -> Int > String

UE_LOG(LogTemp, Log, TEXT("%s"), *FloatIntString);		//Int:32 Float:3.141592
UE_LOG(LogTemp, Log, TEXT("Int:%s Float:%s"), *IntString, *FloatString);		//Int:32 Float:3.141592

int32 IntValueFromString = FCString::Atoi(*IntString);	// FCString::Atoi -> String > Int
float FloatValueFromString = FCString::Atof(*FloatString);	// FCString::Atof -> String > Float
FString FloatIntString2 = FString::Printf(TEXT("Int:%d Float:%f"), IntValueFromString, FloatValueFromString);
UE_LOG(LogTemp, Log, TEXT("%s"), *FloatIntString2);		//Int:32 Float:3.141592
```
   
   
   

참고) 로그에 한글을 띄우는 방법
{: .notice--primary} 

1. cpp 파일을 다른이름으 저장한다(Save as..)
2. 저장 방식에서 옆에 화살표를 누른 뒤 인코딩하여 저장을 클릭<br>
![Image](https://github.com/user-attachments/assets/260b2c4a-9781-4e17-abff-85556a5e89d6)<br>
3.  UTF-8 로 저장<br>
![Image](https://github.com/user-attachments/assets/dd364f70-88a4-46f0-ac81-57d5dedb10c4)
   
   

### 3. FName
<div class="notice--danger" markdown="1">
**<u>문자열처리가 아닌 읽기위해서 사용</u>** 

1. Unreal은 FName과 관련된 싱글턴 Pool  자료구조가 있다
2. 해당 자료형을 추가하는 순간 String이 저장됨과 동시에 해당 String에 맞는 Hash 값도 생성됨
3. 문자 정보는 대소문자를 구분 X
</div>

   

```cpp
FName key1(TEXT("PELVIS"));
FName key2(TEXT("pelvis"));
UE_LOG(LogTemp, Log, TEXT("FName 비교 결과 : %s"), key1 == key2 ? TEXT("같음") : TEXT("다름"));	// FName 비교 결과 : 같음

for (int i = 0; i < 10000; i++)
{
	//tick과 같은곳에서 FName을 선언할 경우 생성, Hash 검색이 이루어져 성능을 저하시킬 수 있다
	FName SearchInNamePool = FName(TEXT("pelvis"));

	//따라서 여러번 호출될 가능성이 있으면 static으로 처리하는 것이 좋다
	const static FName StaticOnlyOnce(FName(TEXT("pelvis")));
}
```


