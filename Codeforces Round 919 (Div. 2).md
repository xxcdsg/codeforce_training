## [Codeforces Round 919 (Div. 2)](https://codeforces.com/contest/1920)

### A. Satisfying Constraints

* 给出 $n$ 个限制 $k$ 的条件，问 $k$ 的可能值的数量
* 条件可以是大于等于某个数；小于等于某个数；不能为某个数

##### 思路：

* 记录最大的最小值，最小的最大值，在二分去掉在范围内的不能成为的数

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

void test(){
	int n;cin >> n;
	vector<int> ve;
	int mi = 0,ma = 1e9 + 10;
	for(int i = 1;i <= n;i++){
		int a,x;cin >> a >> x;
		if(a == 1){
			mi = max(mi,x);
		}else if(a == 2){
			ma = min(ma,x);
		}else{
			ve.push_back(x);
		}
	}
	sort(ve.begin(),ve.end());
	if(ma < mi) cout << 0 << '\n';
	else{
		cout << (ma - mi + 1) - (upper_bound(ve.begin(),ve.end(),ma) - upper_bound(ve.begin(),ve.end(),mi - 1)) << '\n';
	}
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

### B. Summation Game

* A最多可移除k个数
* B最多可以给x个数乘-1
* A先B后
* A目标让最后的和最大，B目标让最后的和最小

##### 思路：

* B为了让和最小，那么就是从最大的数开始选，乘上-1
* 那么A为了让B乘上的-1的数更小，它去掉的也肯定是从最大的数开始
* 那么枚举即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 2e5 + 10;

int a[N];

void test(){
	int n,k,x;cin >> n >> k >> x;
	int ma = -1e9;
	int ans = 0;
	for(int i = 1;i <= n;i++){
		cin >> a[i];
		ans += a[i];
	}
	sort(a + 1,a + 1 + n);
	for(int i = 1;i <= x;i++){
		ans -= a[n - i + 1] * 2;
	}
	ma = ans;
	for(int i = 1;i <= k;i++){
		ans += a[n - i + 1];
		if(n - i - x + 1 > 0)
			ans -= 2 * a[n - i - x + 1];
		ma = max(ma,ans);
	}
	cout << ma << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

### C. Partitioning the Array

* 给一个长度为n的序列分为 $n/k$ 份，每 $k$ 个一份，如果存在一个数 $d\ (d!=1)$ 使得将所有数对 $d$ 取模后所有的 $n/k$ 份的子序列都相同，那么得一分，问对于所有的 $k$ 来说，总分为多少

##### 思路:

* 首先 $2e5$ 的因子数量在 $100$ 左右，因此枚举因子即可
* 然后要使 $x_1\%d=x_2\%d=...$ ，我们有 $(x_1-x_2)\%d=(x_2-x_3)\%d=...=0$ ，因此取所有的 $abs(x_{n+1}-x_{n})$ 取gcd即可得到最小的 $d$ 取多少
* 复杂度就为 $O(100n)$

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 2e5 + 10;

int a[N];

int gcd(int x,int y){
	if(y == 0) return x;
	else return gcd(y,x % y);
}

void test(){
	int n;cin >> n;
	for(int i = 1;i <= n;i++)
		cin >> a[i];
	int sq = sqrt(n);
	int ans = 0;
	for(int i = 1;i <= sq;i++){
		if(n % i == 0){
			int gc = 0;
			for(int j = 1;j <= i;j++){
				for(int k = 2;k <= n / i;k++){
					gc = gcd(gc,abs(a[(k - 1) * i + j] - a[(k - 2) * i + j]));
				}
			}
			if(gc != 1) ans++;
			if(i * i != n){
				gc = 0;
				for(int j = 1;j <= n / i;j++){
					for(int k = 2;k <= i;k++){
						gc = gcd(gc,abs(a[(k - 1) * n / i + j] - a[(k - 2) * n / i + j]));
					}
				}
				if(gc != 1) ans++;
			}
		}
	}
	cout << ans << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

### D. Array Repetition

* 操作1：给序列加一个数
* 操作2：复制序列k次并接在后面
* 给q个询问，问某个位置是什么

##### 思路：

* 给出的询问在 $1e18$ 范围内，那么之前的复制的操作的次数不超过 $60$ 次，那么根据循环节循环向前找即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const double ep = 1e-8;

struct r{
	ll lenpre;
	int re;
	int vlen;
	bool f;
	vector<int> v;
};

vector<r> a;

int dfs(ll x,int deep){
	if(a[deep].f == 0){
		if(a[deep].lenpre != -1)
			return dfs(((x - 1) % a[deep].lenpre) + 1,deep - 1);
		else
			return dfs(x,deep - 1);
	}
	if(a[deep].lenpre * a[deep].re >= x)
		return dfs(((x - 1) % a[deep].lenpre) + 1,deep - 1);
	else{
		return a[deep].v[x - a[deep].lenpre * a[deep].re - 1];
	}
}

void test(){
	int n,q;cin >> n >> q;
	a.clear();
	r be;
	be = {0,1,0,1,vector<int>(0)};
	int madeep = 0;
	for(int i = 1;i <= n;i++){
		int op;
		ll x;cin >> op >> x;
		if(op == 1){
			be.vlen++;
			be.v.push_back(x);
		}else{
			a.push_back(be);
			madeep++;
			r ne;	
			ne.lenpre = be.lenpre * be.re + be.vlen;
			ne.re = x + 1;
			if(ne.lenpre > 2e18 / ne.re || be.f == 0){
				ne.f = 0;
				if(be.f == 0){
					madeep--;
					ne.lenpre = -1;
				}
			}else
				ne.f = 1;
			ne.vlen = 0;
			ne.v = vector<int>(0);
			be = ne;
		}
	}
	a.push_back(be);
	while(q--){
		ll x;cin >> x;
		cout << dfs(x,madeep) << ' ';
	}
	cout << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```
