---
layout: single
title: UnrealVR_Grab Component
categories: unrealTheory
tag: [unreal]

toc: true
toc_sticky: true
---

기본 구조
{: .notice--info} 

### 1. Free 
	Grab component가 있으면 해당 물체를 그대로 집음
### 2. Obj to Grab
	Grab Component 기준으로 손에 물체를 전송
### 3. Grab to Obj
	손을 Grab Component가 있는 곳에 전송

   
   

Comepont 연산
{: .notice--info} 

|                 Rotation                     | Location    | 
| :------------------------------------: | :---: |
| ![Image](https://github.com/user-attachments/assets/ba07f44d-ece7-452f-8fd1-d68bc8adae50) | ![Image](https://github.com/user-attachments/assets/370445f3-83c0-4491-a1d2-3f74a31e7256)   |

   
   

연산해설
{: .notice--primary} 

| rotation 참고 | Location 참고 |
|:-------------:|:-------------:|
|   ![Image](https://github.com/user-attachments/assets/6ef37338-29b1-4e0c-8301-582eb69f406b)            |      ![Image](https://github.com/user-attachments/assets/3c530bb5-65ea-4406-87a0-b7d0039dd740)         |


Rotation, Location 모두 
1. 기존 Object(여기서는 Gun)의 Transform
2. Grab Componet가 가지고 있는 Transform
이 다르기 때문에 해당 연산을 통해서 역산하여 손에 붙이는 것이라고 이해하면 됨
