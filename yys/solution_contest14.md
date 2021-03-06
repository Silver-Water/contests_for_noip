[比赛链接](https://cn.vjudge.net/contest/168929)

# 比赛中解决的问题
## A
### 题意
> 给你四个整数a,b,c,d,如果存在一组整数解x,y使得b+ax=d+cx，则输出b+ax的值，否则输出-1.

### 数据范围：a,b,c,d<=100  时间限制:1s

### 题解
> 数据范围很小，暴力模拟即可，同时设一个变量cnt记录操作次数，如果cnt达到一定值（这个值要足够大）还没有出解则说明无解。

### 代码
```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
	int a,b,c,d;
	scanf("%d%d",&a,&b);
	scanf("%d%d",&c,&d);
	int cnt=0;
	while(b!=d){
		cnt++;
		if(b<d)b+=a;
		else d+=c;
		if(cnt>10000){
			printf("-1");return 0;
		}
	}
	printf("%d",b);
	return 0;
}
```
*****
## B
### 题意
> 给你两个数n,m，接下来m行每行第一个数k[i]，接下来k个数a[i][1],a[i][2]...a[i][k],有正有负，1<=|a[i][j]|<=n。如果对于任意一个i(i<=i<=m),至少有一对整数x,y满足a[i][x]=-a[i][y],则输出NO，否则输出YES。

### 数据范围：1<=n,m<=10^4 时间限制:2s

### 题解
> 逐行判断，开两个bool数组pos,neg分别表示正数和负数的出现情况，随后遍历1到n，如果存在x使得pos[x]&&neg[x]则跳过本行，否则直接输出YES并return 0。最后如果中途没有退出，则输出NO。

### 代码
```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
	int n,m;
	bool pos[10005],neg[10005];
	scanf("%d%d",&n,&m);
	for(int i=1;i<=m;i++){
		memset(pos,0,sizeof(pos));
		memset(neg,0,sizeof(neg));
		int k;scanf("%d",&k);
		if(k==1){printf("YES\n");return 0;}
		for(int j=1;j<=k;j++){
			int x;scanf("%d",&x);
		    if(x>0)pos[x]=1;
		    if(x<0)neg[-x]=1;
		}
		bool flag=0;
		for(int j=1;j<=n;j++)
		if(pos[j]&&neg[j]){flag=1;break;}
		if(!flag){printf("YES\n");return 0;}
	}
	printf("NO\n");return 0;
}
```

# 赛后补题
## C
### 题意
> 两个人在玩游戏，每人有一个整数集，包含k[i]个数，保证每个数均为小于n的正整数。游戏地图是一个长度为n的环，位置1为黑洞。有一个怪物，其位置未知，但一定在[2,n]范围内。游戏规则为：两人交替行动，每次行动可以从自己的数集中任选一个数a[i,j]，使得怪物向前走a[i,j]步。谁操作之后使得怪物到达1的位置，谁就胜利了。为两个人分别输出，当怪物初始位置为2,3,……,n时，如果该玩家先手，是必胜还是必败，还是游戏会陷入无限循环。

### 数据范围：2<=n<=7000  时间限制:4s

### 题解
> 首先将环前移一位，使得黑洞位置变成0，方便处理。开一个数组f[now][x]表示轮到玩家now操作，怪物位置为x时，该玩家的胜负情况（1为必胜，-1为必败，0为循环）。对于第i个玩家，显然有f[i][n-a[i,j]]=1(1<=j<=k[i])。之后用dfs实现dp，算出答案。

### 代码
```cpp
#include<bits/stdc++.h>
using namespace std;
int n,k[2],s[2][7005];
int f[2][7005],vis[2][7005];
void dfs(int x,int now)
{
	if(f[now][x])return;
	f[now][x]=1;
	if(x==0) return;
	for(int i=0;i<k[!now];i++) 
	{
		int d=(x+n-s[!now][i])%n;
		if(++vis[!now][d]==k[!now])
	    {
	    	f[!now][d]=-1;
	    	for (int j=0;j<k[now];j++) 
	    	dfs((d+n-s[now][j])%n,now);
		}
	}
}
int main()
{
	scanf("%d",&n);
	scanf("%d",&k[0]); for (int i=0; i<k[0]; i++) scanf("%d",&s[0][i]);
	scanf("%d",&k[1]); for (int i=0; i<k[1]; i++) scanf("%d",&s[1][i]);
	for (int i=0;i<=1;i++) 
		for(int j=0;j<k[i];j++) dfs(n-s[i][j],i);
	for (int i=0;i<2;i++)
	{
		for (int j=1;j<n;j++) 
		{
			if(f[i][j]==1)printf("Win ");
			if(f[i][j]==0)printf("Loop ");
			if(f[i][j]==-1)printf("Lose ");
		}
		printf("\n");
	}
	return 0;
} 
```
