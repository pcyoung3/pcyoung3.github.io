---
layout: single
title: 백준 2573번 - 빙산
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 2573번 문제](https://www.acmicpc.net/problem/2573)

지구 온난화로 인하여 북극의 빙산이 녹고 있다. 빙산을 그림 1과 같이 2차원 배열에 표시한다고 하자. 빙산의 각 부분별 높이 정보는 배열의 각 칸에 양의 정수로 저장된다. 빙산 이외의 바다에 해당되는 칸에는 0이 저장된다. 그림 1에서 빈칸은 모두 0으로 채워져 있다고 생각한다.

|   |   |   |   |   |   |   |
|---|---|---|---|---|---|---|
||||||||
||2|4|5|3|||
||3||2|5|2||
||7|6|2|4|||
||||||||

그림 1. 행의 개수가 5이고 열의 개수가 7인 2차원 배열에 저장된 빙산의 높이 정보

빙산의 높이는 바닷물에 많이 접해있는 부분에서 더 빨리 줄어들기 때문에, 배열에서 빙산의 각 부분에 해당되는 칸에 있는 높이는 일년마다 그 칸에 동서남북 네 방향으로 붙어있는 0이 저장된 칸의 개수만큼 줄어든다. 단, 각 칸에 저장된 높이는 0보다 더 줄어들지 않는다. 바닷물은 호수처럼 빙산에 둘러싸여 있을 수도 있다. 따라서 그림 1의 빙산은 일년후에 그림 2와 같이 변형된다.

그림 3은 그림 1의 빙산이 2년 후에 변한 모습을 보여준다. 2차원 배열에서 동서남북 방향으로 붙어있는 칸들은 서로 연결되어 있다고 말한다. 따라서 그림 2의 빙산은 한 덩어리이지만, 그림 3의 빙산은 세 덩어리로 분리되어 있다.

|   |   |   |   |   |   |   |
|---|---|---|---|---|---|---|
||||||||
|||2|4|1|||
||1||1|5|||
||5|4|1|2|||
||||||||

그림 2

|   |   |   |   |   |   |   |
|---|---|---|---|---|---|---|
||||||||
||||3||||
|||||4|||
||3|2|||||
||||||||

그림 3

한 덩어리의 빙산이 주어질 때, 이 빙산이 두 덩어리 이상으로 분리되는 최초의 시간(년)을 구하는 프로그램을 작성하시오. 그림 1의 빙산에 대해서는 2가 답이다. 만일 전부 다 녹을 때까지 두 덩어리 이상으로 분리되지 않으면 프로그램은 0을 출력한다.

## 입력

첫 줄에는 이차원 배열의 행의 개수와 열의 개수를 나타내는 두 정수 N과 M이 한 개의 빈칸을 사이에 두고 주어진다. N과 M은 3 이상 300 이하이다. 그 다음 N개의 줄에는 각 줄마다 배열의 각 행을 나타내는 M개의 정수가 한 개의 빈 칸을 사이에 두고 주어진다. 각 칸에 들어가는 값은 0 이상 10 이하이다. 배열에서 빙산이 차지하는 칸의 개수, 즉, 1 이상의 정수가 들어가는 칸의 개수는 10,000 개 이하이다. 배열의 첫 번째 행과 열, 마지막 행과 열에는 항상 0으로 채워진다.

## 출력

첫 줄에 빙산이 분리되는 최초의 시간(년)을 출력한다. 만일 빙산이 다 녹을 때까지 분리되지 않으면 0을 출력한다.

## 예제 입력 1 복사
```
5 7
0 0 0 0 0 0 0
0 2 4 5 3 0 0
0 3 0 2 5 2 0
0 7 6 2 4 0 0
0 0 0 0 0 0 0
```

## 예제 출력 1 복사
```
2
```
   
---
## 풀이
```
5 7
0 0 0 0 0 0 0
0 2 4 0 3 0 0
0 3 0 0 5 2 0
0 7 6 0 4 0 0
0 0 0 0 0 0 0

5 7
0 0 0 0 0 0 0
0 1 1 0 0 0 0
0 1 0 0 0 0 0
0 1 1 0 0 0 0
0 0 0 0 0 0 0

7 9
0 0 0 0 0 0 0 0 0
0 9 5 5 5 5 5 9 0
0 5 9 5 5 5 9 5 0
0 5 5 9 1 9 5 5 0
0 5 9 5 5 5 9 5 0
0 9 5 5 5 5 5 9 0
0 0 0 0 0 0 0 0 0
```
   
### 내가 푼 코드
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

int board[305][305];
int before[305][305];
bool vis[305][305];

int dx[4] = { 1, -1, 0, 0 };
int dy[4] = { 0, 0, 1, -1 };
int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int n, m;
    cin >> n >> m;

	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < m; j++)
		{
			cin >> board[i][j];
			before[i][j] = board[i][j];
		}
	}
        

	int result = 0;
	while (1)
	{
		int chunk = 0;
		for (int i = 0; i < n; i++)
			fill(vis[i], vis[i] + 305, 0);

		bool isOver = false;
		//빙산 녹이기(1회차때 이미 나눠져있을 수 있으므로 1회차때는 돌리지않음)
		if (result != 0)
		{
			for (int i = 0; i < n; i++)
			{
				for (int j = 0; j < m; j++)
				{
					if (board[i][j] == 0)
						continue;

					int water = 0;
					for (int wd = 0; wd < 4; ++wd)	//주변 물의 개수 탐색
					{
						int wx = i + dx[wd];
						int wy = j + dy[wd];
						if (wx < 0 || wx >= n || wy < 0 || wy >= m)
							continue;
						if (before[wx][wy] == 0)
							water++;
					}
					board[i][j] = max(0, before[i][j] - water);
				}
			}
			//빙산 덮어써줌
			for (int i = 0; i < n; i++)
				for (int j = 0; j < m; j++)
					before[i][j] = board[i][j];
		}

		//덩어리에 대한 BFS
		for (int i = 0; i < n; i++)
		{
			for (int j = 0; j < m; j++)
			{
				if (board[i][j] == 0 || vis[i][j])
					continue;

				chunk++;
				if (chunk >= 2)
					break;
				queue<pair<int, int>> Q;
				Q.push({ i, j });
				vis[i][j] = 1;

				while (!Q.empty())
				{
					auto cur = Q.front();
					Q.pop();
					for (int dir = 0; dir < 4; ++dir)
					{
						int nx = cur.X + dx[dir];
						int ny = cur.Y + dy[dir];

						if (nx < 0 || nx >= n || ny < 0 || ny >= m)
							continue;
						if (vis[nx][ny] || board[nx][ny] == 0)
							continue;
						Q.push({ nx, ny });
						vis[nx][ny] = 1;
					}
				}
			}
			if (chunk >= 2)
				break;
		}
		if (chunk >= 2)
		{
			cout << result << '\n';
			return 0;
		}
		else if (chunk == 0)
		{
			cout << 0 << '\n';
			return 0;
		}
		result++;
	}//while
}
{% endhighlight %}
   
