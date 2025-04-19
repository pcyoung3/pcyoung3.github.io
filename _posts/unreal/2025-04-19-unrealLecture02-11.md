---
layout: single
title: 이득우Framework_11_행동트리 모델의 이해
categories: unrealLecture02
tag: [unreal, unrealLecture]

toc: true
toc_sticky: true
---

## 1. 행동트리 이론
![Image](https://github.com/user-attachments/assets/f67a58ce-9c84-45cc-8694-29bc30117be5)

![Image](https://github.com/user-attachments/assets/a0d70dfc-38a5-4f6f-a77a-c8cb4a707a11)

### Composite 종류
* Selector : 여러 행동 중 하나의 행동을 지정
* Sequence : 여러 행동을 차례로 모두 수행
* Parallel : 여러 행동을 함께 수행
   

### Composite 노드에 부착하는 기능들
* Decorator : Composite 노드가 실행되는 조건 지정
* Service : Composite 노드가 활성화 될 때 주기적으로 실행
* Abort : Decorator 조건에 부합되면 Composite 내 활동을 전부 중단
   

#### Decorator 사용예시
![Image](https://github.com/user-attachments/assets/8ed8c978-48ff-48a0-a909-0b938a24d313)


#### Abort 사용예시
![Image](https://github.com/user-attachments/assets/e96e6d68-a62a-40d7-83d4-749738a80456)

   
   
## 2. Behavior Tree 추가
![Image](https://github.com/user-attachments/assets/d67abdf5-d8ca-49c9-8dfb-6a61b0fc1b4a)

Content Browser 우클릭 > Artificial Intelligence 에서 Asset 생성가능
* Behavior Tree : 여러 패턴을 사용하게 될 행동트리
* Blackboard : Behavior Tree에서 사용할 변수들을 저장
   
   

## 3. Behavior Tree 코드
### 3-1. 선언
```cpp
UPROPERTY()
TObjectPtr<class UBlackboardData> BBAsset;

UPROPERTY()
TObjectPtr<class UBehaviorTree> BTAsset;
```
   

### 3-2. 생성
```cpp
static ConstructorHelpers::FObjectFinder<UBlackboardData> BBAssetRef(TEXT("/Script/AIModule.BlackboardData'/Game/ArenaBattle/AI/BB_ABCharacter.BB_ABCharacter'"));
if (nullptr != BBAssetRef.Object)
{
	BBAsset = BBAssetRef.Object;
}

static ConstructorHelpers::FObjectFinder<UBehaviorTree> BTAssetRef(TEXT("/Script/AIModule.BehaviorTree'/Game/ArenaBattle/AI/BT_ABCharacter.BT_ABCharacter'"));
if (nullptr != BTAssetRef.Object)
{
	BTAsset = BTAssetRef.Object;
}
```
   

### 3-3. Pawn이 빙의했을 때 함수 정의
```cpp
...
#include "BehaviorTree/BehaviorTree.h"
#include "BehaviorTree/BlackboardData.h"
#include "BehaviorTree/BlackboardComponent.h"
...
void AABAIController::OnPossess(APawn* InPawn)
{
	Super::OnPossess(InPawn);

	RunAI();
}

...

void AABAIController::RunAI()
{
	UBlackboardComponent* BlackboardPtr = Blackboard.Get();
	if (UseBlackboard(BBAsset, BlackboardPtr))
	{
		bool RunResult = RunBehaviorTree(BTAsset);
		ensure(RunResult);
	}
}
```
   

### 3-4. AI를 멈추는 함수 정의
```cpp
void AABAIController::StopAI()
{
	//BrainComponent AIController의 변수
	UBehaviorTreeComponent* BTComponent = Cast<UBehaviorTreeComponent>(BrainComponent);
	if (BTComponent)
	{
		BTComponent->StopTree();
	}
}
```