---
layout: single
title: 백준 9466번 - 텀 프로젝트
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 9466번 문제](https://www.acmicpc.net/problem/9466)

이번 가을학기에 '문제 해결' 강의를 신청한 학생들은 텀 프로젝트를 수행해야 한다. 프로젝트 팀원 수에는 제한이 없다. 심지어 모든 학생들이 동일한 팀의 팀원인 경우와 같이 한 팀만 있을 수도 있다. 프로젝트 팀을 구성하기 위해, 모든 학생들은 프로젝트를 함께하고 싶은 학생을 선택해야 한다. (단, 단 한 명만 선택할 수 있다.) 혼자 하고 싶어하는 학생은 자기 자신을 선택하는 것도 가능하다.

학생들이(s1, s2, ..., sr)이라 할 때, r=1이고 s1이 s1을 선택하는 경우나, s1이 s2를 선택하고, s2가 s3를 선택하고,..., sr-1이 sr을 선택하고, sr이 s1을 선택하는 경우에만 한 팀이 될 수 있다.

예를 들어, 한 반에 7명의 학생이 있다고 하자. 학생들을 1번부터 7번으로 표현할 때, 선택의 결과는 다음과 같다.

| 1   | 2   | 3   | 4   | 5   | 6   | 7   |
| --- | --- | --- | --- | --- | --- | --- |
| 3   | 1   | 3   | 7   | 3   | 4   | 6   |

위의 결과를 통해 (3)과 (4, 7, 6)이 팀을 이룰 수 있다. 1, 2, 5는 어느 팀에도 속하지 않는다.

주어진 선택의 결과를 보고 어느 프로젝트 팀에도 속하지 않는 학생들의 수를 계산하는 프로그램을 작성하라.

## 입력

첫째 줄에 테스트 케이스의 개수 T가 주어진다. 각 테스트 케이스의 첫 줄에는 학생의 수가 정수 n (2 ≤ n ≤ 100,000)으로 주어진다. 각 테스트 케이스의 둘째 줄에는 선택된 학생들의 번호가 주어진다. (모든 학생들은 1부터 n까지 번호가 부여된다.)

## 출력

각 테스트 케이스마다 한 줄에 출력하고, 각 줄에는 프로젝트 팀에 속하지 못한 학생들의 수를 나타내면 된다.

## 예제 입력 1 복사

```
2
7
3 1 3 7 3 4 6
8
1 2 3 4 5 6 7 8
```

## 예제 출력 1 복사
```
3
0
```
   
---
## 풀이

<div class="notice--warning" markdown="1">
**<u>사이클 문제이며 사이클에 방문표기를 해야하기 때문에 BFS도 섞인 문제이다</u>** 
</div>
   
### 해설
사이클의 특성을 이해하고 문제에 접근해야 한다.
1. n회차를 진행하다가 이번 회차에 처음 방문해서 표시한 곳이 또 나오게되면 사이클이다
2. n회차를 진행할 때 사이클을 만나면 지금까지 순회한 부분은 사이클이 아니다
3. n회차를 진행할 때 이번 회차에 표기하지 않은 부분이 나온다면 사이클이 아니다

|                    방문표기 남기기                    |                   사이클 표시 남기기                   |                     다음번 진행                     |
| :--------------------------------------------: | :--------------------------------------------: | :--------------------------------------------: |
| ![KakaoTalk_20230604_154305648.jpg](/assets/images/algorithm/KakaoTalk_20230604_154305648.jpg) | ![KakaoTalk_20230604_154315816.jpg](/assets/images/algorithm/KakaoTalk_20230604_154315816.jpg) | ![KakaoTalk_20230604_154325436.jpg](/assets/images/algorithm/KakaoTalk_20230604_154325436.jpg) |
| 1번째 방문하는 도중 <br>현재 회차와 동일한 노드를 만남 |              해당 노드는 사이클이므로 사이클 표시              |  사이클은 만나거나(2) <br>사이클이 아닌 부분(3)을 만나면 사이클이 아님  |

   
### 코드
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

int arr[100005];
int state[100005];
int n;

int const NOT_VISITED = 0;
int const CYCLE_IN = -1;

void run(int x);
int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int t;
    cin >> t;
    while (t--)
    {
        cin >> n;
        //학생 번호가 0번부터가 아니라 1번부터
        for (int i = 1; i <= n; ++i)
            cin >> arr[i];
        fill(state + 1, state + n + 1, NOT_VISITED);

        for (int i = 1; i <= n; ++i)
        {
            //방문하지 않은 것에만 BFS를 돌려서 최대 방문회수는 2n이므로 시간복잡도는 O(n)
            if (state[i] == NOT_VISITED)
                run(i);
        }

        int ans = 0;
        for (int i = 1; i <= n; ++i)
        {
            //사이클이 아닌 노드의 개수
            if (state[i] != CYCLE_IN)
                ans++;
        }
        cout << ans << '\n';
    }
}

void run(int x)
{
    int cur = x;

    while (1)
    {
        state[cur] = x; //이번 회차 방문기록 남기기
        cur = arr[cur]; //진행

        //방문기록이 처음에 들어온 인덱스와 같음
        //이번 방문 때 2번 방문한 경우
        if (state[cur] == x)
        {
            //한 사이클 돌면서 사이클임을 표기
            //어짜피 사이클이기 때문에 CYCLE_IN으로 표기해주면서 돌면 전부 마킹 가능
            while (state[cur] != CYCLE_IN)
            {
                state[cur] = CYCLE_IN;
                cur = arr[cur];
            }
            return;
        }
        //이미 전회차에 방문기록이 있음 -> 사이클이 아님
        else if (state[cur] != NOT_VISITED)
            return;
    }
}
{% endhighlight %}
