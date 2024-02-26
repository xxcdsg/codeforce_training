## [Codeforces Round 920 (Div. 3)](https://codeforces.com/contest/1921)

### A. Square

* 给出边平行于坐标轴的长方形的四个点，问长方形的面积

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int inf = 1e9 + 10;

void test(){
	int dx = inf,dy = inf;
	cin >> dx >> dy;
	bool fx = 0,fy = 0;
	for(int i = 1;i <= 3;i++){
		int x,y;cin >> x >> y;
		if(x != dx && !fx){
			fx = 1;
			dx = abs(dx - x);
		}
		if(y != dy && !fy){
			fy = 1;
			dy = abs(dy - y);
		}
	}
	cout << dx * dy << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

### B. Arranging Cats

* 给出原有的01序列与要成为的01序列，可以进行三种变换，问最小操作次数
* 三种操作分别是，0变1，1变0，01位置交换

##### 思路

* 只有不同的位置要变，也就是原来是1要变成0与原来是0要变成1，然后因为位置可以交换，因此只要再添加或者去掉差值的1即可，也就是要求原来是1要变成0与原来是0要变成1的最大值即可

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
	string s1,s2;cin >> s1 >> s2;
	int num = 0,mi = 0;
	for(int i = 0;i < n;i++){
		if(s1[i] == '1' && s2[i] == '0') mi++;
		if(s2[i] == '1' && s1[i] == '0') num++;
	}
	cout << max(mi,num) << '\n';
}
 
int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

### C. Sending Messages

* 要发送 $n$ 条信息，时间分别为 $m_1,m_2...$
* 开机每时刻都消耗能量 $a$ ，关机后开机需要能量 $b$ ，一开始为开机，问能否发送所有信息
* 能量为0时无法发送信息

##### 思路：

* 每次发送信息时手机必须开机，也就是对每一个信息来说，它的前置条件都相同，那么只要根据这个前置条件取两种开机方法中消耗能量最小的即可

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;
 
#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;
 
const int N = 2e5 + 10;
 
int m[N];
 
void test(){
	int n,f,a,b;cin >> n >> f >> a >> b;
	ll ans = 0;
	m[0] = 0;
	for(int i = 1;i <= n;i++){
		cin >> m[i];
		ans += min((ll)a * (m[i] - m[i - 1]),(ll)b);
	}
	if(ans >= f) cout << "NO\n";
	else cout << "YES\n";
}
 
int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

### D. Very Different Array

* 给出a序列，b序列，在b序列中挑n个数，任意排列，求最大的与a序列的差值的绝对值之和

##### 思路：

* 首先，如果两个序列都确定，那么如果一个序列顺序，另一个为倒序，那么它的差的绝对值之和是所有排列中最大的
* 其次，假设有 $k$ 个数有 $a_i-b_i\leq 0$ ，那么这 $k$ 个数取最大的 $k$ 个一定比任意取法更优，同理如果取小就要取最小的几个
* 因此它的取法一定是最小的取几个，最大的取几个
* 那么我们就枚举最小的数量
* 又因为序列的搭配方式导致每次只会修改其中一个绝对值，因此更改的复杂度仅仅为 $1$

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 2e5 + 10;

int a[N],b[N];

