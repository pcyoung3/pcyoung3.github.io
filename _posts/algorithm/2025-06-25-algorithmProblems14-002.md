---
layout: single
title: 백준 12100번 - 2048(Easy)
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 12100번 문제](https://www.acmicpc.net/problem/12100) <br>
2048 게임은 4×4 크기의 보드에서 혼자 즐기는 재미있는 게임이다. 이 [링크](https://gabrielecirulli.github.io/2048/)를 누르면 게임을 해볼 수 있다.

이 게임에서 한 번의 이동은 보드 위에 있는 전체 블록을 상하좌우 네 방향 중 하나로 이동시키는 것이다. 이때, 같은 값을 갖는 두 블록이 충돌하면 두 블록은 하나로 합쳐지게 된다. 한 번의 이동에서 이미 합쳐진 블록은 또 다른 블록과 다시 합쳐질 수 없다. (실제 게임에서는 이동을 한 번 할 때마다 블록이 추가되지만, 이 문제에서 블록이 추가되는 경우는 없다)

|                    |          |                    |
| ------------------ | -------- | ------------------ |
| ![12100_p_1.png](/assets/images/algorithm/12100_p_1.png) | ![12100_p_2.png](/assets/images/algorithm/12100_p_2.png)         | ![12100_p_3.png](/assets/images/algorithm/12100_p_3.png) |
| <그림 1>           | <그림 2> | <그림 3>           |

<그림 1>의 경우에서 위로 블록을 이동시키면 <그림 2>의 상태가 된다. 여기서, 왼쪽으로 블록을 이동시키면 <그림 3>의 상태가 된다.

|   |   |   |   |
|---|---|---|---|
|![12100_p_4.png](/assets/images/algorithm/12100_p_4.png)|![12100_p_5.png](/assets/images/algorithm/12100_p_5.png)|![12100_p_6.png](/assets/images/algorithm/12100_p_6.png)|![12100_p_7.png](/assets/images/algorithm/12100_p_7.png)|
|<그림 4>|<그림 5>|<그림 6>|<그림 7>|

<그림 4>의 상태에서 블록을 오른쪽으로 이동시키면 <그림 5>가 되고, 여기서 다시 위로 블록을 이동시키면 <그림 6>이 된다. 여기서 오른쪽으로 블록을 이동시켜 <그림 7>을 만들 수 있다.

|   |   |
|---|---|
|![12100_p_8.png](/assets/images/algorithm/12100_p_8.png)|![12100_p_9.png](/assets/images/algorithm/12100_p_9.png)|
|<그림 8>|<그림 9>|

<그림 8>의 상태에서 왼쪽으로 블록을 옮기면 어떻게 될까? 2가 충돌하기 때문에, 4로 합쳐지게 되고 <그림 9>의 상태가 된다.

|   |   |   |   |
|---|---|---|---|
|![12100_p_10.png](/assets/images/algorithm/12100_p_10.png)|![12100_p_11.png](/assets/images/algorithm/12100_p_11.png)|![12100_p_12.png](/assets/images/algorithm/12100_p_12.png)|![12100_p_13.png](/assets/images/algorithm/12100_p_13.png)|
|<그림 10>|<그림 11>|<그림 12>|<그림 13>|

<그림 10>에서 위로 블록을 이동시키면 <그림 11>의 상태가 된다. 

<그림 12>의 경우에 위로 블록을 이동시키면 <그림 13>의 상태가 되는데, 그 이유는 한 번의 이동에서 이미 합쳐진 블록은 또 합쳐질 수 없기 때문이다.

|   |   |
|---|---|
|![12100_p_14.png](/assets/images/algorithm/12100_p_14.png)|![12100_p_15.png](/assets/images/algorithm/12100_p_15.png)|
|<그림 14>|<그림 15>|

마지막으로, 똑같은 수가 세 개가 있는 경우에는 이동하려고 하는 쪽의 칸이 먼저 합쳐진다. 예를 들어, 위로 이동시키는 경우에는 위쪽에 있는 블록이 먼저 합쳐지게 된다. <그림 14>의 경우에 위로 이동하면 <그림 15>를 만든다.

이 문제에서 다루는 2048 게임은 보드의 크기가 N×N 이다. 보드의 크기와 보드판의 블록 상태가 주어졌을 때, 최대 5번 이동해서 만들 수 있는 가장 큰 블록의 값을 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 보드의 크기 N (1 ≤ N ≤ 20)이 주어진다. 둘째 줄부터 N개의 줄에는 게임판의 초기 상태가 주어진다. 0은 빈 칸을 나타내며, 이외의 값은 모두 블록을 나타낸다. 블록에 쓰여 있는 수는 2보다 크거나 같고, 1024보다 작거나 같은 2의 제곱꼴이다. 블록은 적어도 하나 주어진다.

## 출력

최대 5번 이동시켜서 얻을 수 있는 가장 큰 블록을 출력한다.

## 예제 입력 1 복사
```
3
2 2 2
4 4 4
8 8 8
```

## 예제 출력 1 복사

16

## 풀이

<div class="notice--info" markdown="1">
**<u>문제에서 구현해야 할 부분들</u>** <br>
1. 판을 기울여서 값을 합치는 부분 <br>
2. 해당 판을 상하좌우로 기울일 수 있게 하는 부분 <br>
3. 상하좌우로 기울이는 행동을 5번 실행하는 부분
</div>

<div class="notice--info" markdown="1">
**<u>상하좌우로 기울이는 행동과 한쪽으로만 기울이고 판을 회전하는 것이 같은걸 염두하면 쉽게 접근 가능</u>** <br>
[시뮬레이션_백준18808 스티커 붙이기](/algorithmproblems/algorithmProblems14-019/#다른풀이) 해당 문제에서 사용했던 기법인 행렬 회전 기법을 상하좌우 함수를 만드는 대신 사용하면 기울이는 함수 하나로 모든 상황을 대처 가능
</div>

<div class="notice--info" markdown="1">
**<u>기울이는 행동을 5번하는 부분?</u>** <br>
[시뮬레이션_백준15683 감시](/algorithmproblems/algorithmProblems14-010/) 해당 문제에서 사용한 진법 기법을 사용하면 됨
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int board[25][25];
int Temp[25][25];
int Temp2[25][25];
int n;

void Rotate();
void Tilted();
void Print();

int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

	cin >> n;
	for (int i = 0; i < n; i++)
		for (int j = 0; j < n; j++)
			cin >> board[i][j];

    int result = 0;
    int count = 1;
    
    for (int i = 0; i < n; i++) //Temp에 초기화 배열 대입
    {
        for (int j = 0; j < n; j++)
        {
            Temp[i][j] = board[i][j];
            Temp2[i][j] = Temp[i][j];
        }
    }

	for (int i = 0; i < 5; i++) //전체 경우의 수 계산 4^5승(4방향으로 5번)
	    count *= 4;

    for (int i = 0; i < count; i++) //진법을 이용한 4방향을 5번 하는 모든 경우의 수 순회
    {
        int brute = i;
        for (int j = 0; j < 5; j++) //5번 기울이기
        {
            int dir = brute % 4;
            brute /= 4;
            while (dir--)
                Rotate();
            Tilted();
        }

        for (int i = 0; i < n; i++) //원복하고 다음 경우의 수 탐색
        {
            for (int j = 0; j < n; j++)
            {
                result = max(result, Temp[i][j]);
                Temp[i][j] = board[i][j];
                Temp2[i][j] = Temp[i][j];
            }
        }
    }

    cout << result;
}

//배열을 90도 시계방향으로 회전
void Rotate()
{
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < n; j++)
            Temp2[i][j] = Temp[n - 1 - j][i];
    }

    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            Temp[i][j] = Temp2[i][j];
}

