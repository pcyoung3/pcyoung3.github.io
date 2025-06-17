---
layout: single
title: 백준 2448번 - 별 찍기 11
categories: algorithmProblems
tags: [algorithm, recursive]
toc: true
toc_sticky: true
---

## 문제
[백준 2448번 문제](https://www.acmicpc.net/problem/2448)

예제를 보고 규칙을 유추한 뒤에 별을 찍어 보세요.

## 입력

첫째 줄에 N이 주어진다. N은 항상 3×$2^k$ 수이다. (3, 6, 12, 24, 48, ...) (0 ≤ k ≤ 10, k는 정수)

## 출력

첫째 줄부터 N번째 줄까지 별을 출력한다.

## 예제 입력 1 복사
```
24
```


## 예제 출력 1 복사
```
                       *                        
                      * *                       
                     *****                      
                    *     *                     
                   * *   * *                    
                  ***** *****                   
                 *           *                  
                * *         * *                 
               *****       *****                
              *     *     *     *               
             * *   * *   * *   * *              
            ***** ***** ***** *****             
           *                       *            
          * *                     * *           
         *****                   *****          
        *     *                 *     *         
       * *   * *               * *   * *        
      ***** *****             ***** *****       
     *           *           *           *      
    * *         * *         * *         * *     
   *****       *****       *****       *****    
  *     *     *     *     *     *     *     *   
 * *   * *   * *   * *   * *   * *   * *   * *  
***** ***** ***** ***** ***** ***** ***** *****
```

   
## 풀이
<div class="notice--warning" markdown="1">
**<u>인덱스 계산에 주의</u>** <br>
![KakaoTalk_20230626_172826631.jpg](/assets/images/algorithm/KakaoTalk_20230626_172826631.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

char board[3500][7000];

void DrawStar(int row, int col, int height)
{
	//탈출문구
	//높이가 3이하면 가장 작은 삼각형을 그림
	if (height <= 3)
	{
		for (int i = 0; i < 3; ++i)
		{
			for (int j = -2; j < 3; ++j)
			{
				switch (i)
				{
				case 0:
					if (j == 0)
						board[row + i][col + j] = '*';
					break;
				case 1:
					if(j == -1 || j == 1)
						board[row + i][col + j] = '*';
					break;
				case 2:
					board[row + i][col + j] = '*';
					break;
				default:
					break;
				}
			}
		}
		return;
	}

	//height로 계산해야 현재 레벨에 관한 좌표계산 가능
	//가운데 삼각형, 왼쪽 삼각형, 오른쪽 삼각형	
	//Up
	DrawStar(row, col, height / 2);
	//Left
	DrawStar(row + height / 2, col - (height / 2), height / 2);
	//Right
	DrawStar(row + height / 2, col + (height / 2), height / 2);
}


int main()
{
    ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	
	int n;
	cin >> n;
	
	int temp = (n / 3);
	int width = (5 * temp) + (temp - 1); //n*2 - 1 도 가능
	int height = n;
	for (int i = 0; i < n; ++i)
		fill(board[i], board[i] + width, ' ');

	DrawStar(0, width / 2, n);
	for (int i = 0; i < n; ++i)
	{
		for (int j = 0; j < width; ++j)
		{
			cout << board[i][j];
		}
		cout << "\n";
	}
}
{% endhighlight %}
