# Programmers - 2018 KAKAO BLIND RECRUITMENT. [3차] n진수 게임

## 1. 문제  
https://programmers.co.kr/learn/courses/30/lessons/17687
## 2. 접근 방법  

## 3. 코드  
```
#include <string>
#include <vector>

using namespace std;

// 자리수 계산(1 증가 후 n이 되면 true 리턴)
bool Calc_Digit(string& cur, int idx, int n)
{
    cur[idx]++;

    return (cur[idx] == ('0' + n)) ? true : false;
}

void nextNum(string& cur, int n)
{
    int i = 1;
    while (i <= cur.size() && Calc_Digit(cur, cur.size() - i, n))
    {
        cur[cur.size() - i] = '0';
        
        if (cur.size() - i == 0)
        {
            cur = '1' + cur;
            break;
        }

        i++;
    }
}

void appendNum(string& ans, string num, int digit)
{
    if (num[digit] <= '9')
        ans += num[digit];
    else
        ans += (num[digit] + 7);
}

// n : 진법, t : 미리 구할 숫자의 개수, m : 게임에 참가하는 인원, p : 튜브의 순서
string solution(int n, int t, int m, int p)
{
    string answer = "";

    string num = "0";
    int digit = 0;
    for (int i = 0; i < t; i++)
    {
        for (int j = 0; j < m; j++)
        {
            if (j+1 == p)
            {
                appendNum(answer, num, digit);
            }
            digit++;
            if (digit == num.size())
            {
                nextNum(num, n);
                digit = 0;
            }
        }
    }

    return answer;
}

int main()
{
    solution(2, 4, 2, 1);
    return 0;
}
```
## 4. 결과
- 결과 : Pass