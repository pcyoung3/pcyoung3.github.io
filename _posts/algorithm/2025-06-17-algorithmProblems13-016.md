---
layout: single
title: 백준 16987번 - 계란으로 계란치기
categories: algorithmProblems
tags: [algorithm, backtracking]
toc: true
toc_sticky: true
---

## 문제
[백준 16987번 문제](https://www.acmicpc.net/problem/16987)

원래 프로그래머의 기본 소양은 팔굽혀펴기를 단 한 개도 할 수 없는 것이라고 하지만 인범이는 3대 500을 넘기는 몇 안되는 프로그래머 중 한 명이다. 인범이는 BOJ에서 틀린 제출을 할 때마다 턱걸이를 5회 하는 기적의 운동 루틴을 통해 뇌와 근육을 동시에 단련한다. 근육을 단련할 때 식단이 정말로 중요하다는 것을 아는 인범이는 탄수화물이 많은 밥이나 빵 따위의 아침 식사를 대신해 단백질이 많은 계란찜을 해먹는다. 계란찜을 먹기 위해서는 계란을 깨야 하는데, 인범이는 힘이 너무 넘치는 나머지 부엌의 대리석을 이용해 계란을 깨면 늘 껍데기가 산산조각나 뒷처리가 너무 어렵게 되곤 한다. 어떻게 하면 계란을 조심스럽게 깰 수 있을까 고민하던 인범이에게 유현이는 굉장히 좋은 해결책을 알려주었다. 바로 계란으로 계란을 치는 것이다. 계란끼리 부딪쳐보니 껍데기가 아주 예쁘게 갈라지는 것을 발견한 인범이는 앞으로 계란으로 계란을 쳐서 식사 준비를 해야겠다고 생각했다. 유현이는 더 나아가 식사 준비를 할 때에도 두뇌를 단련할 수 있는 좋은 퍼즐을 인범이에게 알려주었다.

계란으로 계란을 치게 되면 각 계란의 내구도는 상대 계란의 무게만큼 깎이게 된다. 그리고 내구도가 0 이하가 되는 순간 계란은 깨지게 된다.

유현이가 인범이에게 알려준 퍼즐은 일렬로 놓여있는 계란에 대해 왼쪽부터 차례로 들어서 한 번씩만 다른 계란을 쳐 최대한 많은 계란을 깨는 문제였다.

## 입력

첫째 줄에 계란의 수를 나타내는 N(1 ≤ N ≤ 8)가 주어진다. 그 다음 N개의 줄에는 계란의 내구도와 무게에 대한 정보가 주어진다. i+1번째 줄에는 왼쪽에서 i번째에 위치한 계란의 내구도 Si(1 ≤ Si ≤ 300)와 무게 Wi(1 ≤ Wi ≤ 300)가 한 칸의 빈칸을 사이에 두고 주어진다.

## 출력

첫째 줄에 인범이가 깰 수 있는 계란의 최대 개수를 출력한다.

## 예제 입력 1 복사
```
3
8 5
1 100
3 5
```

## 예제 출력 1 복사
```
3
```
   
## 풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

#define S first
#define W second

pair<int, int> arr[10];
int n;
int result;

void BackTracking(int length, int broken);

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	for (int i = 0; i < n; i++)
	{
		cin >> arr[i].S;
		cin >> arr[i].W;
	}

	BackTracking(0, 0);

	cout << result;
}

void BackTracking(int length, int broken)
{
	if(length == n)	//재귀종료조건
	{
		result = max(result, broken);
		return;
	}
	if (arr[length].S <= 0) //현재 내려치려는 계란이 이미 깨져있는 경우
	{	//다음 계란으로 넘어감
		BackTracking(length + 1, broken);
		return;
	}	

	auto cur = arr[length];	//현재 내려치려는 계란 백업
	for (int i = 0; i < n; i++)
	{
		if (arr[i].S <= 0 || length == i)
			continue;

		int brokenEgg = broken;
		auto other = arr[i]; //부딪히는 계란 백업

		//계란끼리 충돌
		arr[length].S -= other.W;
		arr[i].S -= cur.W;
		if (arr[length].S <= 0)
			brokenEgg++;
		if (arr[i].S <= 0)
			brokenEgg++;
		
		//다음 계란으로 넘어감
		BackTracking(length + 1, brokenEgg);
		
		//충돌한 계란 값 다시 복구
		arr[length] = cur;
		arr[i] = other;
	}

	//마지막 계란을 제외한 모든 계란이 깨졌을 때
	//위에 있는 for문을 타지 않아서 재귀종료조건을 타지 않을 경우 대비
	result = max(result, broken);
}
{% endhighlight %}
   
## 다른사람 풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;
int n;
int s[10],w[10];
int mx = 0;
int cnt = 0; // 깨져있는 계란의 수

void solve(int a){ // a번째 계란으로 다른걸 깰 차례
  if(a == n){
    mx = max(mx,cnt);
    return;
  }
  if(s[a] <= 0 or cnt == n-1){
    solve(a+1);
    return;
  } // a번째 계란이 깨져있거나 다른 모든 계란이 깨져있으면 넘어감
  for(int t = 0; t < n; t++){ // t번째 계란을 내려치고 싶다
    if(t == a or s[t] <= 0) continue;
    s[a] -= w[t];
    s[t] -= w[a];
    if(s[a] <= 0) cnt++;
    if(s[t] <= 0) cnt++;
    solve(a+1);
    if(s[a] <= 0) cnt--;
    if(s[t] <= 0) cnt--;    
    s[a] += w[t];
    s[t] += w[a];
  }
}

int main(void) {
  ios::sync_with_stdio(0);
  cin.tie(0);
  cin >> n;
  for(int i = 0; i < n; i++){
    cin >> s[i] >> w[i];
  }
  solve(0);
  cout << mx;  
}
{% endhighlight %}
