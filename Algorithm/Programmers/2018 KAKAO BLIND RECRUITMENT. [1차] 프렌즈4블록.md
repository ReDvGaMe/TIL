# Programmers - 2018 KAKAO BLIND RECRUITMENT. [1차] 프렌즈4블록

## 1. 문제  
https://programmers.co.kr/learn/courses/30/lessons/17679
## 2. 접근 방법  

## 3. 코드  
```
#include <string>
#include <vector>
#include <queue>
#include <utility>

using namespace std;

bool chk(int i, int j, vector<string>& board)
{
    // 범위 검사
    if (i + 1 == board.size() || j + 1 == board[0].size())
        return false;

    // 해당 칸이 비었으면
    if (board[i][j] == 'a')
        return false;

    // 해당 좌표의 →, ↓, ↘를 검사하여 모두 같으면 제거
    return board[i][j] == board[i][j + 1] && board[i][j] == board[i + 1][j] && board[i][j] == board[i + 1][j + 1];
}

int Clear4Block(pair<int, int> pos, vector<string>& board)
{
    int res = 0;
    if (board[pos.first][pos.second] != 'a')
    {
        res++;
        board[pos.first][pos.second] = 'a';
    }
    if (board[pos.first][pos.second + 1] != 'a')
    {
        res++;
        board[pos.first][pos.second + 1] = 'a';
    }
    if (board[pos.first + 1][pos.second] != 'a')
    {
        res++;
        board[pos.first + 1][pos.second] = 'a';
    }
    if (board[pos.first + 1][pos.second + 1] != 'a')
    {
        res++;
        board[pos.first + 1][pos.second + 1] = 'a';
    }
    return res;
}

void PullDown(vector<string>& board)
{
    for (int i = 0; i < board.size(); i++)
    {
        for (int j = 0; j < board[0].size(); j++)
        {
            if (board[i][j] == 'a')
            {
                if (i == 0)  board[i][j] = 'a';
                else
                {
                    int here = i;
                    int there = here - 1;
                    while (there >= 0)
                    {
                        board[here][j] = board[there][j];
                        board[there][j] = 'a';
                        here--; there--;
                    }
                }
            }
        }
    }
}

int Process(vector<string>& board)
{
    int res = 0;
    queue<pair<int, int>> q;

    // 사라질 블록을 체크
    for (int i = 0; i < board.size() - 1; i++)
    {
        for (int j = 0; j < board[0].size() - 1; j++)
        {
            if (chk(i, j, board))    q.push(make_pair(i, j));
        }
    }

    // 없어질 블록이 없다면 리턴
    if (q.empty())
        return res;

    // 없어질 블록 처리
    while (!q.empty())
    {
        pair<int, int> pos = q.front();
        q.pop();
        res += Clear4Block(pos, board);
    }

    // 사라진 블록을 메꿔줌
    PullDown(board);

    return res + Process(board);
}

int solution(int m, int n, vector<string> board)
{
    return Process(board);
}
```
## 4. 결과
- 결과 : Pass