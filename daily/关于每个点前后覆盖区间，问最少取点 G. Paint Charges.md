## [2024.3.12 *2300 G. Paint Charges](https://codeforces.com/problemset/problem/1927/G)

##### 题意：每个点可以向前或者向后覆盖 $a[i]$ 的区间，问最少取几个点可以将全区间覆盖

*  $O(n^3)$ 思路：
* 设 $dp[i][j][k]$ ，其中 $i$ 代表前面准备取或者不取点 $i$ ， $j$ 代表最前面的未选择的点， $k$ 代表在 $i$ 之后(包括 $i$ )，距 $i$ 最近的点
* 这样设的关键在于，如果我们从前往后枚举，那么如果前面有未被覆盖的点，那么在选择取一个点向前覆盖时一定是要将那个点覆盖的，否则后面的点就需要向前覆盖包括这个区间与那个点才能将所有区间覆盖
* 然后遍历 $i=0\to n-1，j=0\to n，k=0\to n$ ，输出 $dp[n][n][n]$ 即可
* 转移方程为：
* 不取点，那么 $j$ 肯定不变，但是 $k$ 要根据 $i + 1$ 而变
*  $dp[i+1][j][max(k,i+1)]=min(dp[i+1][j][max(k,i+1)],dp[i][j][k]+1)$
* 向前取，那么 $j$ 就跑到 $i$ 的后面，与 $k$ 相同了
*  $dp[i+1][max(k,i+1)][max(k,i+1)]=min(dp[i+1][max(k,i+1)][max(k,i+1)],dp[i][j][k]+1)$

* 向后取，这时 $k$ 的更新很容易，就是到 $min(i+a[i],n)$ ，但是问题是 $j$ ，如果 $j$ 在 $i$ 之前，那么这个向后的区间肯定对它无效，但是如果如果 $j$ 在 $i$ 之后，那么 $j$ 也就和 $k$ 相同就是 $min(i+a[i],n)$

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

void test(){
	int n;cin >> n;
	vector<int> a(n);
	for(int i = 0;i < n;i++) cin >> a[i];

	vector<vector<vector<int>>> dp(n + 1,vector<vector<int>>(n + 1,vector<int>(n + 1,inf)));

	dp[0][0][0] = 0;

	for(int i = 0;i < n;i++)
		for(int j = 0;j <= n;j++)
			for(int k = j;k <= n;k++){
				dp[i + 1][j][max(k,i + 1)] = min(dp[i][j][k],dp[i + 1][j][max(k,i + 1)]);

				if(i - a[i] + 1 <= j){
					int pre = max(k,i + 1);
					int nex = max(k,i + 1);
					dp[i + 1][pre][nex] = min(dp[i][j][k] + 1,dp[i + 1][pre][nex]);
				}

				if(i + a[i] - 1 >= k){
					int pre = j;
					int nex = min(i + a[i],n);
					if(j >= i) pre = nex;
					dp[i + 1][pre][nex] = min(dp[i + 1][pre][nex],dp[i][j][k] + 1);
				}

			}

	cout << dp[n][n][n] << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

* $O(n^3)$思路的关键就是考虑到每个位置取前面区间时一定要将最前面没取到的部分全取到，否则就一定有后面的点用更大的区间覆盖前面的点与原本的区间
* 然后是$O(n^2)$思路：
* 我们设 $f[i]$ 为 $1\to i$ 都被覆盖需要的最少需要的点
* 那么单纯的向前与向后加更新 $i$ 很简单，因为 $f[j]$ 没有到 $i$ ，也不会导致一个点两个覆盖方向
* 那么我们没有考虑的就是像 $[(])$ 这样的区间选择
* 我们从前往后遍历，那么前面的可以被当时的 $j$ 选到，那么它的最前面的值就是我们要更新的值，不过我写的那个更新肯定是错的，因为最后的更新考虑的是两步跑到前面已经更新的区域来更新自身

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

void test(){
	int n;cin >> n;
	vector<int> f(n + 1,inf);
	vector<int> a(n + 1,0);
	for(int i = 1;i <= n;i++) cin >> a[i];
	f[0] = 0;
	for(int i = 1;i <= n;i++){

		for(int j = max(i - a[i],0);j < i;j++)//向前
			f[i] = min(f[i],f[j] + 1);

		for(int j = 1;j < i;j++)//向后
			if(j + a[j] - 1 >= i)
				f[i] = min(f[i],f[j - 1] + 1);

		int mi = inf;

		int k = i - 1;

		for(int j = i + 1;j <= min(i + a[i] - 1,n);j++){
			while(k >= max(j - a[j],0)){
				mi = min(f[k],mi);
				k--;
			}

			f[j] = min(f[j],mi + 2);
		}
		// for(int j = i + 1;j <= min(i + a[i] - 1,n);j++)
		// 	f[j] = min(f[j],mi + 2);
	}
	cout << f[n] << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

