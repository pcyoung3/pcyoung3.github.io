---
layout: single
title: 백준 1780번 - 종이의 개수
categories: algorithmProblems
tags: [algorithm, recursive]
toc: true
toc_sticky: true
---

## 문제
[백준 1780번 문제](https://www.acmicpc.net/problem/1780)

N×N크기의 행렬로 표현되는 종이가 있다. 종이의 각 칸에는 -1, 0, 1 중 하나가 저장되어 있다. 우리는 이 행렬을 다음과 같은 규칙에 따라 적절한 크기로 자르려고 한다.

1. 만약 종이가 모두 같은 수로 되어 있다면 이 종이를 그대로 사용한다.
2. (1)이 아닌 경우에는 종이를 같은 크기의 종이 9개로 자르고, 각각의 잘린 종이에 대해서 (1)의 과정을 반복한다.

이와 같이 종이를 잘랐을 때, -1로만 채워진 종이의 개수, 0으로만 채워진 종이의 개수, 1로만 채워진 종이의 개수를 구해내는 프로그램을 작성하시오.

## 입력

첫째 줄에 N(1 ≤ N ≤ 37, N은 3k 꼴)이 주어진다. 다음 N개의 줄에는 N개의 정수로 행렬이 주어진다.

## 출력

첫째 줄에 -1로만 채워진 종이의 개수를, 둘째 줄에 0으로만 채워진 종이의 개수를, 셋째 줄에 1로만 채워진 종이의 개수를 출력한다.

## 예제 입력 1 복사
```
9
0 0 0 1 1 1 -1 -1 -1
0 0 0 1 1 1 -1 -1 -1
0 0 0 1 1 1 -1 -1 -1
1 1 1 0 0 0 0 0 0
1 1 1 0 0 0 0 0 0
1 1 1 0 0 0 0 0 0
0 1 -1 0 1 -1 0 1 -1
0 -1 1 0 1 -1 0 1 -1
0 1 -1 1 0 -1 0 1 -1
```


## 예제 출력 1 복사

10
12
11
   
---
## 풀이
<div class="notice--warning" markdown="1">
**<u>반례</u>** <br>
{% highlight text linenos%}
3
0 0 0
0 0 0
0 0 1
{% endhighlight %}
</div>
   
### 내가 푼 코드
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

enum Score
{
	minus = -1,
	zero,
	plus
};

int cnt[3];
int arr[2200][2200];
int n;


void func(int firstX, int lastX, int firstY, int lastY);
bool IsSame(int firstX, int lastX, int firstY, int lastY);
int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	for (int i = 0; i < n; ++i)
		for (int j = 0; j < n; ++j)
			cin >> arr[i][j];
	func(0, n-1, 0, n-1);

	cout << cnt[0] << '\n';
	cout << cnt[1] << '\n';
	cout << cnt[2] << '\n';
}

void func(int firstX, int lastX, int firstY, int lastY)
{
	
	//현재 들어온 범위가 전부 같은지 검사
	if (IsSame(firstX, lastX, firstY, lastY))
	{
		if (Score::minus == arr[firstX][firstY])
			cnt[0]++;
		else if (Score::zero == arr[firstX][firstY])
			cnt[1]++;
		else
			cnt[2]++;
		return;
	}
	
	//현재 사각형을 x를 3등분 y를 3등분
	int gap = (lastX - firstX + 1) / 3;
	for (int i = 0; i < 3; i++)
	{
		for (int j = 0; j < 3; j++)
		{
			int fx, lx, fy, ly;
			fx = firstX + (gap * i);
			lx = fx + gap -1;
			fy = firstY + (gap * j);
			ly = fy + gap - 1;
			func(fx, lx, fy, ly);	// 9번의 재귀호출
		}
	}
}

bool IsSame(int firstX, int lastX, int firstY, int lastY)
{
	int firstNum = arr[firstX][firstY];
	for (int i = firstX; i <= lastX; ++i)
		for (int j = firstY; j <= lastY; ++j)
			if (firstNum != arr[i][j])
				return false;
	return true;
}
{% endhighlight %}
   
### 다른풀이(좀 더 간단한 인덱스 계산 참고)
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int N;
int paper[2200][2200];
int cnt[3]; //-1, 0, 1로 채워진 종이 갯수

//해당 종이 내부에 같은 숫자로만 채워졌는지 확인하는 함수
bool check(int x, int y, int n) {
  for (int i = x; i < x + n; i++)
  for (int j = y; j < y + n; j++)
    if (paper[x][y] != paper[i][j])
    return false;
  return true;
}
void solve(int x, int y, int z)
{
  if (check(x, y, z)) {
    cnt[paper[x][y] + 1] += 1;
    return;
  }
  int n = z / 3;
  for (int i = 0; i < 3; i++)
  for (int j = 0; j < 3; j++)
    solve(x + i * n, y + j * n, n);
}
int main(void) {
  ios::sync_with_stdio(0);
  cin.tie(0);
  cin >> N;
  for (int i = 0; i < N; i++)
  for (int j = 0; j < N; j++)
    cin >> paper[i][j];
  solve(0, 0, N);
  for (int i = 0; i < 3; i++) cout << cnt[i] << "\n";
}
{% endhighlight %}
