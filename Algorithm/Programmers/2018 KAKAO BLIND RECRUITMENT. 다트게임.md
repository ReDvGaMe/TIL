# Programmers(L1) - 2018 KAKAO BLIND RECRUITMENT. 다트게임

## 1. 문제  
https://programmers.co.kr/learn/courses/30/lessons/17682
## 2. 접근 방법  
점수를 3개의 구간으로 나눠서 계산 후 마지막에 더하여 출력
## 3. 코드  
```
#include <string>
#include <cmath>
#include <iostream>

using namespace std;

int solution(string dartResult) {
    int answer = 0;
    
    int num = 0, idx = 0, score[3];
    for(int i = 0; i < dartResult.size(); i++){
        if(dartResult[i] <= '9' && dartResult[i] >= '0'){
            if(dartResult[i+1] == '0'){
                num = 10;
                i++;
            }
            else
                num = dartResult[i] - '0';
        }
        else if(dartResult[i] >= 'D' && dartResult[i] <= 'T'){
            if(dartResult[i] == 'S')        score[idx] = num;
            else if(dartResult[i] == 'D')   score[idx] = pow(num, 2);
            else if(dartResult[i] == 'T')   score[idx] = pow(num, 3);
            idx++;
        }
        else if(dartResult[i] == '*' || dartResult[i] == '#'){
            if(dartResult[i] == '*'){
                score[idx-1] *= 2;
                if(idx != 1) score[idx - 2] *= 2;
            }
            if(dartResult[i] == '#')
                score[idx-1] *= -1;      
        }
    }
    
    for(int i = 0; i < 3; i++)
        answer += score[i];
    
    return answer;
}
```
## 4. 결과
- 결과 : Pass