# GoormLEVEL - 암스트롱 수(Narcissistic Number)

## 1. 문제  
https://level.goorm.io/exam/43209/%EC%95%94%EC%8A%A4%ED%8A%B8%EB%A1%B1-%EC%88%98-narcissistic-number/quiz/1
## 2. 접근 방법  
반복문을 사용해 범위내 모든 수를 계산
## 3. 코드  
```
#include <iostream>
#include <cmath>
using namespace std;
int main() {
	int a, b;
	cin >> a >> b;
	
	for(int i = a; i <= b; i++){
		int t = i, val = 0;
		while(t != 0){
			int digit = t % 10;
			val += pow(digit, 3);
			t /= 10;
		}
		if(val == i)	cout << i << " ";
	}
	
	return 0;
}
```
## 4. 결과
- 결과 : Pass