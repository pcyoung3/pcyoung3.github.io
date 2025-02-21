---
layout: single
title: User-defined Literals
categories: cpp
tag: [cpp, modernCpp]

toc: true
toc_sticky: true
---

## 1. C++ 고유 리터럴
C++에는 코드에 직접 삽입된 값, 변경할 수 없으므로 상수라고 불림

### 리터럴 예시
```cpp
void Test()
{
	bool b = true;				//bool 상수 true
	int i = 5;					//int 상수 '5'
	double pi = 3.141592;		//double 상수 3.14
	double bigNum = 6.02e23;	//6.02 * 10^23
	double smallNum = 1.6e-19;	//1.6 * 10^-19
	unsigned int uIntVal = 5u;	//unsigned int
	long nLongVal = 5L;			//long
	float fValue = 11.f;		//float
	
	char c = 'A';				//문자 char 형
	const char* cs = "Hello";	//문자열

	using namespace std::literals;
	std::string s = "hello"s;		//string class

	int octal = 012;	//8진수 리터럴 -> 10
	int hex = 0x12;		//16진수 리터럴 -> 18
}
```
<div class="notice--warning" markdown="1">
**<u>c++에는 2가지 상수가 있다</u>** 

첫번째는 위에 있는 Literal 상수이고 두번째는 Symbolic 상수이다
심볼릭 상수란 const, define, enum 등으로 정의된 상수를 의미
</div>

   
   
### 2. 사용자 정의 리터럴
위의 컴파일러에서 미리 정의된 리터럴이 아닌 사용자가 직접 literal을 정의 할 수 있다
`operator""`를 이용해서 정의

사용자 정의 리터럴에는 `_`를 무조건 붙여야 함
{: .notice--danger} 


### 예시코드
```cpp
#include <iostream>

long double operator"" _km(long double val) { return std::round(val * 100. * 1000.); }
long double operator"" _m(long double val) { return std::round(val * 100.); }
long double operator"" _cm(long double val) { return std::round(val); }

int main()
{
	if (1.5_km - 1500._m < 1e-12)
		std::cout << "km와 m" << std::endl;

	if (10.0_m - 1000.0_cm < 1e-12)
		std::cout << "m와 cm" << std::endl;

	return 0;
}
```
코드 가독성과 단위를 표현할 때 요긴하게 사용가능