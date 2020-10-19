# Programmers - 2 x n 타일링

## 1. 문제  
https://programmers.co.kr/learn/courses/30/lessons/12900

## 2. 접근 방법  
다이내믹 프로그래밍

## 3. 코드  
```
#include <string>
#include <vector>

using namespace std;

vector<int> cache;

int setTile(int n){
    if(n <= 0)  return 0;
    int& res = cache[n];
    if(res != -1)   return res;
    
    return res = (setTile(n - 1) + setTile(n - 2)) % 1000000007;
}

int solution(int n) {
    cache.assign(n + 1, -1);
    cache[1] = 1;
    cache[2] = 2;
    
    return setTile(n);
}
```
## 4. 결과
- 결과 : Pass