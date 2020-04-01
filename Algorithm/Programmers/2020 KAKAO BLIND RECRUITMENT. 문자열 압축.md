# Programmers - 2020 KAKAO BLIND RECRUITMENT. 문자열 압축

## 1. 문제  
https://programmers.co.kr/learn/courses/30/lessons/60057
## 2. 접근 방법  
최대 문자열 / 2 까지 검사하여 각 자리수별로 문자열의 길이를 구하여 비교
## 3. 코드  
```
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

// n개 단위로 글자를 잘라서 검사
int chkStr(int n, string s){
    int res = s.size();
    string prevStr = "";
    int cnt = 1;
    
    for(int i = 0; i <= s.size() - n; i += n){
        string here = "", there = "";
        for(int j = i; j < i + n; j++){
            here += s[j];
            there += s[j + n];
        }
        
        if(here == there){
            if(prevStr != here){
                prevStr = here;
                res++;
                cnt = 1;
            }
            res -= n;
            cnt++;
            if(cnt == 10 || cnt == 100 || cnt == 1000)
                res++;
        }
    }
    
    return res;
}

int solution(string s) {
    int answer = s.size();
    
    for(int i = 1; i <= s.size() / 2; i++){
        answer = min(answer, chkStr(i, s));
    }
    
    return answer;
}
```
## 4. 결과
- 결과 : Pass