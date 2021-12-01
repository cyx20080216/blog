### 该博文汇集了一些博主做的小游戏
二十四点解题：
```cpp
#include<bits/stdc++.h>
#define EXP 1e-7
using namespace std;
struct Element
{
	enum ElementType{add=0,sub=1,mul=2,div=3,mod=4,num=5};
	ElementType type;
	double data;
	Element():
		type(num),
		data(0)
	{
	}
	Element(const ElementType &t):
		type(t),
		data(0)
	{
	}
	Element(const double &n):
		type(num),
		data(n)
	{
	}
};
struct TreeNode
{
	Element element;
	int f,l,r;
	TreeNode(const Element &_element,int _l=-1,int _r=-1,int _f=-1):
		element(_element),
		f(_f),
		l(_l),
		r(_r)
	{
	}
};
inline double calc(const vector<Element> &expr)
{
	stack<double> sta;
	for(vector<Element>::const_iterator i=expr.begin();i!=expr.end();i++)
	{
		if(i->type==Element::num) sta.push(i->data);
		else
		{
			if(sta.size()<2) throw 400;
			double a,b;
			b=sta.top();
			sta.pop();
			a=sta.top();
			sta.pop();
			if(i->type==Element::add) sta.push(a+b);
			else if(i->type==Element::sub) sta.push(a-b);
			else if(i->type==Element::mul) sta.push(a*b);
			else if(i->type==Element::div)
			{
				if(fabs(b-0)<=EXP) throw 500;
				sta.push(a/b);
			}
			else
			{
				if(fabs(b-0)<=EXP) throw 500;
				sta.push(a-floor(a/b)*b);
			}
		}
	}
	if(sta.size()!=1) throw 400;
	return sta.top();
}
inline vector<TreeNode> makeTree(const vector<Element> &expr)
{
	vector<TreeNode> tree;
	stack<int> sta;
	for(vector<Element>::const_iterator i=expr.begin();i!=expr.end();i++)
	{
		if(i->type==Element::num)
		{
			tree.push_back(TreeNode(*i));
			sta.push(tree.size()-1);
		}
		else
		{
			if(sta.size()<2) throw 400;
			int a,b;
			b=sta.top();
			sta.pop();
			a=sta.top();
			sta.pop();
			tree.push_back(TreeNode(*i,a,b));
			tree[a].f=tree.size()-1;
			tree[b].f=tree.size()-1;
			sta.push(tree.size()-1);
		}
	}
	return tree;
}
const string optsChar[5]={"﹢","﹣","×","÷","%",};
int nums[4];
inline vector<Element> makeExpr(int a,int b,int c,int d,int opt1_id,int opt2_id,int opt3_id,int t)
{
	vector<Element> expr;
	if(t==0)
	{
		expr.push_back(Element(a));
		expr.push_back(Element(b));
		expr.push_back(Element(Element::ElementType(opt1_id)));
		expr.push_back(Element(c));
		expr.push_back(Element(Element::ElementType(opt2_id)));
		expr.push_back(Element(d));
		expr.push_back(Element(Element::ElementType(opt3_id)));
	}
	else if(t==1)
	{
		expr.push_back(Element(a));
		expr.push_back(Element(b));
		expr.push_back(Element(Element::ElementType(opt1_id)));
		expr.push_back(Element(c));
		expr.push_back(Element(d));
		expr.push_back(Element(Element::ElementType(opt3_id)));
		expr.push_back(Element(Element::ElementType(opt2_id)));
	}
	else if(t==2)
	{
		expr.push_back(Element(a));
		expr.push_back(Element(b));
		expr.push_back(Element(c));
		expr.push_back(Element(Element::ElementType(opt2_id)));
		expr.push_back(Element(Element::ElementType(opt1_id)));
		expr.push_back(Element(d));
		expr.push_back(Element(Element::ElementType(opt3_id)));
	}
	else if(t==3)
	{
		expr.push_back(Element(a));
		expr.push_back(Element(b));
		expr.push_back(Element(c));
		expr.push_back(Element(Element::ElementType(opt2_id)));
		expr.push_back(Element(d));
		expr.push_back(Element(Element::ElementType(opt3_id)));
		expr.push_back(Element(Element::ElementType(opt1_id)));
	}
	else
	{
		expr.push_back(Element(a));
		expr.push_back(Element(b));
		expr.push_back(Element(c));
		expr.push_back(Element(d));
		expr.push_back(Element(Element::ElementType(opt3_id)));
		expr.push_back(Element(Element::ElementType(opt2_id)));
		expr.push_back(Element(Element::ElementType(opt1_id)));
	}
	return expr;
}
inline string intToString(int num)
{
	if(num==0) return "0";
	char sta[15];
	short top=-1;
	string str;
	if(num<0) str="-";
	while(num!=0) sta[++top]=(char)(num%10<0?-(num%10):num%10),num/=10;
	while(top>=0) str.push_back(sta[top]+'0'),top--;
	return str;
}
void dfs(const vector<TreeNode> &tree,string &str,const int &pos)
{
	#define PRI(TYPE) ((TYPE)==Element::add||(TYPE)==Element::sub?1:2)
	if(tree[pos].element.type==Element::num) str.append(intToString(tree[pos].element.data));
	else
	{
		if(!(tree[pos].f==-1||PRI(tree[pos].element.type)>PRI(tree[tree[pos].f].element.type)||\
		(PRI(tree[pos].element.type)==PRI(tree[tree[pos].f].element.type)&&pos==tree[tree[pos].f].l))) str.push_back('(');
		dfs(tree,str,tree[pos].l);
		str.append(optsChar[tree[pos].element.type]);
		dfs(tree,str,tree[pos].r);
		if(!(tree[pos].f==-1||PRI(tree[pos].element.type)>PRI(tree[tree[pos].f].element.type)||\
		(PRI(tree[pos].element.type)==PRI(tree[tree[pos].f].element.type)&&pos==tree[tree[pos].f].l))) str.push_back(')');
	}
	#undef PRI
}
inline void print(const vector<Element> &expr)
{
	vector<TreeNode> tree=makeTree(expr);
	int root;
	for(int i=0;i<tree.size();i++)
	{
		if(tree[i].f==-1)
		{
			root=i;
			break;
		}
	}
	string str;
	dfs(tree,str,root);
	cout<<str<<"\n";
}
inline int check(const vector<Element> &expr)
{
	try
	{
		return fabs(calc(expr)-24)<=EXP;
	}
	catch(int error_code)
	{
		if(error_code==500) return 0;
		else
		{
			printf("Unknown error! ");
			return 0;
		}
	}
}
inline void sol1(int a,int b,int c,int d)
{
	for(int i=0;i<4;i++)
	{
		for(int j=0;j<4;j++)
		{
			for(int k=0;k<4;k++)
			{
				for(int t=0;t<5;t++)
				{
					vector<Element> expr=makeExpr(a,b,c,d,i,j,k,t);
					if(check(expr)) print(expr);
				}
			}
		}
	}
}
inline void sol2()
{
	for(int i=0;i<4;i++)
	{
		for(int j=0;j<4;j++)
		{
			if(j==i) continue;
			for(int k=0;k<4;k++)
			{
				if(k==i||k==j) continue;
				for(int l=0;l<4;l++)
				{
					if(l==i||l==j||l==k) continue;
					sol1(nums[i],nums[j],nums[k],nums[l]);
				}
			}
		}
	}
}
int main()
{
//	freopen("sol.txt","w",stdout);
	scanf("%d %d %d %d",&nums[0],&nums[1],&nums[2],&nums[3]);
	sol2();
	return 0;
}

```
谁是卧底：
```cpp
#include<stdio.h>
#include<stdlib.h>
#include<string>
#include<time.h>
#define CARD_NUM 15
using namespace std;
bool pm_win(int peo_num,bool *peo_life,bool *wd_pla);
bool wd_win(int peo_num,bool *peo_life,bool *wd_pla);
int main()
{
	string pm[CARD_NUM]={"包菜","包子","被子","壁纸","风扇","饼干","纸巾","橙子","唇膏","平板","保安","水盆","气泡","魔术师","双胞胎"};
	string wd[CARD_NUM]={"生菜","饺子","床单","贴画","空调","薯片","湿巾","橘子","口红","ipad","保镖","水桶","水泡","魔法师","龙凤胎"};
	int peo_num=0;
	printf("请输入3~10的人数:");
	scanf("%d",&peo_num);
	while(!(peo_num<11&&peo_num>2))
	{
		system("cls");
		printf("人数只能为3~10");
		printf("请输入3~10的人数:");
		scanf("%d",&peo_num);	
	}
	int wd_num=peo_num%2==0?peo_num/2-1:peo_num/2;
	int ran_num=time(NULL);
	srand((unsigned)ran_num);
	int ca_num=rand()%CARD_NUM;
	ran_num++;
	bool wd_pla[10]={0,};
	bool peo_life[10]={1,1,1,1,1,1,1,1,1,1};
	for(int i=0;i<peo_num;i++)
	{
		if(wd_num==0) break;
		if(wd_num==peo_num-i)
		{
			wd_pla[i]=1;
			wd_num--;
		}
		else if(rand()%peo_num<wd_num)
		{
			wd_pla[i]=1;
			ran_num++;
			wd_num--;
		}
	}
	system("cls");
	for(int i=1;i<=peo_num;i++)
	{
		printf("玩家%d的单词是...\n",i);
		system("pause");
		system("cls");
		if(wd_pla[i-1]==1) printf("玩家%i的单词是%s\n",i,wd[ca_num].c_str());
		else printf("玩家%i的单词是%s\n",i,pm[ca_num].c_str());
		system("pause");
		system("cls");
	}
	while(!(pm_win(peo_num,peo_life,wd_pla)||wd_win(peo_num,peo_life,wd_pla)))
	{
		short answer=0;
		printf("请选出得票最高者:");
		scanf("%d",&answer);
		while (!(peo_life[answer-1]==1&&peo_num>=answer))
		{
			system("cls");
			if (peo_num<answer) printf("没有这个人\n");
			else printf("这个人已出局\n");
			printf("请选出得票最高者:");
			scanf("%d",&answer);
		}
		peo_life[answer-1]=0;
		system("cls");
	}
	if(pm_win(peo_num,peo_life,wd_pla)) printf("平民胜利!\n");
	else printf("卧底胜利!\n");
	system("pause");
}
bool pm_win(int peo_num,bool *peo_life,bool *wd_pla)
{
	for(int i=0;i<peo_num;i++)
	{
		if(peo_life[i]==1&&wd_pla[i]==1) return 0;
	}
	return 1;
}
bool wd_win(int peo_num,bool *peo_life,bool *wd_pla)
{
	for(int i=0;i<peo_num;i++)
	{
		if(peo_life[i]==1&&wd_pla[i]==0) return 0;
	}
	return 1;
}
```
人机对战井字棋：
```cpp
#include<stdio.h>
#include<stdlib.h>
#include<vector>
#define GETBIT(ITEM,POS) ((ITEM)>>(POS)&1)
#define SETBIT(ITEM,POS,VALUE) ((VALUE)?(ITEM)|1<<(POS):(ITEM)&~(1<<(POS)))
using namespace std;
struct State
{
	enum BlockType{Empty=0,Black=1,White=2};
	enum EndType{No,BlackWin,WhiteWin,Dogfall};
	int data;
	State(int _data=0):data(_data)
	{
	}
	BlockType getBlockData(int x,int y)
	{
		return BlockType(data>>(3*x+y-4)*2&3);
	}
	State setBlockData(int x,int y,BlockType val)
	{
		return State(data&~(3<<(3*x+y-4)*2)|val<<(3*x+y-4)*2);
	}
	EndType getEnd()
	{
		bool blackWin=blackIsWin(),whiteWin=whiteIsWin();
		if(!blackWin&&!whiteWin)
		{
			if(isFull()) return Dogfall;
			return No;
		}
		if(blackWin&&!whiteWin) return BlackWin;
		if(!blackWin&&whiteWin) return WhiteWin;
	}
	bool blackIsWin()
	{
		for(int i=1;i<=3;i++)
		{
			if(getBlockData(i,1)==Black&&getBlockData(i,2)==Black&&getBlockData(i,3)==Black) return 1;
		}
		for(int i=1;i<=3;i++)
		{
			if(getBlockData(1,i)==Black&&getBlockData(2,i)==Black&&getBlockData(3,i)==Black) return 1;
		}
		if(getBlockData(1,1)==Black&&getBlockData(2,2)==Black&&getBlockData(3,3)==Black) return 1;
		if(getBlockData(1,3)==Black&&getBlockData(2,2)==Black&&getBlockData(3,1)==Black) return 1;
	}
	bool whiteIsWin()
	{
		for(int i=1;i<=3;i++)
		{
			if(getBlockData(i,1)==White&&getBlockData(i,2)==White&&getBlockData(i,3)==White) return 1;
		}
		for(int i=1;i<=3;i++)
		{
			if(getBlockData(1,i)==White&&getBlockData(2,i)==White&&getBlockData(3,i)==White) return 1;
		}
		if(getBlockData(1,1)==White&&getBlockData(2,2)==White&&getBlockData(3,3)==White) return 1;
		if(getBlockData(1,3)==White&&getBlockData(2,2)==White&&getBlockData(3,1)==White) return 1;
	}
	bool isFull()
	{
		for(int i=1;i<=3;i++)
		{
			for(int j=1;j<=3;j++)
			{
				if(getBlockData(i,j)==Empty) return 0;
			}
		}
		return 1;
	}
};
struct Node
{
	enum Next{Black,White};
	Next next;
	int end;
	vector<int> nextNode;
	Node(Next _next=Black,int _end=0):next(_next),end(_end)
	{
	}
}nodes[262144];
int dfs(State state)
{
	State::EndType end=state.getEnd();
	if(end!=State::No) return nodes[state.data].end=(end==State::BlackWin?1:(end==State::WhiteWin?-1:0));
	int maxe=-1,mine=1;
	for(int i=1;i<=3;i++)
	{
		for(int j=1;j<=3;j++)
		{
			if(state.getBlockData(i,j)==State::Empty)
			{
				State tmp(state.setBlockData(i,j,nodes[state.data].next==Node::Black?State::Black:State::White));
				nodes[state.data].nextNode.push_back(tmp.data);
				nodes[tmp.data].next=(nodes[state.data].next==Node::Black?Node::White:Node::Black);
				int ans=dfs(tmp);
				maxe=max(maxe,ans),mine=min(mine,ans);
			}
		}
	}
	return nodes[state.data].end=(nodes[state.data].next==Node::Black?maxe:mine);
}
inline void print(State state)
{
	char maps[4][4];
	for(int i=1;i<=3;i++)
	{
		for(int j=1;j<=3;j++) maps[i][j]=(state.getBlockData(i,j)==State::Empty?' ':(state.getBlockData(i,j)==State::Black?'x':'o'));
	}
	printf("  | |  \n %c|%c|%c \n--+-+--\n %c|%c|%c \n--+-+--\n %c|%c|%c \n  | |  \n",maps[1][1],maps[1][2],maps[1][3],maps[2][1],maps[2][2],maps[2][3],maps[3][1],maps[3][2],maps[3][3]);
}
inline int getNext(int at)
{
	if(nodes[at].next==Node::Black)
	{
		int maxe=-1,ans;
		for(int i=0;i<nodes[at].nextNode.size();i++)
		{
			if(maxe<nodes[nodes[at].nextNode[i]].end)
			{
				maxe=nodes[nodes[at].nextNode[i]].end;
				ans=nodes[at].nextNode[i];
			}
		}
		return ans;
	}
	else
	{
		int mine=1,ans;
		for(int i=0;i<nodes[at].nextNode.size();i++)
		{
			if(mine>nodes[nodes[at].nextNode[i]].end)
			{
				mine=nodes[nodes[at].nextNode[i]].end;
				ans=nodes[at].nextNode[i];
			}
		}
		return ans;
	}
}
int at=0;
State::BlockType type;
int main()
{
	nodes[0].next=Node::Black;
	dfs(State(0));
	printf("Choose \"x\" or \"o\"(\"x\" is the first):");
	char ch;
	scanf("%c",&ch);
	if(ch=='x')
	{
		type=State::Black;
		print(State(at));
		int x,y;
		scanf("%d %d",&x,&y);
		while(State(at).getBlockData(x,y)!=State::Empty)
		{
			printf("This block is not empty!\n");
			scanf("%d %d",&x,&y);
		}
		at=State(at).setBlockData(x,y,type).data;
		State::EndType end=State(at).getEnd();
		if(end==State::BlackWin)
		{
			printf("\"x\" is WIN!\n");
			system("pause");
			return 0;
		}
		else if(end==State::WhiteWin)
		{
			printf("\"o\" is WIN!\n");
			system("pause");
			return 0;
		}
		else if(end==State::Dogfall)
		{
			printf("Dogfall!\n");
			system("pause");
			return 0;
		}
	}
	else type=State::White;
	while(1)
	{
		at=getNext(at);
		print(State(at));
		State::EndType end=State(at).getEnd();
		if(end==State::BlackWin)
		{
			printf("\"x\" is WIN!\n");
			system("pause");
			return 0;
		}
		else if(end==State::WhiteWin)
		{
			printf("\"o\" is WIN!\n");
			system("pause");
			return 0;
		}
		else if(end==State::Dogfall)
		{
			printf("Dogfall!\n");
			system("pause");
			return 0;
		}
		int x,y;
		scanf("%d %d",&x,&y);
		while(State(at).getBlockData(x,y)!=State::Empty)
		{
			printf("This block is not empty!\n");
			scanf("%d %d",&x,&y);
		}
		at=State(at).setBlockData(x,y,type).data;
		end=State(at).getEnd();
		if(end==State::BlackWin)
		{
			printf("\"x\" is WIN!\n");
			system("pause");
			return 0;
		}
		else if(end==State::WhiteWin)
		{
			printf("\"o\" is WIN!\n");
			system("pause");
			return 0;
		}
		else if(end==State::Dogfall)
		{
			printf("Dogfall!\n");
			system("pause");
			return 0;
		}
	}
    return 0;
}
```
小公举养成记：
```cpp
#include<iostream>
#include<stdio.h>
#include<stdlib.h>
#include<time.h>
#include<string>
#include<cstring>
#include<fstream>
#define CH_L 1024
using namespace std;
int r_seed=time(0);
int random(int a,int b);
void game(char d_name[CH_L],char f_name[CH_L],int b_month,int b_data,char d_star[128],int sub_i[5],int money,int t_year,int t_month,bool t_talk,bool t_gift);
int main()
{
	char cmd=0;
	printf("请选择游戏模式:\n①新游戏\n②读档\n");
	cin>>cmd;
	if(cmd=='1')
	{
		char d_name[CH_L]={};//女儿名字
		char f_name[CH_L]={};//父亲名字
		char d_star[128]={};//星座名字
		int b_month=0;//生日月份
		int b_data=0;//生日日期
		string star[12][2]={
			{"摩羯座","水瓶座"},//1月
			{"水瓶座","双鱼座"},//2月 
			{"双鱼座","白羊座"},//3月
			{"白羊座","金牛座"},//4月
			{"金牛座","双子座"},//5月
			{"双子座","巨蟹座"},//6月
			{"巨蟹座","狮子座"},//7月
			{"狮子座","处女座"},//8月
			{"处女座","天秤座"},//9月
			{"天秤座","天蝎座"},//10月
			{"天蝎座","射手座"},//11月
			{"射手座","摩羯座"} //12月
		};//十二星座列表
		int star_d[12]={20,19,21,20,21,22,22,22,23,22,21,22};//十二星座跨度表
		//星座算法:star[month-1][data/star_d[month-1]]
		printf("请输入父亲姓名:");
		scanf("%s",f_name);
		printf("请输入女儿姓名:");
		scanf("%s",d_name);
		printf("请输入女儿出生月日(MM DD):");
		scanf("%d %d",&b_month,&b_data);
		strcpy(d_star,star[b_month-1][b_data/star_d[b_month-1]].c_str());
		//根据星座初始化属性
		const char sub_name[5][127]={"体力","智力","魅力","道德","气质"};
		int sub_i[5]={};
		int money=500;
		if(star[b_month-1][b_data/star_d[b_month-1]]=="摩羯座")
		{
			sub_i[0]=56;
			sub_i[1]=21;
			sub_i[2]=16;
			sub_i[3]=25;
			sub_i[4]=23;
		}
		else if(star[b_month-1][b_data/star_d[b_month-1]]=="水瓶座")
		{
			sub_i[0]=41;
			sub_i[1]=43;
			sub_i[2]=20;
			sub_i[3]=23;
			sub_i[4]=17;
		}
		else if(star[b_month-1][b_data/star_d[b_month-1]]=="双鱼座")
		{
			sub_i[0]=41;
			sub_i[1]=20;
			sub_i[2]=29;
			sub_i[3]=23;
			sub_i[4]=32;
		}
		else if(star[b_month-1][b_data/star_d[b_month-1]]=="白羊座")
		{
			sub_i[0]=80;
			sub_i[1]=15;
			sub_i[2]=15;
			sub_i[3]=11;
			sub_i[4]=21;
		}
		else if(star[b_month-1][b_data/star_d[b_month-1]]=="金牛座")
		{
			sub_i[0]=46;
			sub_i[1]=30;
			sub_i[2]=28;
			sub_i[3]=20;
			sub_i[4]=29;
		}
		else if(star[b_month-1][b_data/star_d[b_month-1]]=="双子座")
		{
			sub_i[0]=50;
			sub_i[1]=35;
			sub_i[2]=23;
			sub_i[3]=8;
			sub_i[4]=20;
		}
		else if(star[b_month-1][b_data/star_d[b_month-1]]=="巨蟹座")
		{
			sub_i[0]=40;
			sub_i[1]=31;
			sub_i[2]=33;
			sub_i[3]=17;
			sub_i[4]=33;
		}
		else if(star[b_month-1][b_data/star_d[b_month-1]]=="狮子座")
		{
			sub_i[0]=85;
			sub_i[1]=9;
			sub_i[2]=11;
			sub_i[3]=20;
			sub_i[4]=37;
		}
		else if(star[b_month-1][b_data/star_d[b_month-1]]=="处女座")
		{
			sub_i[0]=35;
			sub_i[1]=28;
			sub_i[2]=36;
			sub_i[3]=18;
			sub_i[4]=40;
		}
		else if(star[b_month-1][b_data/star_d[b_month-1]]=="天秤座")
		{
			sub_i[0]=42;
			sub_i[1]=33;
			sub_i[2]=25;
			sub_i[3]=32;
			sub_i[4]=28;
		}
		else if(star[b_month-1][b_data/star_d[b_month-1]]=="天蝎座")
		{
			sub_i[0]=50;
			sub_i[1]=25;
			sub_i[2]=40;
			sub_i[3]=18;
			sub_i[4]=17;
		}
		else if(star[b_month-1][b_data/star_d[b_month-1]]=="射手座")
		{
			sub_i[0]=57;
			sub_i[1]=31;
			sub_i[2]=15;
			sub_i[3]=19;
			sub_i[4]=20;
		}
		printf("----------------游戏开始----------------\n一天,%s收养了流浪的%s,开始了新生活\n",f_name,d_name);
		game(d_name,f_name,b_month,b_data,d_star,sub_i,money,1659,b_month,1,1);
		system("pause");
	}
	else
	{
		//读档
		char d_name[CH_L]={};//女儿名字
		char f_name[CH_L]={};//父亲名字
		char d_star[128]={};//星座名字
		int b_month=0;//生日月份
		int b_data=0;//生日日期
		string star[12][2]={
			{"摩羯座","水瓶座"},//1月
			{"水瓶座","双鱼座"},//2二
			{"双鱼座","白羊座"},//3月
			{"白羊座","金牛座"},//4月
			{"金牛座","双子座"},//5月
			{"双子座","巨蟹座"},//6月
			{"巨蟹座","狮子座"},//7月
			{"狮子座","处女座"},//8月
			{"处女座","天秤座"},//9月
			{"天秤座","天蝎座"},//10月
			{"天蝎座","射手座"},//11月
			{"射手座","摩羯座"} //12月
		};//十二星座列表
		int star_d[12]={20,19,21,20,21,22,22,22,23,22,21,22};//十二星座跨度表
		//星座算法:star[month-1][data/star_d[month-1]]
		int sub_i[5]={};
		int money;
		int t_year,t_month;
		bool t_talk,t_gift;
		fstream ifile("game_data.dat",ios::in|ios::binary);
		while(!ifile)
		{
			printf("未找到存档文件game_data.dat,请将存档文件放在当前目录下并重命名为game_data.dat\n");
			system("pause");
			ifile.open("game_data.dat",ios::in|ios::binary);
		}
		ifile.read(d_name,sizeof(d_name));
		ifile.read(f_name,sizeof(f_name));
		ifile.read((char*)(&b_month),sizeof(b_month));
		ifile.read((char*)(&b_data),sizeof(b_data));
		ifile.read((char*)(sub_i),sizeof(sub_i));
		ifile.read((char*)(&money),sizeof(money));
		ifile.read((char*)(&t_year),sizeof(t_year));
		ifile.read((char*)(&t_month),sizeof(t_month));
		ifile.read((char*)(&t_talk),sizeof(t_talk));
		ifile.read((char*)(&t_gift),sizeof(t_gift));
		strcpy(d_star,star[b_month-1][b_data/star_d[b_month-1]].c_str());
		game(d_name,f_name,b_month,b_data,d_star,sub_i,money,t_year,t_month,t_talk,t_gift);
		system("pause");
	}
	return 0;
}
int random(int a,int b)
{
	srand(r_seed);
	r_seed++;
	return rand()%(b-a+1)+a;
}
void game(char d_name[CH_L],char f_name[CH_L],int b_month,int b_data,char d_star[128],int sub_i[5],int money,int t_year,int t_month,bool t_talk,bool t_gift)
{
	const char sub_name[5][127]={"体力","智力","魅力","道德","气质"};
	for(int year=t_year;year<=1659+8;year++)
	{
		for(int month=(year==t_year)?t_month:1;month<=12;month++)
		{
			bool gift=((year==t_year&&month==t_month)?t_gift:1);
			printf("当前时间:%d年%d月\n",year,month);
			if(month==b_month&&gift)
			{
				//进行生日操作
				//岁数计算:year-1648
				printf("今天是%s的%d岁生日,要花200金买礼物吗?(y/n)\n",d_name,year-1649);
				char tmp;
				cin>>tmp;
				if(tmp=='y')
				{
					if(money>=200)
					{
						printf("花费200金买了个精美的礼物,%s很开心\n",d_name);
						//对属性进行操作,并通知玩家
						money-=200;
						for(int i=0;i<5;i++)
						{
							int tmp=random(1,15);
							sub_i[i]+=tmp;
							printf("%s+%d\n",sub_name[i],tmp);
						}
						gift=0;
					}
					else printf("钱不够\n");
				}
			}
			//进行月份安排
			bool talk=((year==t_year&&month==t_month)?t_talk:1);//是否谈过话 
			for(bool finish=0;!finish;)
			{
				char cmd;
				printf("要做什么事吗?\n①查看状态\n②安排行程\n③谈话\n④存档\n");
				cin>>cmd;
				switch(cmd)
				{
					case '1':
						//显示状态
						printf("金币:%d\n星座:%s\n",money,d_star);
						for(int i=0;i<5;i++)
						{
							printf("%s:%-4d",sub_name[i],sub_i[i]);
							for(int j=1;j<=10;j++)
							{
								if(sub_i[i]/10>=j) printf("■");
								else printf("□");
							}
							printf("\n");
						}
						break;
					case '2':
						//安排行程
						for(int i=0;i<3;i++)
						{
							char cmd;
							printf("%d月%s,要做些什么呢?\n①学习武艺\n②上私塾\n③学习礼法\n④打工赚钱\n",month,i==0?"上旬":(i==1?"中旬":"下旬"));
							cin>>cmd;
							switch(cmd)
							{
								case '1':
									if(money==0)
									{
										printf("没钱了,强制打工\n");
										int tmp=random(100,250);
										money+=tmp;
										printf("金钱+%d\n",tmp);
										break;
									}
									else
									{
										int tmp1=random(0,5);
										int tmp2=random(0,5);
										int tmp3=random(0,10);
										printf("哼哼哈兮,快使用双节棍...\n");
										printf("%s+%d\n%s+%d\n金钱-%d\n",sub_name[0],tmp1,sub_name[2],tmp2,tmp3<=money?tmp3:money);
										sub_i[0]+=tmp1;
										sub_i[2]+=tmp2;
										money-=tmp3<=money?tmp3:money;
										break;
									}
								case '2':
									if(money==0)
									{
										printf("没钱了,强制打工\n");
										int tmp=random(100,250);
										money+=tmp;
										printf("金钱+%d\n",tmp);
										break;
									}
									else
									{
										int tmp1=random(0,5);
										int tmp2=random(0,5);
										int tmp3=random(0,10);
										printf("学而不思则罔,思而不学则殆...\n");
										printf("%s+%d\n%s+%d\n金钱-%d\n",sub_name[1],tmp1,sub_name[4],tmp2,tmp3<=money?tmp3:money);
										sub_i[1]+=tmp1;
										sub_i[4]+=tmp2;
										money-=tmp3<=money?tmp3:money;
										break;
									}
								case '3':
									if(money==0)
									{
										printf("没钱了,强制打工\n");
										int tmp=random(100,250);
										money+=tmp;
										printf("金钱+%d\n",tmp);
										break;
									}
									else
									{
										int tmp1=random(0,5);
										int tmp2=random(0,5);
										int tmp3=random(0,10);
										printf("中国,是礼仪之邦...\n");
										printf("%s+%d\n%s+%d\n金钱-%d\n",sub_name[3],tmp1,sub_name[4],tmp2,tmp3<=money?tmp3:money);
										sub_i[3]+=tmp1;
										sub_i[4]+=tmp2;
										money-=tmp3<=money?tmp3:money;
										break;
									}
								default:
									{
										int tmp=random(100,250);
										money+=tmp;
										printf("金钱+%d\n",tmp);
										break;
									}
							}
						}
						finish=1;
						break;
					case '3':
						if(talk)
						{
							//谈话
							int cmd;
							printf("请选择谈话方式:\n①打招呼\n②聊天\n③严厉的教育\n④给零花钱\n");
							scanf("%d",&cmd);
							switch(cmd)
							{
								case 1:
									printf("哈喽!\n");
									//打招呼无影响
									break;
								case 2:
									printf("......\n");
									//聊天无影响
									break;
								case 3:
									{
										printf("你对%s进行了严厉的教育\n",d_name);
										int l_d=random(0,15);
										sub_i[3]+=l_d;
										printf("%s+%d\n",sub_name[3],l_d);
									}
									break;
								default:
									printf("你给了%s零花钱100金\n",d_name);
									//对属性进行操作,并通知玩家
									if(money>=100)
									{
										money-=100;
										int num=random(10,25);
										int l_sub_i[5]={};
										for(int i=0;i<num;i++)
										{
											l_sub_i[random(0,4)]++;
										}
										for(int i=0;i<5;i++)
										{
											sub_i[i]+=l_sub_i[i];
											if(l_sub_i[i]>0) printf("%s+%d\n",sub_name[i],l_sub_i[i]);
										}
									}
									else printf("钱不够,需100金\n");
									break;
							}
							talk=0;
						}
						else printf("这个月已谈话\n");
						break;
					default:
						fstream ofile("game_data.dat",ios::out|ios::binary);
						ofile.write(d_name,CH_L);
						ofile.write(f_name,CH_L);
						ofile.write((char*)(&b_month),sizeof(b_month));
						ofile.write((char*)(&b_data),sizeof(b_data));
						ofile.write((char*)(sub_i),20);
						ofile.write((char*)(&money),sizeof(money));
						ofile.write((char*)(&year),sizeof(year));
						ofile.write((char*)(&month),sizeof(month));
						ofile.write((char*)(&talk),sizeof(talk));
						ofile.write((char*)(&gift),sizeof(gift));
						printf("已存档为game_data.dat\n");
						ofile.close();
						break;
				}
			}
		}
	}
	//进行结局判定
	if(sub_i[0]+sub_i[1]+sub_i[2]+sub_i[3]+sub_i[4]>=2000) printf("经过刻苦修炼,恭喜你登基为女王陛下!!!\n----------------游戏结束----------------\n");//女王
	else if(sub_i[0]+sub_i[1]+sub_i[2]+sub_i[3]+sub_i[4]>=1800) printf("经过刻苦修炼,恭喜你成为为王妃!!\n----------------游戏结束----------------\n");//王妃
	else if(sub_i[0]+sub_i[1]+sub_i[2]+sub_i[3]+sub_i[4]>=1600) printf("经过刻苦修炼,恭喜你成为为女将军!\n----------------游戏结束----------------\n");//女将军
	else if(sub_i[0]+sub_i[1]+sub_i[2]+sub_i[3]+sub_i[4]>=1200)
	{
		if(sub_i[0]>sub_i[1]&&sub_i[0]>sub_i[2]&&sub_i[0]>sub_i[3]&&sub_i[0]>sub_i[4]) printf("经过刻苦修炼,恭喜你成为为近卫骑士团长!\n----------------游戏结束----------------\n");//近卫骑士团长
		else if(sub_i[1]>sub_i[0]&&sub_i[1]>sub_i[2]&&sub_i[1]>sub_i[3]&&sub_i[1]>sub_i[4]) printf("经过刻苦修炼,恭喜你成为为皇家学院总裁!\n----------------游戏结束----------------\n");//皇家学院总裁
		else if(sub_i[2]>sub_i[1]&&sub_i[2]>sub_i[0]&&sub_i[2]>sub_i[3]&&sub_i[2]>sub_i[4]) printf("经过刻苦修炼,恭喜你成为为国王的宠妃!\n----------------游戏结束----------------\n");//国王的宠妃
		else if(sub_i[3]>sub_i[1]&&sub_i[3]>sub_i[2]&&sub_i[3]>sub_i[0]&&sub_i[3]>sub_i[4]) printf("经过刻苦修炼,恭喜你成为为高级祭祀!\n----------------游戏结束----------------\n");//高级祭祀
		else if(sub_i[4]>sub_i[1]&&sub_i[4]>sub_i[2]&&sub_i[4]>sub_i[3]&&sub_i[4]>sub_i[0]) printf("经过刻苦修炼,恭喜你成为为公主!\n----------------游戏结束----------------\n");//公主
	}
	else//随机	农妇,魔术师,修女,作家,酒吧女郎
	{
		int tmp=random(0,5);
		string job[5]={"农妇","魔术师","修女","作家","酒吧女郎"};
		printf("经过刻苦修炼,恭喜你成为为%s!\n----------------游戏结束----------------\n",job[tmp].c_str());
	}
}
```
