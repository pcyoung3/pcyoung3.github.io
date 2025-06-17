---
layout: single
title: 백준 15649번 - N과 M(1)
categories: algorithmProblems
tags: [algorithm, backtracking]
toc: true
toc_sticky: true
---

## 문제
[백준 15649번 문제](https://www.acmicpc.net/problem/15649)

자연수 N과 M이 주어졌을 때, 아래 조건을 만족하는 길이가 M인 수열을 모두 구하는 프로그램을 작성하시오.

- 1부터 N까지 자연수 중에서 중복 없이 M개를 고른 수열

## 입력

첫째 줄에 자연수 N과 M이 주어진다. (1 ≤ M ≤ N ≤ 8)

## 출력

한 줄에 하나씩 문제의 조건을 만족하는 수열을 출력한다. 중복되는 수열을 여러 번 출력하면 안되며, 각 수열은 공백으로 구분해서 출력해야 한다.

수열은 사전 순으로 증가하는 순서로 출력해야 한다.

## 예제 입력 1 복사

3 1

## 예제 출력 1 복사
```
1
2
3
```


## 예제 입력 2 복사

4 2

## 예제 출력 2 복사
```
1 2
1 3
1 4
2 1
2 3
2 4
3 1
3 2
3 4
4 1
4 2
4 3
```


## 예제 입력 3 복사

4 4

## 예제 출력 3 복사
```
1 2 3 4
1 2 4 3
1 3 2 4
1 3 4 2
1 4 2 3
1 4 3 2
2 1 3 4
2 1 4 3
2 3 1 4
2 3 4 1
2 4 1 3
2 4 3 1
3 1 2 4
3 1 4 2
3 2 1 4
3 2 4 1
3 4 1 2
3 4 2 1
4 1 2 3
4 1 3 2
4 2 1 3
4 2 3 1
4 3 1 2
4 3 2 1
```

### 1-1. 풀이 및 정답코드

1부터 4까지 길이가 3개인 수열을 만든다고 가정하면

![Pasted image 20230627114343.png](/assets/images/algorithm/Pasted image 20230627114343.png)

다음과 같은 방식으로 이루어질 수 있다.
현재 1, 2인 상태에서 마지막 칸에 올 수 있는 숫자는 3과 4이므로 상태를 바꿔가면서 진행하는 것에 익숙해지면 된다.

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n, m;
int arr[10];
bool isUsed[10];

void BackTracking(int k)
{
	if (k == m) //m은 수열의 개수, 즉, k가 m과 같으면 재귀 탈출 조건
	{
		for (int i = 0; i < m; ++i)
			cout << arr[i] << ' ';
		cout << '\n';
		return;
	}

	for (int i = 1; i <= n; ++i)
	{
		if (!isUsed[i])
		{
			arr[k] = i; //출력용 k번째 수열에 i 숫자 삽입
			isUsed[i] = 1; //i숫자가 사용된 것을 표시
			BackTracking(k + 1); //k+1번째 수열에 들어갈 숫자탐색
			//arr[k] = 0;	//초기화 안해도 되는데 보기 편하려고 함-> 위에서 덮어씀
			isUsed[i] = 0; //재귀에서 나가므로 해당 사용기록을 초기화
		}
	}
}

int main()
{
    ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	
	cin >> n >> m;
	BackTracking(0);	//수열 0번째부터 탐색 시작
}
{% endhighlight %}

n, m, k, i 가 헷갈리면 다음 그림 참고

![KakaoTalk_20230627_155820646.jpg](/assets/images/algorithm/KakaoTalk_20230627_155820646.jpg)
