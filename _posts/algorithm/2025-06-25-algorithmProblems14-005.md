---
layout: single
title: 백준 14499번 - 주사위 굴리기
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 14499번 문제](https://www.acmicpc.net/problem/14499) <br>

크기가 N×M인 지도가 존재한다. 지도의 오른쪽은 동쪽, 위쪽은 북쪽이다. 이 지도의 위에 주사위가 하나 놓여져 있으며, 주사위의 전개도는 아래와 같다. 지도의 좌표는 (r, c)로 나타내며, r는 북쪽으로부터 떨어진 칸의 개수, c는 서쪽으로부터 떨어진 칸의 개수이다. 

```
  2
4 1 3
  5
  6
```

주사위는 지도 위에 윗 면이 1이고, 동쪽을 바라보는 방향이 3인 상태로 놓여져 있으며, 놓여져 있는 곳의 좌표는 (x, y) 이다. 가장 처음에 주사위에는 모든 면에 0이 적혀져 있다.

지도의 각 칸에는 정수가 하나씩 쓰여져 있다. 주사위를 굴렸을 때, 이동한 칸에 쓰여 있는 수가 0이면, 주사위의 바닥면에 쓰여 있는 수가 칸에 복사된다. 0이 아닌 경우에는 칸에 쓰여 있는 수가 주사위의 바닥면으로 복사되며, 칸에 쓰여 있는 수는 0이 된다.

주사위를 놓은 곳의 좌표와 이동시키는 명령이 주어졌을 때, 주사위가 이동했을 때 마다 상단에 쓰여 있는 값을 구하는 프로그램을 작성하시오.

주사위는 지도의 바깥으로 이동시킬 수 없다. 만약 바깥으로 이동시키려고 하는 경우에는 해당 명령을 무시해야 하며, 출력도 하면 안 된다.

## 입력

첫째 줄에 지도의 세로 크기 N, 가로 크기 M (1 ≤ N, M ≤ 20), 주사위를 놓은 곳의 좌표 x, y(0 ≤ x ≤ N-1, 0 ≤ y ≤ M-1), 그리고 명령의 개수 K (1 ≤ K ≤ 1,000)가 주어진다.

둘째 줄부터 N개의 줄에 지도에 쓰여 있는 수가 북쪽부터 남쪽으로, 각 줄은 서쪽부터 동쪽 순서대로 주어진다. 주사위를 놓은 칸에 쓰여 있는 수는 항상 0이다. 지도의 각 칸에 쓰여 있는 수는 10 미만의 자연수 또는 0이다.

마지막 줄에는 이동하는 명령이 순서대로 주어진다. 동쪽은 1, 서쪽은 2, 북쪽은 3, 남쪽은 4로 주어진다.

## 출력

이동할 때마다 주사위의 윗 면에 쓰여 있는 수를 출력한다. 만약 바깥으로 이동시키려고 하는 경우에는 해당 명령을 무시해야 하며, 출력도 하면 안 된다.

## 예제 입력 1 복사

```
4 2 0 0 8
0 2
3 4
5 6
7 8
4 4 4 1 3 3 3 2
```

## 예제 출력 1 복사

```
0
0
3
0
0
8
6
3
```

## 풀이
![KakaoTalk_20230804_085011926.jpg](/assets/images/algorithm/KakaoTalk_20230804_085011926.jpg)

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n, m;
int r, c;
int board[25][25];
queue<int> ways;
int dice[6];

void RollingDice();
void RotateEast();
void RotateWest();
void RotateNorth();
void RotateSouth();

//Debug
void PrintRightLeft();
void PrintUpDown();

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int k;
    cin >> n >> m >> r >> c >> k;
    for (int i = 0; i < n; i++)
        for (int j = 0; j < m; j++)
            cin >> board[i][j];
    for (int i = 0; i < k; i++)
    {
        int input;
        cin >> input;
        ways.push(input);
    }

    for (int i = 0; i < 6; i++)
    {
        dice[i] = 0;
    }

    RollingDice();

}

void RollingDice()
{
    do
    {
        int cur = ways.front(); //들어온 방향 명령 가져옴
        ways.pop();

        switch (cur)
        {
        case 1:
            if (c + 1 >= m) //Out of Bounce 검사
                continue;
            c++;    //동쪽으로 한칸 이동
            RotateEast();
            break;
        case 2:
            if (c - 1 < 0) //Out of Bounce 검사
                continue;
            c--;    //서쪽으로 한칸 이동
            RotateWest();
            break;
        case 3:
            if (r - 1 < 0) //Out of Bounce 검사
                continue;
            r--;    //남쪽으로 한칸 이동
            RotateNorth();
            break;
        case 4:
            if (r + 1 >= n) //Out of Bounce 검사
                continue;
            r++;    //북쪽으로 한칸 이동
            RotateSouth();
            break;
        default:    //잘못된 명령
            cout << "ERROR : wrong Ways!!\n";
            return;
        }

        if (board[r][c] == 0)   //맵의 해당칸이 0일 경우
            board[r][c] = dice[5];  //주사위의 값을 맵으로 복사
        else //맵의 해당칸이 0이 아닐 경우
        {
            dice[5] = board[r][c];  //맵의 값을 주사위로 복사
            board[r][c] = 0;    //맵의 값은 0으로 바뀜
        }

        cout << dice[0] << "\n";    //해당 회차의 주사위 위면의 숫자 출력
    } while (!ways.empty());
}

