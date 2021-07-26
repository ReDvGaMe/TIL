# Programmers - 2020 KAKAO BLIND RECRUITMENT. 자물쇠와 열쇠

## 1. 문제  
https://programmers.co.kr/learn/courses/30/lessons/60059
## 2. 접근 방법  

## 3. 코드  
```
#include <string>
#include <vector>

using namespace std;

vector<vector<int>> vec;
int cnt;

// 해당 Key를 시계 방향으로 90도 회전시킴
void rotate_key(vector<vector<int>>& key)
{
    vector<vector<int>> temp = key;

    for (int i = 0; i < key.size(); i++)
        for (int j = 0; j < key.size(); j++)
            key[i][j] = temp[key.size() - 1 - j][i];
}

bool chk_key(vector<vector<int>>& key, vector<vector<int>>& lock, vector<vector<int>>& obj)
{
    for (int i = 0; i < lock.size(); i++)
        for (int j = 0; j < lock.size(); j++)
            if (obj[i + key.size() - 1][j + key.size() - 1] != 1)
                return false;

    return true;
}

// 해당 Key를 가지고 Lock에 대응하여 열리는지 확인
bool ins_key(vector<vector<int>>& key, vector<vector<int>>& lock)
{    
    for (int start_y = 0, start_x = 0; start_x != cnt + 1 && start_y != cnt; )
    {
        // 열쇠 겹쳐보기
        vector<vector<int>> temp = vec;
        for (int i = 0; i < key.size(); i++)
            for (int j = 0; j < key.size(); j++)
                temp[i + start_y][j + start_x] += key[i][j];

        if (chk_key(key, lock, temp))  return true;

        if (start_x != cnt - 1) start_x++;
        else
        {
            start_x = 0;    start_y++;
        }
    }
    return false;
}

bool solution(vector<vector<int>> key, vector<vector<int>> lock)
{
    int vec_size = (key.size() * 2) + lock.size() - 2;
    vec.assign(vec_size, vector<int>(vec_size, 0));
    cnt = key.size() + lock.size() - 1;

    for (int i = 0; i < lock.size(); i++)
        for (int j = 0; j < lock.size(); j++)
            vec[i + key.size() - 1][j + key.size() - 1] = lock[i][j];\

    if (ins_key(key, lock))  return true;

    for (int i = 0; i < 3; i++)
    {
        rotate_key(key);

        if (ins_key(key, lock))  return true;
    }

    return false;
}
```
## 4. 결과
- 결과 : Pass

## 5. 리뷰
본 시험에서는 풀지 못했던 문제  
단순하게 생각하여 배열의 범위를 넓혀 키가 삽입될 수 있는 모든 경우의 수를 해보는 것이 포인트  
0,0 부터 n,n까지 검사 후 키를 회전시키고 같은 검사를 반복
