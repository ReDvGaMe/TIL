# Programmers - 2018 KAKAO BLIND RECRUITMENT. [3차] 압축

## 1. 문제  
https://programmers.co.kr/learn/courses/30/lessons/17684
## 2. 접근 방법  

## 3. 코드  
```
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

int findWord(string word, vector<string>& dictionary)
{
    auto it = find(dictionary.begin(), dictionary.end(), word);
    return (it == dictionary.end()) ? -1 : it - dictionary.begin();
}

void LZW(string msg, int len, int start, int prev, vector<string>& dictionary, vector<int>& answer)
{
    if (start + len > msg.size())   return;

    string cmp = msg.substr(start, len);
    int idx = findWord(cmp, dictionary);
    // 단어를 못 찾았다면
    if (idx == -1)
    {
        answer.push_back(prev);
        dictionary.push_back(cmp);
        LZW(msg, 1, start + len - 1, -1, dictionary, answer);
    }
    // 찾았다면
    else
    {
        LZW(msg, len + 1, start, idx, dictionary, answer);
    }
}

vector<int> solution(string msg)
{
    vector<int> answer;
    vector<string> dictionary = { "", "A","B","C","D","E","F","G","H","I","J","K","L","M","N","O","P","Q","R","S","T","U","V","W","X","Y","Z" };

    LZW(msg, 1, 0, -1, dictionary, answer);

    return answer;
}
```
## 4. 결과
- 결과 : Pass