---
layout: single
title: 이득우C++_11_언리얼 빌드 시스템
categories: unrealLecture01
tag: [unreal, unrealLecture]

toc: true
toc_sticky: true
---
언리얼엔진의 각 종 구조를 서술
{: .notice--primary} 

## 1. .uproject 파일
<div class="notice--danger" markdown="1">
**<u>uproject 파일에 있는 정보 그리고 작동방식에 관해 서술</u>** 

1. .uproject 파일을 메모장을 열 경우 엔진버전이 표기
2. 버전 내용은 Json 방식으로 표기
3. .uproject는 윈도우 레지스트리에 등록
4. .uproject의 기반을 가지고 UnrealVersionSelector를 통해서 언리얼 엔진이 실행
</div>

![Image](https://github.com/user-attachments/assets/bf317b54-1631-42f5-a175-d9a9d944ed54)
   
   

## 2. 언리얼 프로젝트 구조
![Image](https://github.com/user-attachments/assets/9c00764e-658b-4624-b13d-256eef5c3a04)
1. Config : 프로젝트의 각종 셋팅 저장
2. Content : Asset들을 보관
3. DerivedDataCache : 사용하는 Asset들의 주요정보를 캐싱하여 로딩을 줄임
4. Intermediate : 임시로 사용하는 중간 결과를 저장
5. Saved : 임시로 무언가를 저장하는 용도
   
Intermediate : 언제든지 용량이 클 경우 삭제 가능
DerivedDataCache : 용량을 줄일 때 역시 삭제 가능
Saved : 프로그램에서 무언가를 임의로 저장하지 않았다면 삭제 가능
   
   

## 3. 모듈
![Image](https://github.com/user-attachments/assets/48a26e61-60b0-42eb-b122-44476c990312)
+ 언리얼 엔진의 소스코드는 모두 모듈단위로 구성
+ 에디터용으로는 DLL 동적라이브러리, 게임용으로는 정적라이브러리사용
+ 에디터용 모듈은 UnrealEditor-{모듈이름}.DLL 의 이름규칙을 가짐
   

### 3-1. 에디터용 모듈 위치 및 파일
![Image](https://github.com/user-attachments/assets/8216f828-ab2f-4075-9f0d-4e25529cb7fb)
```
C:\Program Files\Epic Games\UE_5.1\Engine\Binaries\Win64
```
   

### 3-2. 모듈 migration
![Image](https://github.com/user-attachments/assets/8eff5667-ce6a-4e7b-b47c-0a6f8f3da32b)

![Image](https://github.com/user-attachments/assets/159fc3a7-a8a5-4fd3-bfb7-62e48275e5bf)
   

### 3-3. c++ 모듈 생성
![Image](https://github.com/user-attachments/assets/a768f973-182c-48f7-8bb7-e5283db79263)

![Image](https://github.com/user-attachments/assets/1da6342c-563d-498b-abc7-843f5877a3e9)

![Image](https://github.com/user-attachments/assets/c60fe700-9d1e-4516-8fc6-827b7525a9b6)

![Image](https://github.com/user-attachments/assets/49d36665-7d1e-4cf1-a0cf-4ae59d6a980a)

{moduleName}.Build.cs 파일이 있는 곳이 모듈이 시작하는 곳
{: .notice--primary} 
   

### 3-4. 모듈간의 종속관계
<div class="notice--success" markdown="1">

하나의 모듈에 너무 많은 코드가 들어가면 언리얼 엔진은 빌드 방식을 변경하기 때문에
적당한 모듈단위로 나눠서 관리하는 것이 유리
</div>

![Image](https://github.com/user-attachments/assets/55eca199-019d-4421-84c9-1a39b8df9e60)
   

### 3-5. 모듈의 공개와 참조
![Image](https://github.com/user-attachments/assets/e162d874-2e25-4424-b50a-1996a1ee0ebd)

* 모듈 내 소스를 필요한 만큼만 공개하는 것이 좋음(의존성 줄임 / 컴파일 타임 최소화)
* 공개할 파일은 Public 폴더 / 비공개할 파일은 Private 폴더로 나누면 된다
* 외부로 공개할 클래스 선언에는 {모듈이름}\_DLL 매크로를 붙인다
* 게임모듈에서는 Build.cs 설정을 통해 참조할 모듈 지정 가능

   

![Image](https://github.com/user-attachments/assets/06fa8360-0e4e-4d81-a6ee-5268cc1854b8)

모듈 참조를 원하지 않는다면 class 선언부의 외부에서 참조할 수 있게 만든 부분을 삭제해서 참조 불가능하게 만들 수 있다

   
   

## 4. Plugin System
### 4-1. Plugin?
<div class="notice--info" markdown="1">
**<u>모듈을 독립적으로 동작하게 변경한 것</u>** 

게임 프로젝트에 모듈을 직접 추가하면 분업이 어렵고 방법도 까다롭다
따라서 플러그인 구조를 만들어서 분업화 한다
</div>

![Image](https://github.com/user-attachments/assets/abdd3a1b-6dca-444a-82db-0e11b6768696)
![Image](https://github.com/user-attachments/assets/b6a9b0e1-84f8-4ea2-8fcd-bee954b044e9)
   

### 4-2. 클래스 생성 시 Plugin 선택
![Image](https://github.com/user-attachments/assets/31d4413f-f4be-45bd-b125-1e76c713a53e)

에디터에서 C++ 파일을 생성할 때 어느 Plugin 에 생성할 지 선택 가능
   

### 4-3. Build.cs 에 Plugin(Module) 추가
![Image](https://github.com/user-attachments/assets/8cf00349-e1df-4562-b185-f561c48953be)

해당 부분에 모듈을 추가해야 사용가능
   

<div class="notice--info" markdown="1">
**<u>Plugin 작업법</u>** 

보통은 위에 실습처럼 Plugin을 직접 File System에서 추가하지 않고 Editor에서 생성 및 추가
![Image](https://github.com/user-attachments/assets/c2f0444a-fab8-4e4b-88a1-ef7b11177ac4)
</div>
   
   

## 5. 게임 빌드
### 5-1. 게임빌드 관련 정보
![Image](https://github.com/user-attachments/assets/b81b5417-243f-4cdf-9f50-0fe1fd260e78)
   

### 5-1. exe 파일을 뽑는 빌드
"Shipping" 상태에서는 Assertion, check 같은 매크로들은 전부 제외됨
{: .notice--info} 

![Image](https://github.com/user-attachments/assets/90fc8e76-50ad-44bb-ba5c-c00a9f371001)

Editor에서 Shipping 설정 후 Package Project(쿠킹과 빌드 동시 실행)
   

![Image](https://github.com/user-attachments/assets/c3cd2271-c95e-48fd-94b9-10fea062d3e9)

exe 파일을 실행할 수 있다


