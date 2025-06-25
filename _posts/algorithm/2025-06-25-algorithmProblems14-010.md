---
layout: single
title: 백준 15683번 - 감시
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 15683번 문제](https://www.acmicpc.net/problem/15683) <br>
스타트링크의 사무실은 1×1크기의 정사각형으로 나누어져 있는 N×M 크기의 직사각형으로 나타낼 수 있다. 사무실에는 총 K개의 CCTV가 설치되어져 있는데, CCTV는 5가지 종류가 있다. 각 CCTV가 감시할 수 있는 방법은 다음과 같다.

|                  |                  |                  |                  |     |
| ---------------- | ---------------- | ---------------- | ---------------- | --- |
| ![15683_1.png](/assets/images/algorithm/15683_1.png) | ![15683_2.png](/assets/images/algorithm/15683_2.png) | ![15683_3.png](/assets/images/algorithm/15683_3.png) | ![15683_4.png](/assets/images/algorithm/15683_4.png) | ![15683_5.png](/assets/images/algorithm/15683_5.png)    |
| 1번              | 2번              | 3번              | 4번              | 5번 |

1번 CCTV는 한 쪽 방향만 감시할 수 있다. 2번과 3번은 두 방향을 감시할 수 있는데, 2번은 감시하는 방향이 서로 반대방향이어야 하고, 3번은 직각 방향이어야 한다. 4번은 세 방향, 5번은 네 방향을 감시할 수 있다.

CCTV는 감시할 수 있는 방향에 있는 칸 전체를 감시할 수 있다. 사무실에는 벽이 있는데, CCTV는 벽을 통과할 수 없다. CCTV가 감시할 수 없는 영역은 사각지대라고 한다.

CCTV는 회전시킬 수 있는데, 회전은 항상 90도 방향으로 해야 하며, 감시하려고 하는 방향이 가로 또는 세로 방향이어야 한다.

사무실의 크기와 상태, 그리고 CCTV의 정보가 주어졌을 때, CCTV의 방향을 적절히 정해서, 사각 지대의 최소 크기를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 사무실의 세로 크기 N과 가로 크기 M이 주어진다. (1 ≤ N, M ≤ 8)

둘째 줄부터 N개의 줄에는 사무실 각 칸의 정보가 주어진다. 0은 빈 칸, 6은 벽, 1~5는 CCTV를 나타내고, 문제에서 설명한 CCTV의 종류이다. 

CCTV의 최대 개수는 8개를 넘지 않는다.

## 출력

첫째 줄에 사각 지대의 최소 크기를 출력한다.

## 예제 입력 1 복사

```
4 6
0 0 0 0 0 0
0 0 0 0 0 0
0 0 1 0 6 0
0 0 0 0 0 0
```

## 예제 출력 1 복사

20

## 풀이
{% highlight cpp linenos%}
{% raw %}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

struct TCCTV
{
	vector<vector<pair<int, int>>> vecLook;
	int dir;
};

void SetCCTV();
void BackTrackingCCTV(int length, int cctvCount);
void DrawCCTVDir(int row, int col, TCCTV& cctv, queue<pair<int, int>>& draws);
void UndrawCCTVDir(queue<pair<int, int>>& draws);

TCCTV tv[5];
vector<pair<int, int>> cctvs;
int board[10][10];
int n, m;
int ans = 9999;

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	SetCCTV();

	cin >> n >> m;
	for (int i = 0; i < n; i++)
		for (int j = 0; j < m; j++)
			cin >> board[i][j];

	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < m; j++)
		{
			if (board[i][j] == 0 || board[i][j] == 6)
				continue;
			cctvs.push_back({ i, j });
		}
	}
	
	BackTrackingCCTV(0, 0);

	cout << ans;
}

