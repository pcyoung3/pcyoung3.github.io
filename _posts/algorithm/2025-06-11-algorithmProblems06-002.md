---
layout: single
title: 백준 1874번 - 스택수열
categories: algorithmProblems
tags: [algorithm, stack]

toc: true
toc_sticky: true
---

## 문제
[백준 1874번 문제](https://www.acmicpc.net/problem/1874)

스택 (stack)은 기본적인 자료구조 중 하나로, 컴퓨터 프로그램을 작성할 때 자주 이용되는 개념이다. 스택은 자료를 넣는 (push) 입구와 자료를 뽑는 (pop) 입구가 같아 제일 나중에 들어간 자료가 제일 먼저 나오는 (LIFO, Last in First out) 특성을 가지고 있다.

1부터 n까지의 수를 스택에 넣었다가 뽑아 늘어놓음으로써, 하나의 수열을 만들 수 있다. 이때, 스택에 push하는 순서는 반드시 오름차순을 지키도록 한다고 하자. 임의의 수열이 주어졌을 때 스택을 이용해 그 수열을 만들 수 있는지 없는지, 있다면 어떤 순서로 push와 pop 연산을 수행해야 하는지를 알아낼 수 있다. 이를 계산하는 프로그램을 작성하라.

## 입력

첫 줄에 n (1 ≤ n ≤ 100,000)이 주어진다. 둘째 줄부터 n개의 줄에는 수열을 이루는 1이상 n이하의 정수가 하나씩 순서대로 주어진다. 물론 같은 정수가 두 번 나오는 일은 없다.

## 출력

입력된 수열을 만들기 위해 필요한 연산을 한 줄에 한 개씩 출력한다. push연산은 +로, pop 연산은 -로 표현하도록 한다. 불가능한 경우 NO를 출력한다.

## 예제 입력 1

```
8
4
3
6
8
7
5
2
1
```

## 예제 출력 1

```
+
+
+
+
-
-
+
+
-
+
+
-
-
-
-
-
```

## 풀이

{:.notice--warning}
**문제설명**  
문제에서 주어지는 수열은 1~n까지의 연속되는 수가 스택에 넣고 빼면서 발생하는 수열이다  
즉, 기본적으로는 1~n의 차례대로 수열이 들어와야 하기 때문에 법칙성을 발견해야 한다.  
원본인 1~n까지의 수열을 우리가 만들고 문제에서 주어진 수열을 역산해야함  
  
1~n까지 차례대로 들어오는 것은 cnt와 stack으로 구현  
현재 들어오는 입력값까지 cnt를 늘리면서 stack에 추가하고  
입력값과 cnt가 같다면 pop을 해서 주어진 수열과 일치한지를 본다.  
  
일치할 경우 정상적인 상황이므로 계속 진행  
일치하지 않을 경우 잘못된 상황이니 프로그램을 종료하면 됨

{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int n;
    cin >> n;

    int cnt = 1;
    stack<int> st;
    string result;
    while (n--)
    {
        int t;
        cin >> t;
        
        //방금 들어온 입력값까지 cnt를 증가시키며 스택에 추가
        while (cnt <= t)
        {
            st.push(cnt);
            result += "+\n";
            cnt++;
        }
        
        //위의 반복문을 다 돌고 내려왔다는 것은 이제부터 빠지는 것밖에 없음
        //스택의 탑과 입력값이 맞지 않으면 옳바르지 않는 수열
        if (st.top() != t)
        {
            cout << "NO";
            return 0;
        }

        st.pop();
        result += "-\n";
    }
    cout << result;
}
{% endhighlight %}
