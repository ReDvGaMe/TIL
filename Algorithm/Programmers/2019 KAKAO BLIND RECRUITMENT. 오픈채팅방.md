# Programmers(L2) - 서머코딩／윈터코딩(~2018) 스킬 트리

## 1. 문제  
https://programmers.co.kr/learn/courses/30/lessons/42888
## 2. 접근 방법  
map을 이용해 사용자 id와 닉네임을 묶어서 관리
## 3. 코드  
```
#include <string>
#include <vector>
#include <map>

using namespace std;

vector<string> myStrtok(string str){
    vector<string> str_vec;
    string temp = "";
    for(int i = 0; i < str.size(); i++){
        if(str[i] == ' '){
            str_vec.push_back(temp);
            temp = "";
        }
        else
            temp += str[i];
    }
    str_vec.push_back(temp);
    return str_vec;
}

vector<string> solution(vector<string> record) {
    vector<string> answer;
    map<string, string> uid_map;
    // 명령어 순서 저장용 / Enter = 1, Leave = 2
    vector<pair<int, string>> order;

    for(auto s : record){
        vector<string> str_vec = myStrtok(s);

        if(str_vec[0] == "Enter"){
            order.push_back(make_pair(1, str_vec[1]));
            if(uid_map.find(str_vec[1]) == uid_map.end())
                uid_map.insert(make_pair(str_vec[1], str_vec[2]));
            else
                uid_map[str_vec[1]] = str_vec[2];
        }
        else if(str_vec[0] == "Leave"){
            order.push_back(make_pair(2, str_vec[1]));
        }
        else if(str_vec[0] == "Change"){
            string uid = str_vec[1];
            uid_map[str_vec[1]] = str_vec[2];
        }
    }

    for(int i = 0; i < order.size(); i++){
        string str = "";
        str += uid_map[order[i].second];
        if(order[i].first == 1){
            str += "님이 들어왔습니다.";
        }
        else{
            str += "님이 나갔습니다.";
        }
        answer.push_back(str);
    }


    return answer;
}
```

## 4. 결과
- 결과 : Pass

## 5. 리뷰
문자열을 문자로 나누는 방법, 맵에 저장 후 다시 문자열을 읽으면서 출력하는 부분이 비효율적