void RotateEast()   //동쪽으로 이동 
{
    vector<int> rotateArr;
    rotateArr.reserve(4);

    rotateArr.push_back(dice[5]); //6
    rotateArr.push_back(dice[3]); //4
    rotateArr.push_back(dice[0]); //1
    rotateArr.push_back(dice[2]); //3
    
    //STL::Rotate 함수 사용
    //오른쪽으로 회전
    rotate(rotateArr.begin(), rotateArr.end() - 1, rotateArr.end());

    dice[5] = rotateArr[0];
    dice[3] = rotateArr[1];
    dice[0] = rotateArr[2];
    dice[2] = rotateArr[3];
}

void RotateWest()   //서쪽으로 이동
{
    vector<int> rotateArr;
    rotateArr.reserve(4);

    rotateArr.push_back(dice[5]); //6
    rotateArr.push_back(dice[3]); //4
    rotateArr.push_back(dice[0]); //1
    rotateArr.push_back(dice[2]); //3

    //왼쪽으로 회전
    rotate(rotateArr.begin(), rotateArr.begin() + 1, rotateArr.end());

    dice[5] = rotateArr[0];
    dice[3] = rotateArr[1];
    dice[0] = rotateArr[2];
    dice[2] = rotateArr[3];
}

void RotateNorth()  //북쪽으로 이동
{
    vector<int> rotateArr;
    rotateArr.reserve(4);

    rotateArr.push_back(dice[1]); //2
    rotateArr.push_back(dice[0]); //1
    rotateArr.push_back(dice[4]); //5
    rotateArr.push_back(dice[5]); //6

    //왼쪽(위쪽)으로 회전
    rotate(rotateArr.begin(), rotateArr.begin() + 1, rotateArr.end());

    dice[1] = rotateArr[0];
    dice[0] = rotateArr[1];
    dice[4] = rotateArr[2];
    dice[5] = rotateArr[3];
}

void RotateSouth()  //남쪽으로 이동
{
    vector<int> rotateArr;
    rotateArr.reserve(4);

    rotateArr.push_back(dice[1]); //2
    rotateArr.push_back(dice[0]); //1
    rotateArr.push_back(dice[4]); //5
    rotateArr.push_back(dice[5]); //6

    //오른쪽(아래쪽)으로 회전
    rotate(rotateArr.begin(), rotateArr.end() - 1, rotateArr.end());

    dice[1] = rotateArr[0];
    dice[0] = rotateArr[1];
    dice[4] = rotateArr[2];
    dice[5] = rotateArr[3];
}
{% endhighlight %}

## 다른풀이
![KakaoTalk_20230804_090901652.jpg](/assets/images/algorithm/KakaoTalk_20230804_090901652.jpg)

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int N, M, x, y, K, command;
int board[21][21];  // 지도
int dice[7];        // 주사위
//  윗면이 2이고, 동쪽을 바라보는 방향이 6
//     [1]
//  [5][2][6]
//     [3]
//     [4]

int idx[5][4] = {
  {},        // dummy
  {2,6,4,5}, // 동쪽, 5->2, 2->6, 6->4, 4->5
  {2,5,4,6}, // 서쪽, 6->2, 2->5, 5->4, 4->6
  {3,2,1,4}, // 북쪽, 4->3, 3->2, 2->1, 1->4
  {2,3,4,1}, // 남쪽, 1->2, 2->3, 3->4, 4->1
};

// 명령어가 유효한지 검증하는 함수
bool isOk(int nx, int ny){
  if(nx<0||nx>=N||ny<0||ny>=M) return false;
  return true;
}

// 주사위를 굴리는 함수
void roll(int com){
  // 회전 시 기존 주사위의 값을 별도로 보존하기 위한 배열 생성
  int tmp[7];
  for(int i = 1; i <= 6; ++i) tmp[i]=dice[i];

  // 굴리는거 처리
  for(int i = 0; i < 4; i++)
    tmp[idx[com][i]] = dice[idx[com][(i+1)%4]];

  // 회전 결괏값을 기존 주사위에 대입
  for(int i = 1; i <= 6; ++i) dice[i]=tmp[i];
}

void score(int c){
  int nx=x, ny=y;
  // 주사위 위치 이동
  if(c==1) ++ny;
  else if(c==2) --ny;
  else if(c==3) --nx;
  else ++nx;

  if(isOk(nx, ny)){
    x=nx; y=ny; // 유효성 확인 후 주사위의 위치 대입
    roll(c);
    // 0일 경우
    if(board[nx][ny]==0)
      board[nx][ny]=dice[4]; // 칸에 바닥면 값 대입
    
    // 0이 아닐 경우
    else {
      dice[4]=board[nx][ny]; // 바닥면에 칸 값 대입
      board[nx][ny]=0; // 칸 값을 0으로 초기화
    }
    cout << dice[2] << '\n'; // 주사위 윗면 출력
  }
}

int main(void){
  ios::sync_with_stdio(0);
  cin.tie(0);
  cin >> N >> M >> x >> y >> K;
  for(int i = 0; i < N; ++i)
    for(int j = 0; j < M; ++j)
      cin >> board[i][j];

  while(K--){
    cin >> command;
    score(command);
  }
}
{% endhighlight %}