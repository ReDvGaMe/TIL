## 개요  
여러 유형을 직접 구현해보는 연습장

---

# 완전탐색

## 순열
```
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

vector<int> arr;

int main()
{	
	int r;
	cin >> r;

	for (int i = 0; i < r; i++)
		arr.push_back(i + 1);

	do
	{
		for (auto i : arr)
			cout << i << " ";
		cout << endl;
	} while (next_permutation(arr.begin(), arr.end()));

	return 0;
}
```

## 조합
```
#include <iostream>
#include <vector>
using namespace std;

vector<int> arr;

void printPicked(vector<int>& picked)
{
	for (auto i : picked)
		cout << i << " ";
	cout << endl;
	return;
}

// N개의 원소 중에서 R개를 순서 없이 골라낸 것
void combination(int n, vector<int>& picked, int toPick, int idx = 0)
{
	// 기저사례 : R개를 모두 골랐을 때 출력 후 리턴
	if (toPick == 0)
	{	
		printPicked(picked);
		return; 
	}

	for (int next = idx; next < n; next++)
	{
		picked.push_back(arr[next]);
		combination(n, picked, toPick-1, next+1);
		picked.pop_back();
	}
}

int main()
{	
	// 초기화
	for (int i = 0; i < 10; i++)
		arr.push_back(i + 1);

	int r;
	cin >> r;

	vector<int> ans;
	combination(10, ans, r);

	return 0;
}
```