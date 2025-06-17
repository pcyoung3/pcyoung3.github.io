---
layout: single
title: 백준 6603번 - 로또
categories: algorithmProblems
tags: [algorithm, backtracking]
toc: true
toc_sticky: true
---

## 문제
[백준 6603번 문제](https://www.acmicpc.net/problem/6603)

독일 로또는 {1, 2, ..., 49}에서 수 6개를 고른다.

로또 번호를 선택하는데 사용되는 가장 유명한 전략은 49가지 수 중 k(k>6)개의 수를 골라 집합 S를 만든 다음 그 수만 가지고 번호를 선택하는 것이다.

예를 들어, k=8, S={1,2,3,5,8,13,21,34}인 경우 이 집합 S에서 수를 고를 수 있는 경우의 수는 총 28가지이다. (\[1,2,3,5,8,13], \[1,2,3,5,8,21], \[1,2,3,5,8,34], \[1,2,3,5,13,21], ..., \[3,5,8,13,21,34])

집합 S와 k가 주어졌을 때, 수를 고르는 모든 방법을 구하는 프로그램을 작성하시오.

## 입력

입력은 여러 개의 테스트 케이스로 이루어져 있다. 각 테스트 케이스는 한 줄로 이루어져 있다. 첫 번째 수는 k (6 < k < 13)이고, 다음 k개 수는 집합 S에 포함되는 수이다. S의 원소는 오름차순으로 주어진다.

입력의 마지막 줄에는 0이 하나 주어진다. 

## 출력

각 테스트 케이스마다 수를 고르는 모든 방법을 출력한다. 이때, 사전 순으로 출력한다.

각 테스트 케이스 사이에는 빈 줄을 하나 출력한다.

## 예제 입력 1 복사
```
7 1 2 3 4 5 6 7
8 1 2 3 5 8 13 21 34
0
```

## 예제 출력 1 복사
```
1 2 3 4 5 6
1 2 3 4 5 7
1 2 3 4 6 7
1 2 3 5 6 7
1 2 4 5 6 7
1 3 4 5 6 7
2 3 4 5 6 7

1 2 3 5 8 13
1 2 3 5 8 21
1 2 3 5 8 34
1 2 3 5 13 21
1 2 3 5 13 34
1 2 3 5 21 34
1 2 3 8 13 21
1 2 3 8 13 34
1 2 3 8 21 34
1 2 3 13 21 34
1 2 5 8 13 21
1 2 5 8 13 34
1 2 5 8 21 34
1 2 5 13 21 34
1 2 8 13 21 34
1 3 5 8 13 21
1 3 5 8 13 34
1 3 5 8 21 34
1 3 5 13 21 34
1 3 8 13 21 34
1 5 8 13 21 34
2 3 5 8 13 21
2 3 5 8 13 34
2 3 5 8 21 34
2 3 5 13 21 34
2 3 8 13 21 34
2 5 8 13 21 34
3 5 8 13 21 34
```
   
## 풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int arr[15];
int val[15];
int k;

//결과수열이 무조건 오름차순이라 인자값2개
void BackTracking(int length, int idx)
{
	if (length == 6) //최종길이가 6
	{
		for (int i = 0; i < 6; i++)
			cout << arr[i] << " ";
		cout << "\n";
		return;
	}

	for (int i = idx; i < k; i++)
	{
		arr[length] = val[i];
		BackTracking(length + 1, i + 1);
	}
}

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	while (1)
	{
		cin >> k;
		if (k == 0)
			break;

		for (int i = 0; i < k; i++)
			cin >> val[i];
		
		BackTracking(0, 0);

		cout << "\n";
	}
}
{% endhighlight %}
   
## 다른풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int k, arr[15], mask[15];
int main(void) {
  ios::sync_with_stdio(0);
  cin.tie(0);

  while (1) {
    cin >> k;
    if (!k) break;

    for (int i = 0; i < k; i++)
      cin >> arr[i];
    for (int i = 6; i < k; i++)
      mask[i] = 1; // 뽑히지 않아야 할 원소를 표시
    do {
      for (int i = 0; i < k; i++) {
        if (!mask[i]) cout << arr[i] << " ";
      }
      cout << '\n';
    } while (next_permutation(mask, mask + k));
    cout << '\n';
  }
}
/* 
원래 한 실행에 여러 개의 케이스를 받는 문제의 경우에는 다음 케이스를 받기 전에 배열을 초기화 시켜주어야 하나,
본 문제의 경우에는 이전 케이스의 정보가 덮어씌워지므로 관련 과정을 생략하였습니다.

next_permutation은 순서상 마지막 수열에 도달했을 때 (이 문제의 경우 1 1 1 ... 1 0 0 0 0 0 0) 다시 첫 수열
(이 문제의 경우 0 0 0 0 0 0 1 1 ... 1)로 돌아간 후 false를 반환하기 때문에 mask 배열 또한 각 TC가 끝난 후
자연스럽게 앞 6칸에 0이 들어가있어서 초기화가 필요없습니다.
*/
{% endhighlight %}
