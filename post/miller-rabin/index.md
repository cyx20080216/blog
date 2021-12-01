不知道为什么，我对着百度百科看，感觉找到的 _Miller-Rabin_ 算法代码都是错的，便自己对着百度百科写了一个，分享出来
```cpp
#include<stdlib.h>
int qPower(const int &a,const int &b,const int &m)
{
	if(b==0)
		return 1;
	else if(b%2==0)
	{
		int tmp=qPower(a,b/2,m);
		return tmp*tmp%m;
	}
	else
	{
		int tmp=qPower(a,b/2,m);
		return tmp*tmp%m*a%m;
	}
}
inline bool MillerRabin(const int &n,const int &testTime=8)
{
	if(n==0||n==1)
		return 0;
	if(n==2)
		return 1;
	int d=n-1;
	int s=0;
	while(d%2==0)
	{
		d/=2;
		s++;
	}
	for(int i=1;i<=testTime;i++)
	{
		int a=rand()%(n-2)+2;
		bool flag=0;
		int tmp=qPower(a,d,n);
		for(int r=0;r<s;r++)
		{
			if(tmp==1||qPower(a,(1<<r)*d,n)==n-1)
			{
				flag=1;
				break;
			}
		}
		if(!flag)
			return 0;
	}
	return 1;
}
```
