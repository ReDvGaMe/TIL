# Programmers - 2018 KAKAO BLIND RECRUITMENT. [3차] 파일명 정렬

## 1. 문제  
https://programmers.co.kr/learn/courses/30/lessons/17686
## 2. 접근 방법  

## 3. 코드  
```
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

struct myFile
{
    string _head;
    string _num_str;
    int _num_int;
    int _order;

    myFile(string name, int order) : _order(order)
    {
        int head_len = 0, num_len = 0;
        for (int i = 0; i < name.size(); i++)
        {
            if (head_len == 0 && name[i] >= '0' && name[i] <= '9')
                head_len = i;
            if (head_len != 0 && num_len == 0 && (name[i] < '0' || name[i] > '9'))
            {
                num_len = i - head_len;
                break;
            }
        }
        if (num_len == 0)
            num_len = name.size() - head_len;

        _head = name.substr(0, head_len);
        for (int i = 0; i < _head.size(); i++)
        {
            _head[i] = tolower(_head[i]);
        }
        _num_str = name.substr(head_len, num_len);
        _num_int = stoi(_num_str);
    }
};

bool cmp_myFile(myFile A, myFile B)
{
    if (A._head < B._head)
        return true;
    else if (A._head == B._head)
    {
        if (A._num_int < B._num_int)
            return true;
        else if (A._num_int == B._num_int)
            return A._order < B._order;
        else
            return false;
    }
    else
        return false;
}

vector<string> solution(vector<string> files)
{
    vector<string> answer;

    vector<myFile> file_vec;
    for (int i = 0; i < files.size(); i++)
    {
        myFile temp(files[i], i);
        file_vec.push_back(temp);
    }

    sort(file_vec.begin(), file_vec.end(), cmp_myFile);

    for (int i = 0; i < file_vec.size(); i++)
    {
        string res;
        res = files[file_vec[i]._order];
        answer.push_back(res);
    }

    return answer;
}
```
## 4. 결과
- 결과 : Pass