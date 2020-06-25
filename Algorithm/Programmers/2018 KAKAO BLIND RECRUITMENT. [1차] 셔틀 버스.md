# Programmers - 2018 KAKAO BLIND RECRUITMENT. [1차]셔틀 버스

## 1. 문제  
https://programmers.co.kr/learn/courses/30/lessons/17678
## 2. 접근 방법  
셔틀버스가 오는 시간을 정리, 크루들이 무슨 버스에 타는지 정리 후  
마지막 버스의 자리가 남는다면 마지막 버스의 시간을  
남지 않는다면 마지막 사람보다 1분 빨리 도착하게함
## 3. 코드  
```
#include <string>
#include <vector>
#include <queue>
#include <algorithm>

using namespace std;

// n번째 셔틀에 탈 수 있는 크루
vector< vector<string> > vec;

string timetostr(int h, int min)
{
    string str = "";
    str += (h < 10) ? ("0" + to_string(h)) : to_string(h);
    str += ":";
    str += (min < 10) ? ("0" + to_string(min)) : to_string(min);

    return str;
}

vector<string> makeBusTable(int n, int t)
{
    vector<string> bustime;
    bustime.push_back("09:00");
    int h = 9, min = 0;
    for (int i = 1; i < n; i++)
    {
        min += t;
        if (min >= 60)
        {
            min -= 60;
            h++;
        }

        string time = timetostr(h, min);

        bustime.push_back(time);
    }

    return bustime;
}

void chkTime(vector<string>& timetable, vector<string>& bustimetable, int shuttle, int idx, int n, int m)
{
    if (shuttle == n)
        return;

    int cur = idx;
    for (int i = 0; i < m; i++)
    {
         if (cur < timetable.size() && timetable[cur] <= bustimetable[shuttle])
        {
            vec[shuttle].push_back(timetable[cur]);
            cur++;
        }
    }

    chkTime(timetable, bustimetable, shuttle + 1, cur, n, m);
}

// n회 t분 간격으로 셔틀 도착, 셔틀 하나의 최대 인원 m
string solution(int n, int t, int m, vector<string> timetable)
{
    string answer = "";

    // 오른차순 정렬
    sort(timetable.begin(), timetable.end());

    for (int i = 0; i < n; i++)
        vec.push_back(vector<string>());

    vector<string> bustime = makeBusTable(n, t);
    chkTime(timetable, bustime, 0, 0, n, m);

    // 마지막 버스의 줄이 꽉 찼다면
    if (vec[n - 1].size() == m)
    {
        int h = (vec[n - 1][m - 1][0] - '0') * 10 + (vec[n - 1][m - 1][1] - '0');
        int min = (vec[n - 1][m - 1][3] - '0') * 10 + (vec[n - 1][m - 1][4] - '0');

        min--;

        if (min == -1)
        {
            h--;
            min = 59;
        }

        answer = timetostr(h, min);
    }
    else
        answer = bustime[n - 1];

    return answer;
}
```
## 4. 결과
- 결과 : Pass