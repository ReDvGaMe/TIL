# GoormLEVEL - n 구하기

## 1. 문제  
https://level.goorm.io/exam/43264/n-%EA%B5%AC%ED%95%98%EA%B8%B0/quiz/1
## 2. 접근 방법  
반복문을 사용
## 3. 코드  
```
#include <iostream>
using namespace std;
int main() {
	int n;
	cin >> n;
	
	int sum = 0;
	for(int i = 1; i <= n; i++){
		sum += i;
		if(sum > n){
			cout << i;
			break;
		}
	}
	return 0;
}
```
## 4. 결과
- 결과 : Pass