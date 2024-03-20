# [Codeforces Round 935 (Div. 3) F G H](https://codeforces.com/contest/1945)

## [F. Kirill and Mushrooms](https://codeforces.com/contest/1945/problem/F)

* 选 $k$ 个蘑菇，那么得到的药效果为 $k*min\{a_{i_1},a_{i_2},a_{i_3}...a_{i_k}\}$，同时，给出一个排列 $p$ ，当选了 $k$ 个蘑菇时， $a[p_1],a[p_2]...a[p_{k-1}]=0$，问效果的最大值为多少，如有多个可行值，选最小的 $i$ 的方法

##### 思路：

* 先对 $a$ 排序，然后我们要取到最大的最小值才能得到最大的效果，那么我们从后面开始取，并且枚举 $k$
* 因为每次都加一个蘑菇，因此取的最大的最小值要向前跑一位
* 同时，如果 $p[k-1]$ 如果在后面的范围内，说明在上次的取中，后面有一个正常的数变成了 $0$ ，我们得找一个正常的数填上去，那么我们还需要跑一位

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
	vector<int> v(n + 1),p(n + 1);
	vector<pair<int,int>> vv(n + 1);
	for(int i = 1;i <= n;i++) cin >> v[i];
	for(int i = 1;i <= n;i++) cin >> p[i];
	for(int i = 1;i <= n;i++){
		vv[i] = {v[p[i]],i};
	}
	sort(vv.begin() + 1,vv.end());
	vector<bool> vis(n + 1);
	pair<ll,int> ans = {0,0};
	int no = n;
	for(int k = 1;no >= 1;k++){
		
		int num = 1;
		if(vis[k - 1]) num++;
		for(int i = 1;i <= num;i++){
			while(no >= 1 && vv[no].second < k){
				vis[vv[no].second] = 1;
				no--;
			}
			if(i == 1 && num == 2){
				vis[vv[no].second] = 1;
				no--;
			}
		}

		if(no < 1) break;
		else{
			if((ll)vv[no].first * k > ans.first)
				ans = {(ll)vv[no].first * k,k};
		}
		vis[vv[no].second] = 1;
		no--;
		
	}
	cout << ans.first << ' ' << ans.second << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

## [G. Cook and Porridge](https://codeforces.com/contest/1945/problem/G)

*  $n$ 个人，总时间为 $D$ ，每次选排在队头的人吃粥
* 每个人有优先级 $k[i]$ 与吃粥的时间 $s[i]$
* 如果一个人在 $x$ 时刻开始吃粥，那么它将在 $x+s[i]$ 结束加入队列
* 一个人加入队列，它会插从队尾数所有比它优先级小的人的队，也就是它会排在最后面与它优先级相同或者更大的人后面

##### 思路；

*  $D \leq 3e5$ ，如果我们能在 $log$  级别维护队列，那么 $nlog_2n$ 的时间就能通过此题
* 首先，我们创建一个初始队列 $qu$ ，它内部就是 $1...n$
* 因为新加入的人从后面根据 $k$ 来插入队列，那么我们就求一个 $k$ 的后缀最大值 $sufk$
* 除了原本的队列，我们创建一个新的根据 $k$ 与插入时间来排序的优先队列 $ppqu$ 来模拟新加入队列的人的排序
* 无论原本的队列然后与新的人如何排列，新的人的排列肯定是按照它本身的顺序，不会因为原序列的顺序而不同
* 同时，我们还需要给正在吃粥的人创建一个优先队列 $pqu$ ，根据时间与 $s[i]$ 来排序
* 那么给出三个队列的结构

$$
qu(int\ i,int\ sufk),pqu(int\ time,int\ s[i],int\ i),ppqu(int\ k[i],int\ t,int\ i)
$$

* 三个队列相互转移即可

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
	int n,d;cin >> n >> d;
	vector<int> k(n + 1),s(n + 1);
	int ptop = 0;
	map<int,int> mp;
	for(int i = 1;i <= n;i++){
		cin >> k[i] >> s[i];
		mp[k[i]] = 1;
	}
	for(auto [x,y] : mp)
		mp[x] = ++ptop;
	for(int i = 1;i <= n;i++){
		k[i] = n - mp[k[i]] + 1;
	}
	queue<pair<int,int>> qu;
	vector<int> suf(n + 2,inf);
	for(int i = n;i >= 1;i--)
		suf[i] = min(suf[i + 1],k[i]);
	for(int i = 1;i <= n;i++){
		qu.push({i,suf[i]});
	}
	priority_queue<tuple<int,int,int>,vector<tuple<int,int,int>>,greater<tuple<int,int,int>>> pqu;
	priority_queue<tuple<int,int,int>,vector<tuple<int,int,int>>,greater<tuple<int,int,int>>> ppqu;
	ptop = 0;
	for(int i = 1;i <= d;i++){

		while(!pqu.empty()){
			auto [tim,si,_i] = pqu.top();
			if(tim <= i){
				ppqu.push(tuple<int,int,int>(k[_i],++ptop,_i));
				pqu.pop();
			}else
				break;
		}

		bool f = 0;
		if(ppqu.empty()){
			f = 1;
		}else{
			auto [ki,t,_i] = ppqu.top();
			auto [__i,sufk] = qu.front();
			if(sufk <= ki)
				f = 1;
		}

		if(f){
			auto [__i,sufk] = qu.front();
			qu.pop();
			pqu.push(tuple<int,int,int>(i + s[__i] + 1,s[__i],__i));
		}else{
			auto [ki,t,_i] = ppqu.top();
			ppqu.pop();
			pqu.push(tuple<int,int,int>(i + s[_i] + 1,s[_i],_i));
		}

		if(qu.empty()){
			cout << i << '\n';
			return;
		}
	}
	cout << -1 << '\n';
}