//CCTV 데이터 셋팅
void SetCCTV()
{
	//1번 CCTV
	vector<vector<pair<int, int>>> input0
	{ 
		{{1, 0}},
		{{0, -1}},
		{{-1, 0}},
		{{0, 1}}
	};
	tv[0].vecLook = input0;
	tv[0].dir = 0;

	//2번 CCTV
	vector<vector<pair<int, int>>> input1
	{
		{{1, 0}, {-1, 0}},
		{{0, 1}, {0, -1}}
	};
	tv[1].vecLook = input1;
	tv[1].dir = 0;

	//3번 CCTV
	vector<vector<pair<int, int>>> input2
	{
		{{-1, 0}, {0, 1}},
		{{0, 1}, {1, 0}},
		{{1, 0}, {0, -1}},
		{{0, -1}, {-1, 0}}
	};
	tv[2].vecLook = input2;
	tv[2].dir = 0;

	//4번 CCTV
	vector<vector<pair<int, int>>> input3
	{
		{{-1, 0}, {0, 1}, {1, 0}},
		{{0, 1}, {1, 0}, {0, -1}},
		{{1, 0}, {0, -1}, {-1, 0}},
		{{0, -1}, {-1, 0}, {0, 1}}
	};
	tv[3].vecLook = input3;
	tv[3].dir = 0;

	//5번 CCTV
	vector<vector<pair<int, int>>> input4
	{
		{{-1, 0}, {0, 1}, {1, 0}, {0, -1}}
	};
	tv[4].vecLook = input4;
	tv[4].dir = 0;
}

//백트레킹 함수
void BackTrackingCCTV(int length, int cctvCount)
{
	if (length == cctvs.size()) //모든 CCTV를 체크한 경우
	{
		int result = 0;
		for (int i = 0; i < n; i++)
			for (int j = 0; j < m; j++)
				if (board[i][j] == 0)
					result++;

		ans = min(ans, result); //최소 사각지대
		return;
	}

	for (int i = cctvCount; i < cctvs.size(); i++)
	{
		int nx = cctvs[i].X;
		int ny = cctvs[i].Y;
		int cctvType = board[nx][ny];
		queue<pair<int, int>> markedIdx; //이번 cctv의 감시구역 복구용
		
		TCCTV curTV = tv[cctvType - 1];
		for (int j = 0; j < curTV.vecLook.size(); j++)
		{
			curTV.dir = j;
			DrawCCTVDir(nx, ny, curTV, markedIdx); //cctv 1개 감시구역 표기
			BackTrackingCCTV(length + 1, i + 1); //다음 cctv검사
			UndrawCCTVDir(markedIdx); //이번 cctv 감시구역 복구
		}
	}
}

//cctv 1개 감시구역 표기
void DrawCCTVDir(int row, int col, TCCTV& cctv, queue<pair<int, int>>& draws)
{
	auto cur = cctv.vecLook[cctv.dir];
	for (int i = 0; i < cur.size(); i++)
	{
		int nx = 0, ny = 0;
		int dist = 1;
		while (1)
		{
			nx = row + cur[i].X * dist;
			ny = col + cur[i].Y * dist;
			if (nx < 0 || nx >= n || ny < 0 || ny >= m)
				break;
			if (board[nx][ny] == 6)
				break;
			if (board[nx][ny] == 0)
			{
				draws.push({ nx, ny });
				board[nx][ny] = -1;
			}
			dist++;
		}
	}
}

//cctv 감시구역 원복
void UndrawCCTVDir(queue<pair<int, int>>& draws)
{
	while (!draws.empty())
	{
		auto cur = draws.front();
		draws.pop();

		board[cur.X][cur.Y] = 0;
	}
}
{% endraw %}
{% endhighlight %}

## 다른풀이

<div class="notice--info" markdown="1">
**<u>CCTV의 4방향을 표현하기 위해 4진수로 문제를 해결</u>**
</div>

![15683_ans_1.png](/assets/images/algorithm/15683_ans_1.png)
![15683_ans_2.png](/assets/images/algorithm/15683_ans_2.png)
![KakaoTalk_20230726_083827317.jpg](/assets/images/algorithm/KakaoTalk_20230726_083827317.jpg)

