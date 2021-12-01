### 这道题应该使用动态规划
但是我们一看题目就会发现，这样做时间复杂度会达到$O(n^4)$

于是大佬们就开始用线段树等来进行优化

**可是真的需要优化吗?**

让我们算一算：

$R,C<=100$

$O(n^4)$的复杂度会使循环体被运行$10^8$次，约$530$毫秒，而且实际运算时时间会更短

所以，这道题不需要优化

**Code:**
```cpp
#include<bits/stdc++.h>
using namespace std;
template<typename type>
inline void read(type & num)
{
	num=0;
	bool flag=0;
	char ch=getchar();
	while(!(ch>='0'&&ch<='9')) flag=(ch=='-'),ch=getchar();
	while(ch>='0'&&ch<='9') num=(num<<1)+(num<<3)+(ch^'0'),ch=getchar();
	num=(flag?-num:num);
}
template<typename type>
inline void write(type num)
{
	if(num==0)
	{
		putchar('0');
		return;
	}
	char sta[30];
	short top=-1;
	if(num<0) putchar('-');
	while(num!=0) sta[++top]=(char)(num%10<0?-(num%10):num%10),num/=10;
	while(top>=0) putchar(sta[top]+'0'),top--;
}
#define M 1000000007
int r,c,k;
int maps[101][101],f[101][101];
inline void dp()
{
	f[1][1]=1;
	for(int i=1;i<=r;i++)
	{
		for(int j=1;j<=c;j++)
		{
			if(i==1&&j==1) continue;
			for(int k=1;k<i;k++)
			{
				for(int l=1;l<j;l++) if(maps[i][j]!=maps[k][l]) f[i][j]=(f[i][j]+f[k][l])%M;
			}
		}
	}
}
int main()
{
	read(r),read(c),read(k);
	for(int i=1;i<=r;i++)
	{
		for(int j=1;j<=c;j++) read(maps[i][j]);
	}
	dp();
	write(f[r][c]);
	return 0;
}
```
