---
layout: single
title: 백준 18808번 - 스티커 붙이기
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 18808번 문제](https://www.acmicpc.net/problem/18808) <br>
혜윤이는 최근에 다양한 대회를 참여하면서 노트북에 붙일 수 있는 스티커들을 많이 받았다. 스티커는 아래와 같이 사각 모눈종이 위에 인쇄되어 있으며, 스티커의 각 칸은 상하좌우로 모두 연결되어 있다.

혜윤이는 자신의 노트북에 이 스티커들을 붙이기로 했다. 혜윤이의 노트북은 마침 직사각형 모양이고, 스티커가 인쇄된 모눈종이와 같은 간격으로 격자가 그려져 있다. 혜윤이는 스티커들을 먼저 받았던 것부터 차례대로 격자에 맞춰서 붙이려고 한다.

혜윤이가 스티커를 붙이는 방법은 다음과 같다.

1. 스티커를 회전시키지 않고 모눈종이에서 떼어낸다.
2. 다른 스티커와 겹치거나 노트북을 벗어나지 않으면서 스티커를 붙일 수 있는 위치를 찾는다. 혜윤이는 노트북의 위쪽부터 스티커를 채워 나가려고 해서, 스티커를 붙일 수 있는 위치가 여러 곳 있다면 가장 위쪽의 위치를 선택한다. 가장 위쪽에 해당하는 위치도 여러 곳이 있다면 그중에서 가장 왼쪽의 위치를 선택한다.
3. 선택한 위치에 스티커를 붙인다. 만약 스티커를 붙일 수 있는 위치가 전혀 없어서 스티커를 붙이지 못했다면, 스티커를 시계 방향으로 90도 회전한 뒤 2번 과정을 반복한다.
4. 위의 과정을 네 번 반복해서 스티커를 0도, 90도, 180도, 270도 회전시켜 봤음에도 스티커를 붙이지 못했다면 해당 스티커를 붙이지 않고 버린다.

## 입력

첫째 줄에 노트북의 세로와 가로 길이를 나타내는 N(1 ≤ N ≤ 40)과 M(1 ≤ M ≤ 40), 그리고 스티커의 개수 K(1 ≤ K ≤ 100)이 한 칸의 빈칸을 사이에 두고 주어진다.

그 다음 줄부터는 K개의 스티커들에 대한 정보가 주어진다.

## 출력

첫째 줄에 주어진 스티커들을 차례대로 붙였을 때 노트북에서 스티커가 붙은 칸의 수를 출력한다.

## 예제 입력 1 복사

```
5 4 4
3 3
1 0 1
1 1 1
1 0 1
2 5
1 1 1 1 1
0 0 0 1 0
2 3
1 1 1
1 0 1
3 3
1 0 0
1 1 1
1 0 0
```

## 예제 출력 1 복사

18

## 풀이

<div class="notice--warning" markdown="1">
**<u>2차원 배열의 90도 회전을 알고 있으면 풀 수 있다</u>** <br>
![18808_ans_1.png](/assets/images/algorithm/18808_ans_1.png) <br>
![KakaoTalk_20230728_092129764.jpg](/assets/images/algorithm/KakaoTalk_20230728_092129764.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int board[40][40];      //전체 판크기
int sticker[10][10];    //스티커 판
int n, m, k;

bool IsAttachEnable(int startRow, int startCol, int stickerRow, int stickerCol);
void AttachSticker(int startRow, int startCol, int stickerRow, int stickerCol);
void RotateSticker(int& outRow, int& outCol);

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    cin >> n >> m >> k;

    while (k--)
    {
        int r, c;
        cin >> r >> c;

        //이번회차 스티커 입력
        for (int i = 0; i < r; i++)
            for (int j = 0; j < c; j++)
                cin >> sticker[i][j];

        //모든 칸에 스티커를 붙여보고 안되면 90도 회전시킴
        bool bAttach = false;
        for (int rot = 0; rot < 4; rot++)
        {
            for (int i = 0; i < n; i++)
            {
                for (int j = 0; j < m; j++)
                {
                    bAttach = IsAttachEnable(i, j, r, c); //붙일 수 있는가?
                    if (bAttach)
                    {
                        AttachSticker(i, j, r, c); //스티커 붙임
                        break;
                    }
                }
                if (bAttach)
                    break;
            }
            if (bAttach)
                break;

            RotateSticker(r, c); //스티커 회전
        }
    }

    //스티커가 붙은 칸 세고 출력
    int result = 0;
    for (int i = 0; i < n; i++)
        for (int j = 0; j < m; j++)
            if (board[i][j] == 1) result++;
    cout << result;
}

bool IsAttachEnable(int startRow, int startCol, int stickerRow, int stickerCol)
{
    for (int i = startRow; i < startRow + stickerRow; i++)
    {
        for (int j = startCol; j < startCol + stickerCol; j++)
        {
            if (i >= n || j >= m) //Out of Bounds
                return false;
            if (board[i][j] == 1 && sticker[i - startRow][j - startCol] == 1)
                return false; //보드에 스티커가 이미 붙어있는지 + 스티커 배열에서 스티커가 있는 칸인지
        }
    }
    return true;
}

void AttachSticker(int startRow, int startCol, int stickerRow, int stickerCol)
{
    for (int i = 0; i < stickerRow; i++)
    {
        for (int j = 0; j < stickerCol; j++)
        {
            if(sticker[i][j] == 1) //스티커배열에서 스티커가 있는 칸만 옮김
                board[startRow + i][startCol + j] = sticker[i][j];
        }
    }
}

void RotateSticker(int& outRow, int& outCol)
{
    int row = outCol;
    int col = outRow;
    int stickerTemp[10][10];//스티커 임시 판

    //임시배열에 90도 돌린배열 넣음
    for (int i = 0; i < row; i++)
    {
        for (int j = 0; j < col; j++)
        {
            //2차원 배열 90도 회전공식
            stickerTemp[i][j] = sticker[outRow - 1 - j][i];
        }
    }

    //돌린 배열 덮어씀
    for (int i = 0; i < row; i++)
    {
        for (int j = 0; j < col; j++)
        {
            sticker[i][j] = stickerTemp[i][j];
        }
    }
    swap(outRow, outCol); //가로 세로 스왑
}
{% endhighlight %}