### 4진수 예시코드
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int a, b;
    a = 4 << 1;             // a == 8
    b = 1 << 1 * 2;         // b == 4

    for (int tmp = 0; tmp < 64; tmp++)
    {
        int brute = tmp;
        for (int i = 0; i < 3; i++)
        {
            cout << brute % 4;
            brute /= 4;
        }
        cout << "\n";
    }
    return 0;
}
{% endhighlight %}

### 문제 해답
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

int dx[4] = { 1,0,-1,0 };
int dy[4] = { 0,1,0,-1 }; // 남쪽, 동쪽, 북쪽, 서쪽 순서
int n, m;
int board1[10][10]; // 최초에 입력받은 board를 저장할 변수
int board2[10][10]; // 사각 지대의 개수를 세기 위해 사용할 변수
vector<pair<int, int> > cctv; // cctv의 좌표를 저장할 변수

bool OOB(int a, int b) 
{ // Out Of Bounds 확인
    return a < 0 || a >= n || b < 0 || b >= m;
}

// (x,y)에서 dir 방향으로 진행하면서 벽을 만날 때 까지 지나치는 모든 빈칸을 7로 바꿔버림
void upd(int x, int y, int dir) 
{
    dir %= 4;
    while (1) 
    {
        x += dx[dir];
        y += dy[dir];
        if (OOB(x, y) || board2[x][y] == 6) 
            return; // 범위를 벗어났거나 벽을 만나면 함수를 탈출
        if (board2[x][y] != 0) 
            continue; // 해당 칸이 빈칸이 아닐 경우(=cctv가 있을 경우) 넘어감
        board2[x][y] = 7; // 빈칸을 7로 덮음
    }
}

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    cin >> n >> m;
    int mn = 0; // 사각 지대의 최소 크기 (=답)
    for (int i = 0; i < n; i++) 
    {
        for (int j = 0; j < m; j++) 
        {
            cin >> board1[i][j];
            if (board1[i][j] != 0 && board1[i][j] != 6)
                cctv.push_back({ i,j });
            if (board1[i][j] == 0) 
                mn++;
        }
    }
    // 1 << (2*cctv.size())는 4의 cctv.size()승을 의미.
    // 4의 거듭제곱이여서 다음과 같은 수식이 가능하다.
    // 이 수식이 가능한 수는 2, 4, 8, 16 같은 단일 비트인 2의 거듭제곱 뿐
    // 홀수나 나머지 짝수는 해당 방식으로 표현 불가능하므로 for를 이용해서 나타낸다
    for (int tmp = 0; tmp < (1 << (2 * cctv.size())); tmp++) 
    { // tmp를 4진법으로 뒀을 때 각 자리수를 cctv의 방향으로 생각할 것이다.
        for (int i = 0; i < n; i++)
            for (int j = 0; j < m; j++)
                board2[i][j] = board1[i][j];
        int brute = tmp;
        for (int i = 0; i < cctv.size(); i++) 
        {
            int dir = brute % 4;
            brute /= 4;
            int x = cctv[i].X;
            int y = cctv[i].Y; // tie(x, y) = cctv[i];로 쓰면 1줄로 줄일 수 있음
            if (board1[x][y] == 1)  //1번 cctv type
            {
                upd(x, y, dir);
            }
            else if (board1[x][y] == 2) 
            {
                upd(x, y, dir);
                upd(x, y, dir + 2);
            }
            else if (board1[x][y] == 3)
            {
                upd(x, y, dir);
                upd(x, y, dir + 1);
            }
            else if (board1[x][y] == 4)
            {
                upd(x, y, dir);
                upd(x, y, dir + 1);
                upd(x, y, dir + 2);
            }
            else
            { // board1[x][y] == 5
                upd(x, y, dir);
                upd(x, y, dir + 1);
                upd(x, y, dir + 2);
                upd(x, y, dir + 3);
            }
        }
        int val = 0;
        for (int i = 0; i < n; i++)
            for (int j = 0; j < m; j++)
                val += (board2[i][j] == 0);
        mn = min(mn, val);
    }
    cout << mn;
}
{% endhighlight %}