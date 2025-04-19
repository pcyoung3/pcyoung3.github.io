---
layout: single
title: 바킹독_0_표준입출력 등의 팁
categories: algorithmTheory
tag: [algorithm, algorithmTheory]

toc: true
toc_sticky: true
---
## getline 함수를 이용
공백이 담긴 문자를 입력받아야 할 때
{: .notice--info}

```
string s;
getline(cin, s);
cout << s;
```

   
   

## cin, cout 의 최적화
cin, cout을 쓸 때 주의점
{: .notice--info}
```cpp
ios::sync_with_stdio(0)    // c, c++ 의 string stream 동기화 함수 -> 동기화 기능을 끄겠다는 뜻 : 이 호출 이후 printf/scanf 를 사용하면 안됨
cin.tie(0)                 // 버퍼를 모으지 않고 cin의 버퍼를 비워주는 함수 -> 인데 어짜피 출력값만 보일 것이기 때문에 기능을 끈다
```
해당 함수를 호출 후 사용
   
   
<div class="notice--danger" markdown="1">
**<u>endl 을 사용하지 말자</u>** 

버퍼를 비우기 때문에 최적화만 깎아먹어서 코딩테스트에서는 사용하지 않는 것을 추천
</div>