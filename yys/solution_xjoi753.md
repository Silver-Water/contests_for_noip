# 比赛中解决的问题
## A
### 题意
> 一个平面被n条直线分割，这些直线相交于m个点，并且没有三条直线相交于同一个点。求出平面被分成了多少个部分。

### 数据范围
##### 0<=n,m<=10^10000
##### 时间限制：1s

### 题解
> 我们知道，互不相交的n条直线可以把平面分成n+1个部分。一条直线每与另一直线相交，平面就会多分割出一块。由此我们可以推出答案就是n+m+1。（由于数据范围很大，需要写高精度）

### 代码
```cpp
#include<bits/stdc++.h>
#define maxlen 10050
using namespace std;
char N[maxlen],M[maxlen];
int n[maxlen],m[maxlen],ans[maxlen];
int main(){
	scanf("%s%s",N,M);
	int p=strlen(N);
	for(int i=p-1;i>=0;i--)n[p-i]=N[i]-'0';
	int q=strlen(M);
	for(int i=q-1;i>=0;i--)m[q-i]=M[i]-'0';
	memset(ans,0,sizeof(ans));
	for(int i=1;i<=max(p,q);i++){
		ans[i]+=n[i]+m[i];
		ans[i+1]+=ans[i]/10;
		ans[i]%=10;
	}
	ans[1]+=1;
	for(int i=1;ans[i]>9;i++){
		ans[i+1]+=ans[i]/10;
		ans[i]%=10;
	}
	int len=(ans[max(p,q)+1]==0)?max(p,q):max(p,q)+1;
	for(int i=len;i>=1;i--)printf("%d",ans[i]);
	printf("\n");return 0;
}
```
*****
## B
### 题意
> 输入两个数n,m,分别输出n^m除以19941023和19950814的余数。

### 数据范围
##### 0<=n,m<=10^9
##### 时间限制：1s

### 题解
> 简单的快速幂。

### 代码
```cpp
#include<bits/stdc++.h>
using namespace std;
long long ha[2]={19941023,19950814};
int n,m;
long long ksm(int t,int x,int k){
	if(k==1)return (x%ha[t]);
	long long ans=ksm(t,x,k/2)%ha[t];
	ans=(ans*ans)%ha[t];
	if(k%2==1)ans*=x,ans%=ha[t];
	return ans;
}
int main(){
	scanf("%d%d",&n,&m);
	for(int i=0;i<=1;i++)
	printf("%lld ",ksm(i,n,m));
	return 0;
}
```
# 赛后补题
## C
### 题意
> 给出n根栏杆以及其长度l[i]，求出用这些栏杆能够围出的最大矩形面积，如果不能围出矩形则输出-1。

### 数据范围
##### 1<=n<=16,1<=l[i]<=15
##### 时间限制：1s

### 题解
> n的范围只有16，考虑DFS。将矩形分为两部分，每部分包含一组邻边。DFS时，每次枚举第i根栏杆放在哪个部分（或者不放），同时统计两个部分栏杆的长度，处理完所有栏杆后，如果两个部分长度和相等，则判断在该情况下是否能够围成矩形，如果可以则更新答案。最后输出最大值。

### 代码
```cpp
#include<bits/stdc++.h>
using namespace std;
int n,a[20],vis[20],ans=0,f1[200],f2[200];
void DP(int sum) {
    if(sum*sum/4<=ans)return;
    memset(f1,0,sizeof(f1));
    memset(f2,0,sizeof(f2));
    f1[0]=1;f2[0]=1;
    for(int i=1;i<=n;i++)
        for(int j=sum;j>=a[i];j--) {
          if(vis[i]==1&&f1[j-a[i]])f1[j]=1;
          if(vis[i]==2&&f2[j-a[i]])f2[j]=1;
        }
    for(int i=1;i<=sum-1;i++)if(f1[i]&&f2[i])ans=max(ans,i*(sum-i));
}
void dfs(int x,int sum1,int sum2){
    if (x==n+1){
        if (sum1==sum2)DP(sum1);
        return;
    }
    vis[x]=1;dfs(x+1,sum1+a[x],sum2);
    vis[x]=2;dfs(x+1,sum1,sum2+a[x]);
    vis[x]=0;dfs(x+1,sum1,sum2);
}
int main(){
	scanf("%d",&n);
	for(int i=1;i<=n;i++)scanf("%d",&a[i]);
	if(n<4){printf("No Solution\n");return 0;}
	dfs(1,0,0);
	if(ans>0)printf("%d\n",ans);
	else printf("No Solution\n");
	return 0;
}
```
