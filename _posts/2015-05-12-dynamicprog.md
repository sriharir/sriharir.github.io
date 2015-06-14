---
layout: post
title:  "Introduction to Dynamic Programming"
summary: Algorithms Post 1
category: "tech"
---

Majority of time during the last four days were spent on trying to understand and solve Dynamic Programming related questions. While I attempted and glanced through quite a few of them, I implemented in CPP about 9 of them so far. So this blog post is a means to review and to streamline all the concepts picked up so far. 

First of all, dynamic programming has nothing to with any data structure - but rather it's a paradigm. We use dynamic programming techniques in problems with two properties :

1) Optimal Substructure
 
2) Overlapping subproblems

One, optimal substructure means the problem could be solved as by finding an optimal solution to a subproblem. Like for example, Fibonacci numbers. **F(n) = F(n-1) + F(n-2)**. Here, by knowing the optimal solution for **F(n-1)** and **F(n-2)** we would be able to find F(n). Usually such problems tend to be solved by recursion methods. 

But recursion gives exponential time complexity. Now we use the second property of overlapping subproblems. Suppose we want to find **F(n-1)** now in the above process. **F(n-1) = F(n-2) + F(n-3)** and hence we need to calculate F(n-2) again [we have already calculated F(n-2) for finding F(n), remember]. To avoid the redundancy, a data structure can be used to store already calculated values, and while recursing, if we encounter the value, we can use it directly instead of going deeper into recursion.

{% highlight cpp tabsize=2 %}

#include <iostream>
#define NIL -1
#define MAX 100

using namespace std;

class Fibonacci{
	private:
		//data structure to save already computed values
		int table[MAX]; 
	public:
		Fibonacci(){
			int i;
			for(i=0;i<MAX;i++)
				table[i]=NIL;
			}
		//critical function to calculate F(n)
		int fibon(int n){
			//checking if already saved value exists
			if (table[n]==NIL){
				if (n==0) table[n]=0;
				else if (n==1) table[n]=1;
				else{
					//new value saved for future used
					table[n]=fibon(n-1)+fibon(n-2); 
					
					}
				}
			return table[n];
			}
	};
		
int main()
	{
		Fibonacci a=Fibonacci();
		cout<<a.fibon(9);
		return 0;
	}

{% endhighlight %}

This process of finding a value only once, could also be implemented using a bottom up approach. Starting from F(0) we build up until required F(n). Probably the same approach amateurs use, probably without realizing it's dynamic programming. 

{% highlight cpp tabsize=2 %}

#include<iostream>
#define MAX 100
#define NIL -1

using namespace std;

class Fibo{
	private:
		//data table to store values
		int table[MAX];
		//storing last n for which F(n) in table
		int lastval; 
	public:
		Fibo(){
			int i;	
			//initializing data table
			for(i=0;i<MAX;i++)
				table[i]=NIL;
			table[0]=0; //base condition
			table[1]=1; //base condition
			lastval=1;
			}
		int fibon(int n){
			if(n<lastval)
				return table[n];
			else{
				int j;
				//storing values starting from last stopped place
				for(j=lastval+1;j<=n;j++)
					table[j]=table[j-1]+table[j-2];
				lastval=n;
				return table[n];
				}
		}
	};

int main()
	{
		Fibo a=Fibo();
		cout<<a.fibon(5)<<"\n";
		cout<<a.fibon(45)<<"\n";
		cout<<a.fibon(46)<<"\n";
		cout<<a.fibon(47)<<"\n";
		return 0;
	}
			
{% endhighlight %}

Even thought the examples are basic, they outline the general procedure in DP. Less intuitive examples to follow would ease the process of understanding.

Consider a problem to find number of ways to give coin change for a given amount of money. The implementation follows as a solution [to this question](). So, first we need to dissect this question so as to figure out it's optimal substructure. 

Consider the m coin types. While issuing change for any amount, there are two possibilities. A coin type is used, or it is not used - this can be used for creating an optimal subproblem structure.. For mathematical formulation, consider m-th coin.

Let N(n, m) denote the number of ways to render change using coins 1-_m_, for an amount of _n_.

N(n,m) = N(n,m-1) + N(n - coin_value[m], m)

As for overlapping solutions, we create a 2-D array of size (n+1) x (m+1) to store values for N(0...n, 0...m).

Implementation follows:

{% highlight cpp tabsize=2 %}

#include <iostream>

using namespace std;

class Coin{
	int n,m;
	int* coin;
	//for storing data
	long int **table;
	void rendertable(){
		for(int i=1;i<n+1;i++){
			for(int j=1;j<m+1;j++){
				if(i-coin[j-1]>=0){
					//optimal substructure formulated
					//ways=ways with jth-coin + ways without jth-coin
					table[i][j]=table[i-coin[j-1]][j]+table[i][j-1];
					}
				else
					table[i][j]=table[i][j-1];
				}
			}
		}
	
	public:
		//constructor
		Coin(){
			cin>>n>>m;
			coin=new int[m];
			for(int i=0;i<m;i++)
				cin>>coin[i];
			table=new long int*[n+1];
			for(int i=0;i<n+1;i++)
				table[i]=new long int[m+1];
			for(int i=0;i<n+1;i++)
				table[i][0]=0;
			for(int i=0;i<m+1;i++)
				table[0][i]=1;
			}
		long int getresult(){
			rendertable();
			return table[n][m];
			}
		};
	int main(){
		Coin A=Coin();
		cout<<A.getresult();
		return 0;
		}

{% endhighlight %}

This fairly highlights the way to go about any dynamic programming. Once the substructure is mathematically formulated, it's only a matter of simple implementation.