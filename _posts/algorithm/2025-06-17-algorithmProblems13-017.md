---
layout: single
title: 백준 1941번 - 소문난 칠공주
categories: algorithmProblems
tags: [algorithm, backtracking]
toc: true
toc_sticky: true
---

## 문제
[백준 1941번 문제](https://www.acmicpc.net/problem/1941)

총 25명의 여학생들로 이루어진 여학생반은 5×5의 정사각형 격자 형태로 자리가 배치되었고, 얼마 지나지 않아 이다솜과 임도연이라는 두 학생이 두각을 나타내며 다른 학생들을 휘어잡기 시작했다. 곧 모든 여학생이 '이다솜파'와 '임도연파'의 두 파로 갈라지게 되었으며, 얼마 지나지 않아 '임도연파'가 세력을 확장시키며 '이다솜파'를 위협하기 시작했다.

위기의식을 느낀 '이다솜파'의 학생들은 과감히 현재의 체제를 포기하고, '소문난 칠공주'를 결성하는 것이 유일한 생존 수단임을 깨달았다. '소문난 칠공주'는 다음과 같은 규칙을 만족해야 한다.

1. 이름이 이름인 만큼, 7명의 여학생들로 구성되어야 한다.
2. 강한 결속력을 위해, 7명의 자리는 서로 가로나 세로로 반드시 인접해 있어야 한다.
3. 화합과 번영을 위해, 반드시 '이다솜파'의 학생들로만 구성될 필요는 없다.
4. 그러나 생존을 위해, '이다솜파'가 반드시 우위를 점해야 한다. 따라서 7명의 학생 중 '이다솜파'의 학생이 적어도 4명 이상은 반드시 포함되어 있어야 한다.

여학생반의 자리 배치도가 주어졌을 때, '소문난 칠공주'를 결성할 수 있는 모든 경우의 수를 구하는 프로그램을 작성하시오.

## 입력

'S'(이다'솜'파의 학생을 나타냄) 또는 'Y'(임도'연'파의 학생을 나타냄)을 값으로 갖는 5*5 행렬이 공백 없이 첫째 줄부터 다섯 줄에 걸쳐 주어진다.

## 출력

첫째 줄에 '소문난 칠공주'를 결성할 수 있는 모든 경우의 수를 출력한다.

## 예제 입력 1 복사
```
YYYYY
SYSYS
YYYYY
YSYYS
YYYYY
```

## 예제 출력 1 복사
```
2
```

## 힌트
가능한 방법은 아래와 같다.
```
.....    .....
SYSYS    SYSYS
....Y    .Y...
....S    .S...
.....    .....
```
   
## 풀이

한쪽방향으로 계속 진행하는 것이 아니라 중간에 갈림길이 나올 수 있으므로
일반적인 BFS나 BackTracking으로 해결할 수 없음

![KakaoTalk_20230720_144014269.jpg](/assets/images/algorithm/KakaoTalk_20230720_144014269.jpg)

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

const int maxIndex = 5;

char board[maxIndex][maxIndex];
bool mask[maxIndex * maxIndex];

int dx[4] = { 1, 0, -1, 0 };
int dy[4] = { 0, 1, 0, -1 };

bool BFS();
int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int ans = 0;
	for (int i = 0; i < maxIndex; i++)
		cin >> board[i];

	//25명중 칠공주가 될 사람의 후보 조합을 뽑음
	//000000011111...
	fill(mask + 7, mask + 25, 1);

	//mask의 수열을 진행시키면서 회차에 방문할 칸들을 표시
	//즉, 한 회차 돌릴 때 칸이 전부 같지 않음
	//또한 7칸이 애초에 붙지 않는 경우도 있는데 그 경우 어짜피 bfs과정에서 걸러짐
	do 
	{
		if(BFS())
			ans++;
	} while (next_permutation(mask, mask+25));

	cout << ans;
}

bool BFS()
{
	bool result = false;
	queue<pair<int, int>> Q;
	bool vis[maxIndex][maxIndex] = {};	//탐색하면서 방문표시
	bool idx[maxIndex][maxIndex] = {};	//이번 회차에 탐색할 칸들
	int length = 0;	//가로세로로 인접한 사람의 수
	int numOfS = 0;	//구성원 중 이다솜파의 수
	for (int i = 0; i < maxIndex; i++)
	{
		for (int j = 0; j < maxIndex; j++)
		{
			int index = i * 5 + j;
			if (!mask[index])
			{
				idx[i][j] = true;	//이번회차에 탐색할 칸들을 표시
				if (Q.empty())	//queue에 1개 원소 삽입
				{
					Q.push({ i,j });
					vis[i][j] = 1;
				}
			}
		}
	}

	while (!Q.empty())
	{
		auto cur = Q.front();
		Q.pop();

		length++; //7자리가 BFS로 붙어있는지 확인
		if (board[cur.first][cur.second] == 'S')
			numOfS++;	//해당 자리가 이다솜파인지 확인

		for (int dir = 0; dir < 4; dir++)
		{
			int nx = cur.first + dx[dir];
			int ny = cur.second + dy[dir];
			//bfs의 인덱스 예외처리
			if (nx < 0 || nx >= maxIndex || ny < 0 || ny >= maxIndex)
				continue;
			//탐색할 칸이거나 이미 방문한게 아니면 continue
			if (!idx[nx][ny] || vis[nx][ny])
				continue;

			vis[nx][ny] = 1;
			Q.push({ nx, ny });
		}
	}
	if (length >= 7 && numOfS >= 4)
		result = true;

	return result;
}
{% endhighlight %}
