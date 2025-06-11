---
layout: single
title: 백준 10799번 - 쇠막대기
categories: algorithmProblems
tags: [algorithm, stack]

toc: true
toc_sticky: true
---

## 문제
[백준 10799번 문제](https://www.acmicpc.net/problem/10799)

## 풀이
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	string input;
	cin >> input;

	stack<char> st;
	char before;
	int result = 0;
	for (auto& ch : input)
	{
		if (ch == '(')
		{
			st.push(ch);
		}
		else
		{
			st.pop();
			if (before == '(') //laser
			{
				result += st.size();	//레이저를 쏘았을 때 짤리는 파이프
			}
			else
			{
				result++;		//남은 파이프
			}
		}
		before = ch;
	}
	cout << result;
}
{% endhighlight %}