//test2

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```



## [H. GCD is Greater](https://codeforces.com/contest/1945/problem/H)

* 从数列中选 $[2,n-2]$ 个数，问能否使得选出的数的 $gcd$ 大于剩下的数的 $ANDsum + x$

##### 思路：

* 首先，无论是 $gcd$ 还是 $ANDsum$ 都是数越多，值越小，那么为了让前者更大，后者更小，那么我们就选两个数 $gcd$ 即可
* 然后我们思考 $AND$ 的性质，如果某个数的二进制位为0，那么这个二进制位就是0，因为我们只选两个，因此如果有三个及以上的数某个二进制位不存在，那么这个二进制位一定为0
* 我们将那些有二进制位，但该二进制位对应的数小于等于2的先不选（放到 $other$ 数组中），那么剩下的数 $ANDsum$ 就是固定的，就是所有数的 $ANDsum$ ，确定 $ANDsum$ ，那么我们只有找出最大的 $gcd$ 即可，我们分解因子加 $map$ 来存因数的方式找最大 $gcd$ ，对于一个因数来说，那么记录一个数即可
* 对于 $other$ 与正常的数的组合，我们枚举 $other$ 中的数，因为正常的数不会影响 $ANDsum$ 因此我们只要考虑 $other$ 中的那个数是否是唯一一个没有某个二进制的数，那么 $ANDsum$ 就要或上那个二进制数，然后同上找最大的 $gcd$
* 对于 $other$ 内部，我们还要考虑两个数恰好没有且仅仅这两个数没有某个二进制位，因为 $other$ 数组长度较小，因此暴力枚举即可

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

int gcd(int x,int y){
	if(y == 0) return x;
	else return gcd(y,x % y);
}

void test(){
	int n,x;cin >> n >> x;
	vector<int> a(n);
	vector<vector<int>> none(20);//没有这位的数
	vector<int> count(20,0);
	map<int,int> mp;//因子与数
	
	int ptop = 0;
	for(int &_ : a){
		cin >> _;
		for(int i = 0;i <= 19;i++){
			if((_&(1<<i))==0){
				none[i].push_back(ptop);
				count[i]++;
			}
		}
		ptop++;
	}

	int no = 0;

	set<int> se;//不取的数
	for(int i = 0;i <= 19;i++){
		if(count[i] <= 2){//先把count[i]小于等于2的标记不取
			if(count[i] == 2){
				if(a[none[i][0]] < a[none[i][1]])
					swap(none[i][0],none[i][1]);
			}
			for(int _ : none[i])
				se.insert(a[_]);
		}else
			no |= (1 << i);
	}

	pair<int,int> ma = {0,0};

	vector<int> other;

	for(int _ : a){
		if(se.find(_) != se.end()){//可加入
			int sq = sqrt(_);
			for(int i = 1;i <= sq;i++){
				if(_ % i != 0) continue;
				if(mp.count(i) && i > gcd(ma.first,ma.second)){
					ma = {_,mp[i]};
				}else
					mp[i] = _;
				if(i*i != _){
					int j = _ / i;
					if(mp.count(j) && j > gcd(ma.first,ma.second)){
						ma = {_,mp[j]};
					}else
						mp[j] = _;
				}
			}
		}else{
			other.push_back(_);
		}
	}

	bool f = 0;

	if(gcd(ma.first,ma.second) > no + x){//初步统计
		f = 1;
	}else{
		//normal与other
		for(auto _ : other){
			int sq = sqrt(_);
			int magc = 0;
			int nno = no;
			for(int i = 0;i <= 19;i++){
				if(count[i] == 1 && a[none[i][0]] == _)
					nno |= 1 << i;
			}
			for(int i = 1;i <= sq;i++){
				if(_ % i != 0) continue;
				if(mp.count(i)) magc = max(magc,i);
				if(mp.count(_ / i)) magc = max(magc,_ / i);
			}
			if(magc > nno + x){
				f = 1;
				ma = {_,mp[magc]};
			}
		}
		//other与other
		int len = other.size();
		for(int i = 0;i < len;i++)
			for(int j = i + 1;j < len;j++){
				int _ = other[i],__ = other[j];
				int nno = no;
				if(_ < __) swap(_,__);
				for(int k = 0;k <= 19;k++){
					if(count[k] == 2 && _ == a[none[k][0]] && __ == a[none[k][1]]) nno |= 1 << k;
					if(count[k] == 1 && a[none[k][0]] == _)
						nno |= 1 << k;
					if(count[k] == 1 && a[none[k][0]] == __)
						nno |= 1 << k;
				}
				if(gcd(_,__) > nno + x){
					ma = {_,__};
					f = 1;
				}
			}
	}
	if(f){
		cout << "Yes\n";
		cout << 2 << ' ' << ma.first << ' ' << ma.second << '\n';
		cout << n - 2 << ' ';
		for(auto _ : a){
			if(_ == ma.first){
				ma.first = 0;
				continue;
			}
			if(_ == ma.second){
				ma.second = 0;
				continue;
			}
			cout << _ << ' ';
		}
		cout << '\n';
	}else
		cout << -1 << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