### 다른풀이(시간복잡도 참조)
{% highlight cpp linenos %}
// Authored by : std-freejia
// Co-authored by : BaaaaaaaaaaarkingDog
// http://boj.kr/f04bee3141834b83b6d1e0a6cd8c59b6
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

int n, m, year;
int area[303][303];
int vis[303][303];

int dx[] = {1, 0, -1, 0};
int dy[] = {0, 1, 0, -1};

bool check(int i, int j) {
  return (i >= 0 && i < n && j >= 0 && j < m);
}

void initvis(){
  for(int i = 0; i < n; i++) fill(vis[i], vis[i] + m, 0);
}

// 1년의 시간 흐름을 진행
void melting(){ 
  int zero[303][303] = {0};
  for(int i = 0; i < n; i++){
    for(int j = 0; j < m; j++){
      if(area[i][j] == 0) continue;
      for(int dir = 0; dir < 4; dir++){ // 주변의 0의 개수를 센다
        int nx = i + dx[dir];
        int ny = j + dy[dir];
        if(check(nx, ny) && area[nx][ny] == 0) zero[i][j]++;
      }
    }
  }
  for(int i = 0; i < n; i++){
    for(int j = 0; j < m; j++)
      area[i][j] = max(0, area[i][j] - zero[i][j]);    
  }
}

// 0 : 빙산이 다 녹음, 1 : 아직 한 덩이, 2 : 분리됨
int status(){
  int x = -1, y = -1;
  int cnt1 = 0; // 빙산의 개수
  // 빙산이 남아있는 아무 칸이나 선택
  for(int i = 0; i < n; i++){
    for(int j = 0; j < m; j++){
      if(area[i][j]){
        x = i;
        y = j;
        cnt1++;
      }
    }
  }
  if(cnt1 == 0) return 0; // 빙산이 남아있는 칸이 없음
  int cnt2 = 0; // (x, y)와 붙어있는 빙산의 수
  queue<pair<int,int> > q;
  vis[x][y] = 1; // 현재 위치 방문
  q.push({x, y});
  while(!q.empty()){
    auto cur = q.front(); q.pop();
    cnt2++;
    for(int i = 0; i < 4; i++){
      int nx = cur.X + dx[i];
      int ny = cur.Y + dy[i];
      if(!check(nx, ny) || vis[nx][ny] == 1 || area[nx][ny] <= 0) continue; // 정상 범위, 첫 방문, 이동가능 체크
      vis[nx][ny] = 1; // 방문표시
      q.push({nx, ny}); // 이동
    }
  }
  if(cnt1 == cnt2) return 1; // 전체 빙산의 수와 (x, y)와 붙어있는 빙산의 수가 일치하므로 아직 한 덩이
  return 2;
}

int main(void) {
  ios::sync_with_stdio(0);
  cin.tie(0);

  cin >> n >> m;
  for(int i = 0; i < n; i++){
    for(int j = 0; j < m; j++)
      cin >> area[i][j];
  }

  while(true){    
    year++; // 1년 추가
    melting(); // 빙산 녹이기
    initvis(); // 방문배열 초기화
    int check = status(); // 빙산의 상태 확인
    if(check == 0){
      cout << 0;
      return 0;
    }
    else if(check == 1) continue; // 아직 한 덩이
    else break; // check = 2, 분리됨
  }
  cout << year;
  return 0;
}

/*
이 코드는 O(NM * year)에 동작. 최악의 데이터를 넣어도 year가 대략 500 이하여서 시간 초과가 발생하지 않지만
구현을 하기 전에 이 코드의 시간복잡도가 얼마일지, year가 어떤 경우에 가장 클지, 그 경우 year는 얼마일지
고민해볼 필요가 있다.
*/
{% endhighlight %}