void test(){
	int n,m;cin >> n >> m;
	for(int i = 1;i <= n;i++) cin >> a[i];
	for(int i = 1;i <= m;i++) cin >> b[i];
	sort(a + 1,a + 1 + n);
	sort(b + 1,b + 1 + m);
	ll ans = 0,aans = 0;
	for(int i = 1;i <= n;i++)
		ans += abs(a[i] - b[n - i + 1]);
	aans = ans;
	for(int i = 1;i <= n;i++){
		ans -= abs(a[i] - b[n - i + 1]);
		ans += abs(a[i] - b[m - i + 1]);
		aans = max(aans,ans);
	}
	cout << aans << '\n';
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

### E. Eat the Chip

* $h*w$的棋盘，A先走，B后走，A只能向下或者45°斜着向下走，B只能向上或者45°斜着向上走，问谁会吃掉谁，或者平局，棋子不能移动出棋盘

##### 思路：

* 首先根据棋子 $x$ 的差值的奇偶性可以初步判断谁被谁吃，然后根据一直向左移动与一直向右移动是否会被追上得出是否被吃即可
* 注意移动的次数有可能相同，有可能多 $1$

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

void test(){
	int h,w,xa,ya,xb,yb;cin >> h >> w >> xa >> ya >> xb >> yb;
	if(xa < xb){
		bool ea = 1;
		if((xb - xa) % 2 == 0){
			int mv = (xb - xa) / 2;
			int l1 = max(1,ya - mv),l2 = max(1,yb - mv);
			if(l1 < l2) ea = 0;
			int r1 = min(w,ya + mv),r2 = min(w,yb + mv);
			if(r1 > r2) ea = 0;
			if(ea)
				cout << "Bob\n";
			else
				cout << "Draw\n";
		}else{
			int mv = (xb - xa) / 2;
			int l1 = max(1,yb - mv),l2 = max(1,ya - mv - 1);
			// cout << l1 << ' ' << l2 << '\n';
			if(l1 < l2) ea = 0;
			int r1 = min(w,yb + mv),r2 = min(w,ya + mv + 1);
			if(r1 > r2) ea = 0;
			if(ea)
				cout << "Alice\n";
			else
				cout << "Draw\n";
		}
	}else
		cout << "Draw\n";
}

int main(){
	IOS
	int t = 1;cin >> t;
	while(t--){
		test();
	}
}
```

### F. Sum of Progression

* 求 $2e5$ 次 $a_s+a_{s+d}* 2+...+a_{s+d*(k-1)}*k$

##### 思路：

* 典型分类讨论使得最大复杂度小于一个阈值，经典的 $x+n/x$ 复杂度为 $\sqrt{n}$
* 对于 $d$ 较大时，取的数的数量较少，那么我们就可以直接求和
* 对于 $d$ 较小时，我们可以根据 $sum_{xd}=a_x+a_{x-d}+...a_y(y < d)$ 与 $asum_{xd}=k*a_x+(k-1)*a_{x-d}+...a_y(y < d)$
* 而 $sum_{xd}$ 与 $asum_{xd}$ 对于每个 $d$ 来说都需要遍历一次数组，如果我们求出前 $s$ 个 $d$ 的 $sum_{xd}$ 与 $asum_{xd}$ 后面的直接求那么复杂度就为 $O(s+n/s)$ ，那么取 $s=\sqrt n$ ，那么复杂度就为 $\sqrt n$ ，乘上 $q$ 为 $q\sqrt n$ ， $n\sqrt n$ 的复杂度

```cpp
// xxc
#include<bits/stdc++.h>
using namespace std;

#define endl '\n'
#define IOS ios::sync_with_stdio(false);cin.tie(nullptr);cout.tie(nullptr);
#define ll long long
#define debug(x) cout << #x << ' ' << x << endl;

const int N = 1e5 + 10;

int a[N];

ll sum[350][N];
ll asum[350][N];

void test(){
	int n,q;cin >> n >> q;
	for(int i = 1;i <= n;i++) cin >> a[i];
	int mid = sqrt(n);
	for(int i = 1;i <= mid;i++){
		for(int j = 1;j <= n;j++){
			if(j < i){
				sum[i][j] = a[j];
				asum[i][j] = a[j];
			}else{
				sum[i][j] = sum[i][j - i] + a[j];
				asum[i][j] = asum[i][j - i] + (ll)a[j] * ((j - 1) / i + 1);
			}
		}
	}
	while(q--){
		int s,d,k;cin >> s >> d >> k;
		if(d <= mid){
			int aim = s + (k - 1) * d;
			int count = (s - 1) / d;
			s -= d;
			s = max(s,0);
			ll ans = asum[d][aim] - asum[d][s];
			ans -= (ll)count * (sum[d][aim] - sum[d][s]);
			cout << ans << ' ';
		}else{
			ll ans = 0;
			int mul = 1;
			while(mul <= k){
				ans += (ll)mul * a[s];
				mul++;
				s += d;
			}
			cout << ans << ' ';
		}
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

