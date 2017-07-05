# 比赛中解决的问题
## A
### 题意
> 给你一个长度为N的正整数序列，如果一个连续的子序列，子序列的和能够被K整除，那么就视此子序列合法。求原序列包括多少个合法的连续子序列。

### 数据范围
##### 多组数据，数据组数T<=20 
##### 1<=N<=5*10^4,1<=k<=10^6,1<=a[i]<=10^9

### 题解
> 首先开一个数组sum，用sum[i]表示前i个数之和对k的模。显然如果sum[i]=x且sum[j]=x(i<j)，则第i+1到第j个数之和能被k整除。因此我们可以再开一个数组f，用f[i]表示到目前为止，有多少个x满足sum[x]=i。然后一重循环扫过去，每次ans+=f[sum[i]],f[sum[i]]++。最后输出ans。

### 代码
```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<algorithm>
using namespace std;
const int maxn=50005,maxm=1000005;
int T,a[maxn],sum[maxn];
long long f[maxm];
int main(){
	scanf("%d",&T);
	while(T--)
	{
		int n,k;
		scanf("%d%d",&k,&n);
		memset(f,0,sizeof(f));
		sum[0]=0;
		long long ans=0;
		for(int i=1;i<=n;i++){
			scanf("%d",&a[i]);
			sum[i]=(sum[i-1]+a[i])%k;
			if(sum[i]==0)ans++;
			ans+=f[sum[i]];
			f[sum[i]]++;
		}
		printf("%d\n",ans);
	}
	return 0;
}
```
*****

## B
### 题意
> 在一个二维世界（只有宽度和高度，可看做平面直角坐标系）中，有一个人坐在船上钓鱼。船一开始的坐标记为(Ax,y)。河深为y，河宽为x。某个时刻会从左边界或右边界游出来一条鱼(左边的往右边游，右边的往左边游)，即鱼游出来时的横坐标为0或x，这条鱼每秒会游D个单位长度，鱼的长度为L。初始时刻为0，对于每个时刻x，船可以选择花费1s向左或向右移动最多Q个单位长度，或者选择在当前位置进行钓鱼，钓鱼的动作是瞬间的，且发生在时刻x，鱼还来不及移动就被钓上了。如果选择钓鱼，那么在时刻x就不能动。（x+1时刻可以选择移动）设当前位置为z，将鱼看成一条线段，当线段与直线x=z相交时就认为鱼上钩了，所以一次钓鱼动作可能会钓多条鱼。求出T秒后最多能钓多少鱼。

### 数据范围
#### 对于100%的数据：
##### 1<=T,time<=10
##### 1<=Ax,Ay,Q,x,y,D,L<=10
##### 1<=N<=14
#### 对于30%的数据：
##### 1<=n<=5

### 题解
> 一道很明显的状压dp题。我们用一个三维数组f[i][j][k]表示在i时刻，船在j的位置，抓到鱼的情况为k（用二进制的方法储存）是否可以达到。dp求出答案，之后对于每一对i,j使得f[T][i][j]=1，二进制拆分j算出该情况下钓到鱼的数量，取最大值。

### 代码
```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<algorithm>
using namespace std;
int T,maxx,maxy,Ax,q,n;
struct fish{
	int L,R;
}a[20][20];
int f[20][20][50000],can[20][20][50000];
bool used(int sum,int num){
	int tmp=sum;
	for(int i=1;i<=num;i++)tmp>>=1;
	return tmp&1;
}
int getans(int sum){
	int tmp=sum,cnt=0;
	while(tmp>0){
		cnt+=(tmp&1);
		tmp>>=1;
	}
	return cnt;
}
int main(){
	scanf("%d",&T);
	scanf("%d%d",&maxx,&maxy);
	scanf("%d%d",&Ax,&q);
	scanf("%d",&n);
    for(int i=0;i<n;i++){
    	int x,y,d,l,t;
    	scanf("%d%d%d%d%d",&x,&y,&d,&l,&t);
    	for(int j=0;j<t;j++)a[i][j].L=maxx+1,a[i][j].R=-1;
    	if(x==maxx){
    		a[i][t].L=a[i][t].R=maxx;
    		for(int j=t+1;j<=T;j++)
    		{
    			a[i][j].L=a[i][j-1].L-d;
    			a[i][j].R=min(a[i][j].L+l,maxx);
    			a[i][j].L=max(a[i][j].L,0);
			}
		}else{
			a[i][t].L=a[i][t].R=0;
    		for(int j=t+1;j<=T;j++)
    		{
    			a[i][j].R=a[i][j-1].R+d;
    			a[i][j].L=max(a[i][j].R-l,0);
    			a[i][j].R=min(a[i][j].R,maxx);
			}
		}
	}
	memset(f,0,sizeof(f));
	f[0][Ax][0]=1;
	for(int k=0;k<=T;k++)
	for(int i=0;i<=maxx;i++){
		for(int j=(1<<n)-1;j>=0;j--){
			if(!f[k][i][j])continue;
			for(int l=-q;l<=q;l++)
			if(i+l>=0&&i+l<=maxx)f[k+1][i+l][j]=1;
			int s=0;
			for(int l=0;l<n;l++)
			if(i>=a[l][k].L&&i<=a[l][k].R&&!used(j,l))s+=(1<<l);
			f[k+1][i][j+s]=1;
		}
	}
	int ans=0;
	for(int i=0;i<=maxx;i++)
	for(int j=0;j<=(1<<n)-1;j++)
	if(f[T+1][i][j])ans=max(ans,getans(j));
	printf("%d\n",ans);
	return 0;
}
```
******

## D
### 题意
> 已知用不同的手指打字，会增加不同的疲劳度，拇指、食指、中指、无名指、小指每次打字增加的疲劳度分别为a,b,c,d,e。现在给你一个长度为n的字符串，请你重新排列键盘按键，使得打出这个字符串所需的疲劳度最小，输出这个最小值。

### 数据范围
#### 对于100%的数据：
##### 0<=a,b,c,d,e<=100
##### 1<=n<=1000000
#### 对于30%的数据：
##### 1<=n<=100

### 题解
> 贪心。首先将五个手指按照疲劳度从小到大排序，然后对于输入的字符串，统计每个字母出现次数，并从大到小排序。随后尽可能将出现次数多的字母存放到疲劳度小的字母下，算出疲劳度并加到ans中。

### 代码
```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<algorithm>
using namespace std;
int n,sum[128];
char s[1000000];
struct hehe{
	int cnt,v;
}a[5];
bool cmp(hehe x,hehe y){
	return x.v<y.v;
}
bool cmp2(int x,int y){
	return x>y;
}
int main(){
	a[0].cnt=1;a[1].cnt=16;a[2].cnt=8;a[3].cnt=8;a[4].cnt=15;
	for(int i=0;i<5;i++)scanf("%d",&a[i].v);
	sort(a,a+5,cmp);
	scanf("%d",&n);
	char c;
	scanf("%c",&c);
	for(int i=0;i<n;i++){
	    scanf("%c",&s[i]);
	    sum[s[i]]++;
    }
	sort(sum,sum+128,cmp2);
	int ans=0,i=0;
	for(int k=0;k<=4;k++){
		while(a[k].cnt--)ans+=(sum[i]*a[k].v),i++;
		if(sum[i]==0)break;
	}
	printf("%d\n",ans);
	return 0;
}
```
****

# 赛后补题（稍后更新）
