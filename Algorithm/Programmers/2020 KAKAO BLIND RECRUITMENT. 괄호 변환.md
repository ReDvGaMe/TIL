# Programmers(L2) - 2020 KAKAO BLIND RECRUITMENT. 괄호 변환

## 1. 문제  
https://programmers.co.kr/learn/courses/30/lessons/60058
## 2. 접근 방법  
주어진 알고리즘을 하나씩 함수로 구현
## 3. 코드  
```
#include <string>
#include <vector>
#include <stack>

using namespace std;

bool Check_Balanced(string str){    
    int open = 0, close = 0;    
    for(int i = 0; i < str.size(); i++){
        if(str[i] == '(')
            open++;
        else
            close++;
    }
    return open == close;
}

bool Check_Correct(string str){
    stack<char> Parentheses;
    for(int i = 0; i < str.size(); i++){
        if(str[i] == '(')
            Parentheses.push('(');
        else{
            if(!Parentheses.empty() && Parentheses.top() == '(')
                Parentheses.pop();
            else
                return false;
        }
    }
    
    return Parentheses.empty();
}

string Convert_Parentheses(string w){
    if(w.empty())   return "";
    
    string u, v, res = "";
    int idx = 2;
    do {
        u = w.substr(0, idx);
        v = w.substr(idx);
        idx += 2;
    }while(!Check_Balanced(u) && !Check_Balanced(v));
    
    if(Check_Correct(u)){
        res += u;
        res += Convert_Parentheses(v);
    }
    else{
        res += "(";
        res += Convert_Parentheses(v);
        res += ")";
        
        u = u.substr(1, u.size() - 2);
        for(int i = 0; i < u.size(); i++){
            if(u[i] == '(') res += ")";
            else            res += "(";
        }
    }
    
    return res;
}

string solution(string p) {
    // 예외처리
    if(Check_Correct(p))    return p;
    
    return Convert_Parentheses(p);
}
```
## 4. 결과
- 결과 : Pass