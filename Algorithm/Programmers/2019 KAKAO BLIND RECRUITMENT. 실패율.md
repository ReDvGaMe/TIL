# Programmers(L1) - 2019 KAKAO BLIND RECRUITMENT. 실패율

## 1. 문제  
https://programmers.co.kr/learn/courses/30/lessons/42889
## 2. 접근 방법  
실패율을 계산 후 정렬
## 3. 코드  
```
#include <string>
#include <vector>
#include <algorithm>
#include <utility>

using namespace std;

bool cmp(const pair<double, int>& a, const pair<double, int>& b)
{
    if(a.first==b.first)
        return a.second < b.second;
    return a.first > b.first;
}

vector<int> solution(int N, vector<int> stages) {
    vector<int> answer;
    vector<int> clear(N, 0);
    vector<pair<double, int>> fail;
    
    for(int i = 0; i < stages.size(); i++){
        int len = (stages[i] == N+1) ? N : stages[i];

        for(int j = 0; j < len; j++){
            clear[j]++;
        }
    }
    
    for(int i = 0; i < N; i++){
        int not_clear = 0;
        for(int j = 0; j < stages.size(); j++){
            if(i+1 == stages[j])    not_clear++;
        }
        if(clear[i] != 0)   fail.push_back(make_pair((double)not_clear / clear[i], i+1));
        else    fail.push_back(make_pair(0, i+1));
    }
    
    sort(fail.begin(), fail.end(), cmp);
    
    for(auto p : fail)
        answer.push_back(p.second);

    return answer;
}
```
## 4. 결과
- 결과 : Pass

## 5. 리뷰
pair 혹은 구조체를 사용하여 여러 데이터를 묶은 후 정렬하는 방법을 정확히 익혀야함  
예외처리 잘해야함(이번 문제에서는 N+1 일때 N스테이지까지 클리어, 0으로 나누기)