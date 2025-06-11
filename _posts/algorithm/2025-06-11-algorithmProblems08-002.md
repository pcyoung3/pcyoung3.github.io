---
layout: single
title: 백준 5430번 - AC
categories: algorithmProblems
tags: [algorithm, deque]
toc: true
toc_sticky: true
---

## 문제
[백준 5430번 문제](https://www.acmicpc.net/problem/5430)

선영이는 주말에 할 일이 없어서 새로운 언어 AC를 만들었다. AC는 정수 배열에 연산을 하기 위해 만든 언어이다. 이 언어에는 두 가지 함수 R(뒤집기)과 D(버리기)가 있다.

함수 R은 배열에 있는 수의 순서를 뒤집는 함수이고, D는 첫 번째 수를 버리는 함수이다. 배열이 비어있는데 D를 사용한 경우에는 에러가 발생한다.

함수는 조합해서 한 번에 사용할 수 있다. 예를 들어, "AB"는 A를 수행한 다음에 바로 이어서 B를 수행하는 함수이다. 예를 들어, "RDD"는 배열을 뒤집은 다음 처음 두 수를 버리는 함수이다.

배열의 초기값과 수행할 함수가 주어졌을 때, 최종 결과를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 테스트 케이스의 개수 T가 주어진다. T는 최대 100이다.

각 테스트 케이스의 첫째 줄에는 수행할 함수 p가 주어진다. p의 길이는 1보다 크거나 같고, 100,000보다 작거나 같다.

다음 줄에는 배열에 들어있는 수의 개수 n이 주어진다. (0 ≤ n ≤ 100,000)

다음 줄에는 \[x1,...,xn]과 같은 형태로 배열에 들어있는 정수가 주어진다. (1 ≤ xi ≤ 100)

전체 테스트 케이스에 주어지는 p의 길이의 합과 n의 합은 70만을 넘지 않는다.

## 출력

각 테스트 케이스에 대해서, 입력으로 주어진 정수 배열에 함수를 수행한 결과를 출력한다. 만약, 에러가 발생한 경우에는 error를 출력한다.

## 예제 입력 1 복사

```
4
RDD
4
[1,2,3,4]
DD
1
[42]
RRD
6
[1,1,2,3,5,8]
D
0
[]
```

## 예제 출력 1 복사

```
[2,1]
error
[1,2,3,5,8]
error
```

## 풀이
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

void parse(string& tmp, deque<int>& d) 
{
	int cur = 0;
	for (int i = 1; i + 1 < tmp.size(); i++)
	{
		if (tmp[i] == ',') 
		{
			d.push_back(cur);
			cur = 0;
		}
		else
		{
			cur = 10 * cur + (tmp[i] - '0');
		}
	}
	if (cur != 0)
		d.push_back(cur);
}

void print_result(deque<int>& d) 
{
	cout << '[';
	for (int i = 0; i < d.size(); i++)
	{
		cout << d[i];
		if (i + 1 != d.size())
			cout << ',';
	}
	cout << "]\n";
}

int t;
int main() 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> t;
	while (t--) 
	{
		deque<int> d;
		int rev = 0;
		int n;
		bool isWrong = false;
		string query, tmp;
		cin >> query;
		cin >> n;
		cin >> tmp;

		parse(tmp, d);

		for (char c : query)
		{
			if (c == 'R')
				rev = 1 - rev;
			else 
			{
				if (d.empty()) 
				{
					isWrong = true;
					break;
				}
				if (!rev) 
					d.pop_front();
				else 
					d.pop_back();
			}
		}

		if (isWrong)
			cout << "error\n";
		else {
			if (rev) 
				reverse(d.begin(), d.end());
			print_result(d);
		}
	}
}
{% endhighlight %}

**<u>틀린풀이</u>** 
<div class="notice--warning" markdown="1">

**반례**
```
1
DRDR
4
[1,2,3,4]
```

**<u>오답</u>** 
```
[3,4]
```
{: .notice--info}

**<u>정답</u>** 
```
[2,3]
```
{: .notice--info}

![KakaoTalk_20231201_111431201.jpg](/assets/images/algorithm/KakaoTalk_20231201_111431201.jpg)

번갈아서 회전, 삭제 연산을 할 경우 덱 2개로 처리했을 때 틀린답이 나옴
</div>

{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

int arr[100010];
int n;

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int t;
	cin >> t;

	while (t--)
	{
		string p, temp;
		cin >> p >> n >> temp;
		int idx = 0;
		temp.erase(temp.begin());
		temp.erase(temp.end() - 1);
		std::replace(temp.begin(), temp.end(), ',', ' ');
		stringstream s(temp);
		while (s)
			s >> arr[idx++];

		deque<int> dqNormal, dqReverse;
		for (int i = 0; i < n; i++)
		{
			dqNormal.push_back(arr[i]);
			dqReverse.push_back(arr[i]);
		}
		//처음 한번만 회전시키는 개념인데 이대로 하면 오답
		reverse(dqReverse.begin(), dqReverse.end());

		bool isReverse = false;
		bool isError = false;
		for (int i = 0; i < p.length(); i++)
		{
			if (p[i] == 'R')
				isReverse = !isReverse;
			else if (p[i] == 'D')
			{
				if (dqNormal.empty())
				{
					isError = true;
					break;
				}
				dqNormal.pop_front();
				dqReverse.pop_front();
			}
		}
		if (isError)
			cout << "error\n";
		else
		{
			deque<int>* outputDeque = nullptr;
			if (!isReverse)
				outputDeque = &dqNormal;
			else
				outputDeque = &dqReverse;

			string outputStr = "[";
			for (auto num : *outputDeque)
			{
				outputStr += to_string(num);
				outputStr += ',';
			}
			if (outputStr.length() > 1)
				outputStr.erase(outputStr.end() - 1);
			outputStr += "]\n";
			cout << outputStr;
		}
	}
}
{% endhighlight %}