void Tilted()
{
    for (int i = 0; i < n; i++)
    {
        int index = 0;
        for (int j = 0; j < n; j++)
        {
            if (index == j)
                continue;
            if (Temp[i][j] == 0)
                continue;
            if (Temp2[i][index] != 0) //현재 index가 0이 아닌 경우
            {
                if (Temp2[i][index] == Temp[i][j]) //합쳐지는 경우
                {
                    Temp2[i][index++] += Temp[i][j];
                    Temp2[i][j] = 0;
                }
                else //합쳐지지 않는 경우
                {
                    Temp2[i][++index] = Temp[i][j];
                    if(j != index)  //합쳐지지 않았지만 앞에 빈칸이 있어서 옮기는 경우
                        Temp2[i][j] = 0;
                }
            }
            else //Temp2[i][index] == 0 아무것도 없어서 그냥 옮기는 경우
            {
                Temp2[i][index] = Temp[i][j];
                Temp2[i][j] = 0;
            }
        }
    }

    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            Temp[i][j] = Temp2[i][j];
}

//Debug용
void Print()
{
    cout << "\n\n";
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < n; j++)
            cout << Temp[i][j] << " ";
        cout << "\n";
    }
}
{% endhighlight %}

## 다른풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int board1[21][21];
int board2[21][21];
int n;

void Rotate();
void Tilt(int dir);
void Print();

int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    cin >> n;
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            cin >> board1[i][j];

    int result = 0;
    int count = 1;

    for (int i = 0; i < 5; i++) //전체 경우의 수 계산 4^5승(4방향으로 5번)
        count *= 4;

    for (int i = 0; i < n; i++) //board1은 입력값을 유지하고 board2로 값 변경
        for (int j = 0; j < n; j++)
            board2[i][j] = board1[i][j];

    for (int i = 0; i < count; i++) //진법을 이용한 4방향을 5번 하는 모든 경우의 수 순회
    {
        int brute = i;
        for (int j = 0; j < 5; j++)
        {
            int dir = brute % 4;
            brute /= 4;
            Tilt(dir);
        }

        for (int i = 0; i < n; i++) //원복하고 결과값을 저장
        {
            for (int j = 0; j < n; j++)
            {
                result = max(result, board2[i][j]);
                board2[i][j] = board1[i][j];
            }
        }
    }

    cout << result;
}

//배열을 90도 시계방향으로 회전
void Rotate()
{
    int temp[21][21] = {};
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < n; j++)
            temp[i][j] = board2[n - 1 - j][i];
    }

    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            board2[i][j] = temp[i][j];
}

//dir의 회전만큼 돌린 후 기울이는 함수
//회전 수는 여기서 상,하,좌,우로 기울이는 것과 동일
void Tilt(int dir)
{
    while (dir--)
        Rotate();
    for (int i = 0; i < n; i++)
    {
        int tilted[21] = {};
        int index = 0;
        for (int j = 0; j < n; j++)
        {
            if (board2[i][j] == 0)  //현재 옮기려는 수가 0이면 할 필요 없음
                continue;
            if (tilted[index] == 0) //옮기려는 index가 0인 경우 그냥 옮기면 됨
                tilted[index] = board2[i][j];
            else if (tilted[index] == board2[i][j]) //만약 목적지의 수와 같다면 합침
                tilted[index++] *= 2;
            else //목적지의 수와 같지 않으면 index를 증가시키고 옮김
                tilted[++index] = board2[i][j];
        }
        for (int j = 0; j < n; j++) //기울인 행을 덮어씀
            board2[i][j] = tilted[j];
    }
}

//Debug용
void Print()
{
    cout << "\n\n";
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < n; j++)
            cout << board2[i][j] << " ";
        cout << "\n";
    }
}
{% endhighlight %}