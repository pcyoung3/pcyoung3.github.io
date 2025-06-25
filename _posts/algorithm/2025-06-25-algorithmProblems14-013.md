---
layout: single
title: 백준 14891번 - 톱니바퀴
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 14891번 문제](https://www.acmicpc.net/problem/14891) <br>
총 8개의 톱니를 가지고 있는 톱니바퀴 4개가 아래 그림과 같이 일렬로 놓여져 있다. 또, 톱니는 N극 또는 S극 중 하나를 나타내고 있다. 톱니바퀴에는 번호가 매겨져 있는데, 가장 왼쪽 톱니바퀴가 1번, 그 오른쪽은 2번, 그 오른쪽은 3번, 가장 오른쪽 톱니바퀴는 4번이다.

이때, 톱니바퀴를 총 K번 회전시키려고 한다. 톱니바퀴의 회전은 한 칸을 기준으로 한다. 회전은 시계 방향과 반시계 방향이 있고, 아래 그림과 같이 회전한다.

톱니바퀴를 회전시키려면, 회전시킬 톱니바퀴와 회전시킬 방향을 결정해야 한다. 톱니바퀴가 회전할 때, 서로 맞닿은 극에 따라서 옆에 있는 톱니바퀴를 회전시킬 수도 있고, 회전시키지 않을 수도 있다. 톱니바퀴 A를 회전할 때, 그 옆에 있는 톱니바퀴 B와 서로 맞닿은 톱니의 극이 다르다면, B는 A가 회전한 방향과 반대방향으로 회전하게 된다.

## 입력

첫째 줄에 1번 톱니바퀴의 상태, 둘째 줄에 2번 톱니바퀴의 상태, 셋째 줄에 3번 톱니바퀴의 상태, 넷째 줄에 4번 톱니바퀴의 상태가 주어진다. 상태는 8개의 정수로 이루어져 있고, 12시방향부터 시계방향 순서대로 주어진다. N극은 0, S극은 1로 나타나있다.

다섯째 줄에는 회전 횟수 K(1 ≤ K ≤ 100)가 주어진다. 다음 K개 줄에는 회전시킨 방법이 순서대로 주어진다. 각 방법은 두 개의 정수로 이루어져 있고, 첫 번째 정수는 회전시킨 톱니바퀴의 번호, 두 번째 정수는 방향이다. 방향이 1인 경우는 시계 방향이고, -1인 경우는 반시계 방향이다.

## 출력

총 K번 회전시킨 이후에 네 톱니바퀴의 점수의 합을 출력한다. 점수란 다음과 같이 계산한다.

- 1번 톱니바퀴의 12시방향이 N극이면 0점, S극이면 1점
- 2번 톱니바퀴의 12시방향이 N극이면 0점, S극이면 2점
- 3번 톱니바퀴의 12시방향이 N극이면 0점, S극이면 4점
- 4번 톱니바퀴의 12시방향이 N극이면 0점, S극이면 8점

## 예제 입력 1 복사

10101111
01111101
11001110
00000010
2
3 -1
1 1

## 예제 출력 1 복사

7

## 풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

enum class Rotator
{
    Right,  //시계방향
    Left,   //반시계방향
};

const int GearAmount = 8;
deque<int> Gear[4];
deque<int> beforeGear[4];
bool isTask[4];

void TaskGear(int gearIdx, int direction);
void RotateGear(int idx, Rotator dir);

int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    for (int i = 0; i < 4; i++)
    {
        string input;
        cin >> input;
        for(auto ch : input)
            Gear[i].push_back(ch - '0');
    }

    int k;
    vector<pair<int, int>> rotationWays;
    cin >> k;
    rotationWays.reserve(k);
    for (int i = 0; i < k; i++)
    {
        pair<int, int> input;
        cin >> input.first >> input.second;
        rotationWays.push_back(input);
    }

    for (auto cur : rotationWays)
    {
        //기어백업
        for (int i = 0; i < 4; i++)
            for (int j = 0; j < GearAmount; j++)
                beforeGear[i].push_back(Gear[i][j]);

        TaskGear(cur.first - 1, cur.second);

        for (int i = 0; i < 4; i++)
        {
            isTask[i] = false;
            beforeGear[i].clear();
        }
    }

    int result = 0;
    for (int i = 0; i < 4; i++)
        result += Gear[i][0] << i;//점수는 shift 연산자로 합산
    cout << result;
}

void TaskGear(int idx, int direction)
{
    Rotator dir;
    if (direction == 1)
        dir = Rotator::Right;
    else
        dir = Rotator::Left;

    if (idx < 0 || idx >= 4)    //인덱스 넘어가면 return
        return;
    if (isTask[idx])    //방문했던 gear면 return
        return;

    isTask[idx] = true; //방문표시

    //자기자신 기어 돌리기
    RotateGear(idx, dir);

    //왼쪽기어
    if (idx > 0 && beforeGear[idx][6] != beforeGear[idx - 1][2])
        TaskGear(idx - 1, direction * -1);
    //오른쪽기어
    if (idx < 3 && beforeGear[idx][2] != beforeGear[idx + 1][6])
        TaskGear(idx + 1, direction * -1);
}

//정해진 기어와 방향으로 기어를 돌리는 함수
void RotateGear(int idx, Rotator dir)
{
    if (dir == Rotator::Right)
    {
        int tmp = Gear[idx].back();
        Gear[idx].pop_back();
        Gear[idx].push_front(tmp);
    }
    else
    {
        int tmp = Gear[idx].front();
        Gear[idx].pop_front();
        Gear[idx].push_back(tmp);
    }
}
{% endhighlight %}

## 다른풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

string board[4];

// x : 번호, dir : 방향, 1번의 회전을 처리하는 함수
void go(int x, int dir) 
{
    int dirs[4] = {};
    dirs[x] = dir;

    // 왼쪽으로 회전을 전파
    int idx = x;
    while (idx > 0 && board[idx][6] != board[idx - 1][2]) 
    {
        dirs[idx - 1] = -dirs[idx];
        idx--;
    }

    // 오른쪽으로 회전을 전파
    idx = x;
    while (idx < 3 && board[idx][2] != board[idx + 1][6]) 
    {
        dirs[idx + 1] = -dirs[idx];
        idx++;
    }

    /*
    STL에 rotate 함수가 있어서 회전을 다소 편하게 처리할 수 있다.
    */
    for (int i = 0; i < 4; i++) 
    {
        if (dirs[i] == -1)
            rotate(board[i].begin(), board[i].begin() + 1, board[i].end());
        else if (dirs[i] == 1)
            rotate(board[i].begin(), board[i].begin() + 7, board[i].end());
    }
}

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    for (int i = 0; i < 4; i++) 
        cin >> board[i];

    int k;
    cin >> k;
    while (k--) 
    {
        int x, dir;
        cin >> x >> dir;
        go(x - 1, dir);
    }

    int ans = 0;
    for (int i = 0; i < 4; i++)
    {
        if (board[i][0] == '1') 
            ans += (1 << i);
    }
    cout << ans;
}
{% endhighlight %}