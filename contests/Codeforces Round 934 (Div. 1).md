# [Codeforces Round 934 (Div. 1)](https://codeforces.com/contest/1943)

## [A.MEX Game 1](https://codeforces.com/contest/1943/problem/A)

* 两个人博弈，第一个人从 $a$ 中挑一个数放到 $b$ 中，第二个人从 $a$ 中挑一个数丢掉
* 第一个人先走，第二个人后动
* 第一个人目的是让 $b$ 中的 $MEX$ 最大，第二个人相反
* 问最后 $b$ 中的 $MEX$ 为多少

##### 思路：

* 如果第一个人的目的为 $x$ ，那么如果 $a$ 中存在一个数它只有一个并且小于 $x$ ，那么它肯定先选，不然就被后一个人截胡了
* 然后后一个人选，那么如果它选了一个不止一个的数，那么前一个人在下一回合就可以选和它相同的数，来使得后一个人的操作无效
* 因此我们得出， $MEX$ 最大值满足，前面的数的数量都大于 $2$ ，或者至多有一个 $1$

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

const int N = 2e5 + 10;

int num[N];

void test(){
	int n;cin >> n;
	for(int i = 0;i <= n;i++) num[i] = 0;
	for(int i = 1;i <= n;i++){
		int x;cin >> x;
		num[x]++;
	}
	bool f = 0;
	for(int i = 0;i <= n;i++){
		if(num[i] == 0 || (num[i] == 1 && f)){
			cout << i << '\n';
			return;
		}else if(num[i] == 1)
			f = 1;
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

## [B. Non-Palindromic Substring](https://codeforces.com/contest/1943/problem/B)

* 称一个字符串为 $k-good$ ，即这个字符串存在一个长度为 $k$ 的连续子序列，它不为回文串
* $f(t)$ ,就是 $\sum_{k=1}^{|t|}k,t-k-good$
* 问一个字符串 $s$ 其中一个区间 $f(s[l]s[l+1]...s[r])$

##### 思路：

* 如果一个字符串，它的所有长度为 $k$ 的连续子序列都为回文串
* 如果 $k$ 为偶数，那么有

$$
s[l]s[l+1]...s[l+k-1]为回文串\\
$$

$$
s[l+1]s[l+2]...s[l+k]为回文串\\
$$

$$
\to\\
$$

$$
s[l]=s[l+1]=...s[l+k]\\
$$

$$
从中间推理\\
$$

$$
s[l]...s[k/2]s[k/2+1]...s[r]\\
$$

$$
s[l+1]...s[k/2+1]s[k/2+2]...s[r+1]\\
$$

$$
\to\\
$$

$$
s[k/2]=s[k/2+1]=s[k/2+2]=...
$$

* 因此，对于所有 $k$ 为偶数来说，只要所有字符都相同，那么它就满足所有偶数都为回文串
* 对于 $k$ 为奇数来说，它就只能两个两个跳，因此只要奇数位与偶数位各自都完全相同，那么都为回文串
* 也就是说只要看两个条件即可判断所有的小于长度的 $k$
* 最后 $k$ 为长度的特殊情况，我们用哈希，马拉车均可

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

vector<int> manachar(string s){
	string tem = "#";
	for(auto c : s){
		tem += c;
		tem += '#';
	}
	int len = tem.size();
	vector<int> res(len);
	for(int t = 1,r = 0,mid = 0;t < len;t++){
		if(t <= r) res[t] = min(res[(mid << 1) - t],r-t+1);
		while(tem[t-res[t]] == tem[t+res[t]]) res[t]++;
		if(res[t]+t>r) r = res[t]+t-1,mid=t;
	}
	return res;
}

void test(){
	int n,q;cin >> n >> q;
	string s;cin >> s;
	auto bd = manachar(s);
	vector<int> f1(n + 1),f2(n + 1);
	f1[n] = f2[n] = n;
	for(int i = n - 1;i >= 0;i--){
		if(i - 1 >= 0 && s[i] != s[i - 1]) f1[i] = i;
		else f1[i] = f1[i + 1];
		if(i - 2 >= 0 && s[i] != s[i - 2]) f2[i] = i;
		else f2[i] = f2[i + 1];
	}
	// for(auto x : bd)
	// 	cout << x << ' ';
	while(q--){
		int l,r;cin >> l >> r;
		l--;
		r--;
		int len = (r - l + 1);
		ll ans = 0;
		if(l + 1 <= r && f1[l + 1] <= r){
			ll num = len / 2 - (len % 2 == 0 ? 1 : 0);
			ans += (num * 2 + 2) * num / 2;
		}
		if(l + 2 <= r && f2[l + 2] <= r){
			ll num = len / 2 - 1;
			ans += (num * 2 + 4) * num / 2;
		}
		if(bd[l + r + 1] / 2 * 2 - (bd[l + r + 1] - 1) % 2 < len){
			ans += len;
		}
		cout << ans << '\n';
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

## [C - Tree Compass](https://codeforces.com/contest/1943/problem/C)

* 给一颗树，每一次操作可选择一个点与一个距离，将所有与这个点相隔该距离的点涂黑，问最少操作几次

##### 思路：

* 首先，我们想到最长链（直径）的中点，如果最长链为奇数长度，那么一直选中点需要 $(D+1)/2$ 的操作次数
* 证明正确性：我们每次操作，最多会在链上取两个点涂黑，那么最少需要 $(D+1)/2$ 的次数才能涂满，而中点到最远点的距离为 $(D-1)/2$ ,加上涂自己的0后，正好需要 $(D+1)/2$ 的操作次数就可以涂满
* 那么如果不是奇数怎么办
* 如果我们还是选中点，那么有两个中点，如果随便选一个，那么操作次数为 $D/2+1$
* 考虑到每次在链上最多可填两个点，因此有可能最优解为 $D/2$
* 假设就只有一条链，那么当 $D$ 为 $4$ 的倍数时最优解为 $D/2$
* 两个点可同时选择只有可能是它们距离为偶数时，也就是这两个点的奇偶性相同时，那么如果 $D$ 不为 $4$ 的倍数，那么奇数点的数量与偶数点的数量均为奇数，那么就无法完全配成，因此按奇数的方法处理即可
* 当 $D$ 为 $4$ 的倍数时，最优解为 $D/2$ ，那么我们取链中间的两个点开涂，构造可得我们取 $1,3,5...$，涂满即可

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


const int MAXN = 2e3 + 10;
struct edge{
	int v,dis;
	edge* nex;
}ed[MAXN * 2];
edge* head[MAXN];
int ptop = 0,ans = 0;
bool use[MAXN];
struct pos{
	int x;
	int dis;
	bool operator <(const pos &x)const{
		return dis > x.dis;
	}
};
priority_queue<pos> qu;

int fa[MAXN];

int bfs(int u){
	pos tem;
	tem.x = u,tem.dis = 0;
	qu.push(tem);
	int x;
	while(!qu.empty()){
		x = qu.top().x;
		int pre = qu.top().dis;
		ans = pre;
		qu.pop();
		edge* p = head[x];
		while(p != NULL){
			int v = p -> v,dis = p -> dis;
			p = p -> nex;
			if(!use[v]){
				fa[v] = x;
				use[v] = 1;
				pos tem;
				tem.x = v,tem.dis = dis + pre;
				qu.push(tem);
			}
		}
	}
	return x;
}
void add(int u,int v,int dis){
	ed[ptop].v = v;
	ed[ptop].dis = dis;
	ed[ptop].nex = head[u];
	head[u] = &ed[ptop];
	ptop++;
}

void test(){
	int n;cin >> n;

	for(int i = 1;i <= n;i++){
		head[i] = NULL;
	}
	ptop = 0;

	int u = 1,v,dis;
	for(int i = 1;i < n;i++){
		dis = 1;cin >> u >> v;
		add(u,v,dis);
		add(v,u,dis);
	}

	use[u] = 1;
	for(int i = 1;i <= n;i++)
		use[i] = 0;
	use[u] = 1;
	u = bfs(u);
	for(int i = 1;i <= n;i++)
		use[i] = 0;
	use[u] = 1;
	u = bfs(u);
	int root = u;

	ans++;
	if(ans % 4 == 0){
		int rt = root;
		cout << ans / 2 << '\n';
		for(int i = 1;i <= ans / 2 - 1;i++)
			rt = fa[rt];
		for(int i = 1;i <= ans / 4;i++){
			cout << rt << ' ' << i * 2 - 1 << '\n';
			cout << fa[rt] << ' ' << i * 2 - 1 << '\n';
		}
	}else{
		cout << ans / 2 + 1 << '\n';
		int num = ans / 2;
		for(int i = 1;i <= num;i++)
			root = fa[root];
		for(int i = 0;i <= num;i++)
			cout << root << ' ' << i << '\n';
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

* 代码简化版

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
	vector<vector<int>> ed(n + 1);
	for(int i = 1;i < n;i++){
		int u,v;cin >> u >> v;
		ed[u].push_back(v);
		ed[v].push_back(u);
	}
	vector<int> fa(n + 1);
	auto find = [&](int s){
		for(int & x : fa) x = 0;
		queue<int> qu;
		qu.push(s);
		int x;
		while(!qu.empty()){
			x = qu.front();
			qu.pop();
			for(int y : ed[x]){
				if(fa[x] == y) continue;
				else{
					fa[y] = x;
					qu.push(y);
				}
			}
		}
		return x;
	};
	int u = find(1);
	int v = find(u);
	vector<int> lis;
	int len = 1;
	lis.push_back(v);
	while(u != v){
		v = fa[v];
		len++;
		lis.push_back(v);
	}
	int mid = (len - 1) / 2;
	if(len % 4 == 0){
		cout << len / 2 << '\n';
		for(int i = 0;i < len / 4;i++){
			cout << lis[mid] << ' ' << i * 2 + 1 << '\n';
			cout << lis[mid + 1] << ' ' << i * 2 + 1 << '\n';
		}
	}else{
		cout << len / 2 + 1 << '\n';
		for(int i = 0;i < len / 2 + 1;i++)
			cout << lis[mid] << ' ' << i << '\n';
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

## [D1. Counting Is Fun (Easy Version)](https://codeforces.com/contest/1943/problem/D1)

* 给一个数组，每次可选择一个区间 $[l,r],(l\neq r)$ ，减去相同的数
* 称能够通过上述操作使得数组每一个数均变为0的数组为好数组，问长度为 $n$ ,元素最大不超过 $k$ 的 $(k+1)^n$ 个数组中有多少个好数组

##### 思路：

* 首先，我们要满足 $a[i]\leq a[i+1]+a[i-1]$ ,不然 $a[i]$ 肯定不能变为 $0$
* 只需要满足上面那条条件，这个数组就为好数组，其中 $a[0]=0,a[n+1]=0$，那么我们就可以递推出该数组为好数组
* 然后我们假设 $dp[a][b]$,其中$a$为上上个数，$b$为上个数，那么它的转移有

$$
dp[a][b]=\sum dp[c][a],b\geq a-c
$$

* 我们把 $b-a$ 作为一个参量 $c$ ，丢掉参量  $a$

$$
dp[b'][c']=\sum dp[b][c],b'\geq c,b'-b=c'
$$

* 我们对 $dp[b]$ 再求一个前缀和 $res$

$$
dp[b'][c']=res[b][b']
$$

* 那么因为 $res$ 是 $dp$ 的前缀和模式，因此可以把 $dp$ 数组丢掉
* 因为假设 $a[0]=a[n+1]=0$ ,因此在初始化时要 $res[0][0][0\to k]=1$
* 因为参量 $c$ 有可能为负数，因此统一 $+k$ 处理

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
	int n,k,p;cin >> n >> k >> p;
	vector<vector<vector<int>>> res(2,vector<vector<int>>(k+1,vector<int>(2*k+1,0)));
	for(int i = 0;i <= k;i++)
		res[0][0][i + k] = 1;
	for(int i = 1;i <= n;i++){
		for(int a = 0;a <= k;a++)
			for(int b = 0;b <= k;b++){
				res[1][b][b - a + k] = res[0][a][b + k];
			}
		for(int j = 1;j <= 2*k;j++)
			for(int a = 0;a <= k;a++)
				res[1][a][j] = (res[1][a][j] + res[1][a][j - 1]) % p;
		res[0] = res[1];
		res[1] = vector<vector<int>>(k+1,vector<int>(2*k+1,0));
	}
	int ans = 0;
	for(int a = 0;a <= k;a++)
		ans = (ans + res[0][a][k]) % p;
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

