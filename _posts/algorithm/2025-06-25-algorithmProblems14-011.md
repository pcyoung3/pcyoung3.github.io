---
layout: single
title: 백준 14889번 - 스타트와 링크
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 14889번 문제](https://www.acmicpc.net/problem/14889) <br>
오늘은 스타트링크에 다니는 사람들이 모여서 축구를 해보려고 한다. 축구는 평일 오후에 하고 의무 참석도 아니다. 축구를 하기 위해 모인 사람은 총 N명이고 신기하게도 N은 짝수이다. 이제 N/2명으로 이루어진 스타트 팀과 링크 팀으로 사람들을 나눠야 한다.

BOJ를 운영하는 회사 답게 사람에게 번호를 1부터 N까지로 배정했고, 아래와 같은 능력치를 조사했다. 능력치 Sij는 i번 사람과 j번 사람이 같은 팀에 속했을 때, 팀에 더해지는 능력치이다. 팀의 능력치는 팀에 속한 모든 쌍의 능력치 Sij의 합이다. Sij는 Sji와 다를 수도 있으며, i번 사람과 j번 사람이 같은 팀에 속했을 때, 팀에 더해지는 능력치는 Sij와 Sji이다.

N=4이고, S가 아래와 같은 경우를 살펴보자.

|i\j|1|2|3|4|
|---|---|---|---|---|
|1||1|2|3|
|2|4||5|6|
|3|7|1||2|
|4|3|4|5||

예를 들어, 1, 2번이 스타트 팀, 3, 4번이 링크 팀에 속한 경우에 두 팀의 능력치는 아래와 같다.

- 스타트 팀: S12 + S21 = 1 + 4 = 5
- 링크 팀: S34 + S43 = 2 + 5 = 7

1, 3번이 스타트 팀, 2, 4번이 링크 팀에 속하면, 두 팀의 능력치는 아래와 같다.

- 스타트 팀: S13 + S31 = 2 + 7 = 9
- 링크 팀: S24 + S42 = 6 + 4 = 10

축구를 재미있게 하기 위해서 스타트 팀의 능력치와 링크 팀의 능력치의 차이를 최소로 하려고 한다. 위의 예제와 같은 경우에는 1, 4번이 스타트 팀, 2, 3번 팀이 링크 팀에 속하면 스타트 팀의 능력치는 6, 링크 팀의 능력치는 6이 되어서 차이가 0이 되고 이 값이 최소이다.

## 입력

첫째 줄에 N(4 ≤ N ≤ 20, N은 짝수)이 주어진다. 둘째 줄부터 N개의 줄에 S가 주어진다. 각 줄은 N개의 수로 이루어져 있고, i번 줄의 j번째 수는 Sij 이다. Sii는 항상 0이고, 나머지 Sij는 1보다 크거나 같고, 100보다 작거나 같은 정수이다.

## 출력

첫째 줄에 스타트 팀과 링크 팀의 능력치의 차이의 최솟값을 출력한다.

## 예제 입력 1 복사

```
4
0 1 2 3
4 0 5 6
7 1 0 2
3 4 5 0
```

## 예제 출력 1 복사

0

## 풀이

<div class="notice--info" markdown="1">
**<u>팀을 결정하거나 해당 팀에서 2명의 선수를 뽑을 때 순열을 이용해서 해결</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int scoreBoard[22][22];
int n;

int GetDiffTeamScore(vector<int>& teamStart, vector<int>& teamLink);

int main() 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	for (int i = 0; i < n; i++)
		for (int j = 0; j < n; j++)
			cin >> scoreBoard[i][j];

	int ans = 0x7f7f7f7f;
	int mask[22];
	fill(mask, mask + n / 2, 0);
	fill(mask + n / 2, mask + n, 1);
	do   //2개의 팀을 구하는 과정
	{
		vector<int> teamStart, teamLink;	//각 팀의 index가 들어갈 배열
		teamStart.reserve(n), teamLink.reserve(n);
		for (int i = 0; i < n; i++)
		{
			if (mask[i])
				teamStart.push_back(i);
			else
				teamLink.push_back(i);
		}
		ans = min(GetDiffTeamScore(teamStart, teamLink), ans);
			
	} while (next_permutation(mask, mask + n));

	cout << ans;
}

int GetDiffTeamScore(vector<int>& teamStart, vector<int>& teamLink)
{
	int scoreStart = 0, scoreLink = 0;
	size_t teamSize = teamStart.size();
	vector<int> mask;
	
	mask.reserve(teamSize);
	for (int i = 0; i < teamSize; i++)
	{
		if (i < teamSize - 2)	//mask는 {0, 0, ... 0, 1, 1}로 끝에 2개만 1인 배열
			mask.push_back(0);
		else
			mask.push_back(1);
	}

	do	//각 팀에서 2명을 뽑아 점수를 합산
	{
		int startIndex[2], linkIndex[2];	//각 팀의 이번회차 선수 2명의 index 저장
		int index = 0;
		for (int i = 0; i < mask.size(); i++)	//선수 2명의 index를 뽑는 과정
		{
			if (mask[i])
			{
				startIndex[index] = teamStart[i];
				linkIndex[index] = teamLink[i];
				index++;
			}
		}
		//start팀의 2명의 선수가 2, 3 인덱스라면 scoreBoard[2][3] + scoreBoard[3][1]
		scoreStart += scoreBoard[startIndex[0]][startIndex[1]] + scoreBoard[startIndex[1]][startIndex[0]];
		scoreLink += scoreBoard[linkIndex[0]][linkIndex[1]] + scoreBoard[linkIndex[1]][linkIndex[0]];
	} while (next_permutation(mask.begin(), mask.end()));

	return abs(scoreStart - scoreLink);
}
{% endhighlight %}

## 다른풀이

<div class="notice--info" markdown="1">
**<u>선수 2명을 뽑는 과정을 2중for문으로 해결할 수도 있다</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n;
int a[25][25];

int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	for (int i = 0; i < n; i++)
		for (int j = 0; j < n; j++)
			cin >> a[i][j];

	vector<int> team(n);
	fill(team.begin() + n / 2, team.end(), 1);

	int ans = 0x7f7f7f7f;

	// team = {0, 0, 0, .., 0, 1, 1, .. ,1}, team[i]는 i번째 선수의 팀을 의미
	do {
		int diff = 0; // 능력치의 차이
		for (int i = 0; i < n; i++)	//모든 원소를 비교(선택정렬과 유사)
		{
			for (int j = i + 1; j < n; j++) 
			{
				if (team[i] != team[j])	//같은 선수일 때는 계산할 필요 X
					continue;
				if (team[i] == 0)	//팀0번은 diff에 능력치를 더해줌
					diff += (a[i][j] + a[j][i]);
				else                //팀1번은 diff에 능력치를 빼줌 -> 이러면 0번과 1번의 차이가 diff에 저장
					diff -= (a[i][j] + a[j][i]);
			}
		}
		ans = min(ans, abs(diff));
	} while (next_permutation(team.begin(), team.end()));

	cout << ans;
}
{% endhighlight %}