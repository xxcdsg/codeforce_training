## [2024.3.13 D. Pinball](https://codeforces.com/contest/1937/problem/D)

* 球碰到 $>$ 向右边移动一格，碰到 $<$ 向左边移动一格，并且会将经过的符号反向，问从每个位置出发，最后跑到字符串外面需要移动的距离

##### 思路：

* 蒙的
* 如果一个位置与原本球移动的方向一致，那么它之后无论如何都不会反弹这个球，也就是贡献为0
* 我们假设所有反弹的位置有贡献，并且可以直接把球弹出字符串，那么它的贡献就是

$>:n-i+1 \\ <:i$

* 因为我们要求的肯定是一段区间的贡献，那么求前缀和即可
* 然后这个贡献肯定不是完全达到的，因为反弹过去，肯定会在出去前反弹回来，那么记录这个因为反弹而损失的

$>:i \\ <:n-i+1$

* 我们还需要知道从每个位置最后的贡献区间
* 因为它最后肯定是弹出去，那么贡献区间的 $l=1$ 或者 $r=n$ 分类讨论
* 如果是 $l=1$ ，那么 $i$ 位置的右边的 $<$ 数量肯定比 $i$ 位置左边的（包括 $i$ 位置）数量多，同理 $r=n$ 时
* 然后 $i$ 位置右边的 $>$ 与 $i$ 位置左边的 $<$ 肯定无贡献，因此差分 $1\to i\ >$ 的贡献与 $i\to r <$ 的贡献，然后减去 $1\to i\ >$ 的损失与 $i\to r <$ 的损失
* 但是我们模拟可得到，其实我们一开始的 $i$ 位置的损失是没有的，因为它是开始，没有反弹别人推来的球，因此还要重新加上它的损失
* 至于 $r$ （或者 $l$ ）我们用二分来找即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;
#define int long long

const int inf = 1e9 + 10;
const ll INF = 1e18 + 10;

const int N = 5e5 + 10;

int lsum[N],rsum[N],ld[N],rd[N],lnum[N],rnum[N];

void test(){
	int n;cin >> n;
	string s;cin >> s;
	s = ' ' + s;
	for(int i = 1;i <= n;i++){
		lsum[i] = lsum[i - 1];
		rsum[i] = rsum[i - 1];
		rnum[i] = rnum[i - 1];
		ld[i] = ld[i - 1];
		rd[i] = rd[i - 1];
		if(s[i] == '>'){
			rsum[i] += n + 1 - i;
			rnum[i]++;
			rd[i] += i;
		}else{
			lsum[i] += i;
			ld[i] += n + 1 - i;
		}
	}
	rnum[n + 1] = 0;
	for(int i = n;i >= 1;i--){
		lnum[i] = lnum[i + 1];
		if(s[i] == '<')
			lnum[i]++;
	}
	for(int i = 1;i <= n;i++){
		if((s[i] == '>' && lnum[i] >= rnum[i]) || lnum[i] > rnum[i])//向左边的多
		{
			int l = 1;
			int rn = rnum[i];
			int _l = 1,_r = n;
			while(_l <= _r){
				int mid = (_l + _r) / 2;
				if(lnum[i + 1] - lnum[mid + 1] >= rn)
					_r = mid - 1;
				else
					_l = mid + 1;
			}
			int r = _l;
			cout << (lsum[r] - lsum[i - 1] + rsum[i] - rsum[l - 1] - (ld[r] - ld[i - 1] + rd[i] - rd[l - 1])) + (s[i] == '>' ? i : n - i + 1) << ' ';
		}else{
			int r = n;
			int ln = lnum[i];
			int _l = 1,_r = n;
			while(_l <= _r){
				int mid = (_l + _r) / 2;
				if(rnum[i - 1] - rnum[mid - 1] >= ln)
					_l = mid + 1;
				else
					_r = mid - 1;
			}
			int l = _r;
			cout << (lsum[r] - lsum[i - 1] + rsum[i] - rsum[l - 1] - (ld[r] - ld[i - 1] + rd[i] - rd[l - 1])) + (s[i] == '>' ? i : n - i + 1) << ' ';
		}
	}
	cout << '\n';
}

signed main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

