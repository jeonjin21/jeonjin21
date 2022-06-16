# Ford-Fulkerson Algorithm
포드-풀커슨 알고리즘에 대해 알기 위해서는 우선 네트워크 플로우에 대해서 알아 볼 필요가 있다.


## Network Flow
:최단 경로 문제의 목적이 정점 간 간선들의 가중치의 합이 최소가 되는 경우를 찾아내는 것이라면
네트워크 플로우의 목적은 시작 지점에서 도착 지점까지 최대의 유량을 흘러 보내는 것이다. 즉, 최단
경로 문제와 반대로 가중치의 합을 최대로 만들어 내는 것인데 이 때 용량이라는 새로운 개념이 적용된다.
자세한 내용은 뒤의 내용에서 확인 가능하다.

네트워크 플로우를 기반으로 포드-풀커슨 알고리즘을 이해하기에 앞서 사용되는 주요 용어들을 알아 둘
필요가 있다.

* 유량(Flow)
:두 개의 정점 사이에 흐르는 양.

* 용량(Capacity)
:두 개의 정점 사이에 흐를 수 있는 최대한의 양.

* 소스(Source)
:시작점으로 S로 나타내는 게 일반적이다.

* 싱크(Sink)
:도착점으로 T로 나타내는 게 일반적이다.

* 증가 경로(Augmenting Path)
:소스에서 싱크로 유량이 흐를 수 있는 경로.

* 잔여 용량(Residual Capacity)
:말 그대로 남아있는 용량. 즉, 현재 흐를 수 있는 유량.(용량-유량)

```
c(a,b): 정점 a에서 정점 b로 가는 간선의 용량

f(a,b): 정점 a에서 정점 b로 실제로 흐르는 유량
```

간단한 예시를 통하여 네트워크 플로우의 진행 방식을 파악할 수 있다.

![컴알중간1](https://user-images.githubusercontent.com/101388167/165359207-a58de25a-f1b6-4235-a98b-9c91bdf1394a.png)

이러한 방향 그래프에서 간선은 유량/용량을 의미한다.
S->1->T의 경로를 살펴보면 c(S,1)=2 , c(1,T)=3이므로 2만큼의 유량을 흘려 보낼 수 있다.

![컴알중간2](https://user-images.githubusercontent.com/101388167/165360240-b08c64cc-27e1-41da-b24d-5b8b483c6134.png)

다른 경로인 S->2->T의 경로를 살펴보면 c(S,2)=3 , c(2,T)=2이므로 2만큼의 유량을 흘려 보낼 수 있다.

![컴알중간3](https://user-images.githubusercontent.com/101388167/165360251-9004bea3-da86-496b-8579-b87aec224355.png)

그 결과 전체적으로 S에서 T로 4만큼의 유량을 흘려 보낼 수 있고, 이러한 그래프는 최대 4의 유량을 흘러 
보내 줄 수 있다고 할 수 있다.

이러한 네트워크 플로우를 성립시키기 위해서는 다음과 같은 세 가지 조건을 충족해야 한다.

```
1. 용량 제한 속성
: 유량은 용량을 초과 할 수 없다.

2. 유량의 대칭성
: 정점 a에서 정점 b까지 3이 흐른다고 한다면 정점 b에서는 정점 a까지 -3이 흐른다. 
이는 즉, 정점 b에서 정점 a까지 3이 흐른다고 봐도 무방하며, f(a,b)=-f(b,a)라고도 표기한다.

3. 유량 보존의 법칙
: 각 정점에 들어오는 유량과 나가는 유량이 일치해야한다. 단, 이때 소스와 싱크의 정점은 예외이다.
```

그리고 네트워크 플로우를 구체적으로 구현하는 대표적인 알고리즘이 포드-풀커슨 알고리즘이다.

이어 소개할 포드-풀커슨 알고리즘과 애드먼드-카프 알고리즘을 설명하기에 있어서 dfs와 bfs 알고리즘을
배제 할 수 없다. 그렇기에 dfs와 bfs에 대해 간단히 소개하자면 다음과 같다.

## DFS(깊이 우선 탐색) / BFS(너비 우선 탐색)

* dfs
: 루트 노드에서 시작하는 해당 분기를 모두 거친 후에 다음 분기를 탐색. 
쉽게 설명하자면 미로찾기에서 갈림길을 고려하지 않고 한 방향으로 계속 나아가다가
막다른 길을 맞이했을 때 가장 가까운 갈림길로 되돌아와 다른 길로 나아가는 방식이다.

* bfs
: 루트 노드에서 시작할 때 인접한 노드들을 우선 탐색.
쉽게 설명하자면 미로찾기에서 갈림길을 맞이 했을 때 모든 경로를 한 번씩 방문하고 '
다시 다음 갈림길에서도 이러한 과정을 반복하는 것과 같은 방식이다.

이러한 dfs와 bfs의 예시와 코드는 다음과 같다.

![컴알중간13](https://user-images.githubusercontent.com/101388167/165478158-df9000c9-1e88-496a-a938-758b455d5b66.png)

``` C
#include <stdio.h>
#include <stdlib.h>

#define false 0
#define true 1

typedef struct incidentedge
{
	char iname;
	int w;
	struct incidentedge* next;
}incidentedge;

typedef struct vertex
{
	char vname;
	int isvisit;
	incidentedge* ihead;
	struct vertex* next;
}vertex;

typedef struct
{
	vertex* vhead;
}graph;

void init(graph* g)
{
	g->vhead = NULL;
}

void makevertex(graph* g, char vname)
{
	vertex* v = (vertex*)malloc(sizeof(vertex));
	v->vname = vname;
	v->isvisit = false;
	v->ihead = NULL;
	v->next = NULL;

	vertex* p = g->vhead;
	if (p == NULL)
		g->vhead = v;
	else
	{
		while (p->next != NULL)
			p = p->next;
		p->next = v;
	}
}

void makeincidentedge(vertex* v, char iname, int w)
{
	incidentedge* i = (incidentedge*)malloc(sizeof(incidentedge));
	i->iname = iname;
	i->w = w;
	i->next = NULL;

	incidentedge* p = v->ihead;
	if (p == NULL)
		v->ihead = i;
	else
	{
		while (p->next != NULL)
			p = p->next;
		p->next = i;
		
	}
}

vertex* findvertex(graph* g, char vname)
{
	vertex* v = g->vhead;
	while (v->vname != vname)
		v = v->next;
	return v;
}

void insertedge(graph* g, char v1, char v2, int w)
{
	vertex* v = findvertex(g, v1);
	makeincidentedge(v, v2, w);
	v = findvertex(g, v2);
	makeincidentedge(v, v1, w);
}

void dfs(graph* g, char vname)
{
	vertex* v = findvertex(g, vname);
	incidentedge* p;

	if (v->isvisit == false)
	{
		v->isvisit = true;
		printf("(%c) ", v->vname);
	}

	for (p = v->ihead; p != NULL; p = p->next)
	{
		v = findvertex(g, p->iname);
		if (v->isvisit == false)
			dfs(g, v->vname);
	}
}

void print(graph* g)
{
	vertex* v = g->vhead;
	incidentedge* i;
	for (; v != NULL; v = v->next)
	{
		printf("(%c) : ", v->vname);
		for (i = v->ihead; i != NULL; i = i->next)
			printf("(%c,%d) ", i->iname, i->w);
		printf("\n");
	}
	printf("\n");
}

void bfs(graph* g, char vname)
{
	vertex* v = findvertex(g, vname);
	incidentedge* p;
	vertex* q;

	if (v->isvisit == false)
	{
		v->isvisit = true;
		printf("(%c) ", v->vname);
	}

	while (v != NULL)
	{
		for (p = v->ihead; p != NULL; p = p->next)
		{
			q = findvertex(g, p->iname);
			if (q->isvisit == false)
			{
				q->isvisit = true;
				printf("(%c) ", q->vname);
			}
		}
		v = v->next;
	}
}

void main()
{
	graph g;
	init(&g);

	makevertex(&g, 'a');
	makevertex(&g, 'b');
	makevertex(&g, 'c');
	makevertex(&g, 'd');
	makevertex(&g, 'e');

	insertedge(&g, 'a', 'b', 1);
	insertedge(&g, 'a', 'c', 2);
	insertedge(&g, 'a', 'e', 3);
	insertedge(&g, 'b', 'c', 4);
	insertedge(&g, 'c', 'd', 6);
	insertedge(&g, 'c', 'e', 7);
	insertedge(&g, 'd', 'e', 5);

	print(&g);
	dfs(&g, 'a'); printf("\n");       or  //dfs(&g, 'a'); printf("\n");
	//bfs(&g, 'a'); printf("\n");         bfs(&g, 'a'); printf("\n"); 
}


```
![컴알중간11](https://user-images.githubusercontent.com/101388167/165478131-3bfa3afb-99a4-427f-8e58-e9e88f82d3fa.png)
![컴알중간12](https://user-images.githubusercontent.com/101388167/165478144-42aaa231-2941-4f1a-b859-0faa6b415081.png)

## Ford-Fulkerson Algorithm + 동작방식

```
1. dfs(깊이 우선 탐색)을 통하여 증가 경로를 탐색하고 찾은 만큼 유량을 흘려 보낸다.
2. 증가 경로가 더 이상 탐색 되지 않을 때까지 1을 반복한다.
```


dfs를 통하여 탐색해낸 경로가 증가 경로가 된다. 그렇기에 더 이상의 경로가 없을 때까지
dfs를 통해 탐색한 경로에 flow를 흘려보내고, 이러한 과정을 거쳐 소스에서 싱크에 도착한 
flow가 곧 최대로 흐를 수 있는 flow이다.

다음과 같은 그래프를 통해 포드-풀커슨 알고리즘의 구체적인 작동방식을 파악할 수 있다.

![컴알중간4](https://user-images.githubusercontent.com/101388167/165449719-e2e8c644-4959-47f4-893a-a12e48f2f240.png)

dfs를 통해 S->1->2->T의 증가 경로를 찾는다.
이 때, c(S,1)=1 , c(1,2)=1, c(2,T)=1이므로 1만큼의 유량을 흘려 보낼 수 있다.

![컴알중간5](https://user-images.githubusercontent.com/101388167/165449727-3f720b15-0c1e-44cc-b816-17adbe6373e8.png)

포드-풀커슨 알고리즘의 작동 방식에 따라 dfs를 통해 다음 증가경로를 찾아도 흘려보낼 수 없다.
만약 S->1->T, S->2->T의 경로를 통해 플로우를 흘려 보냈다면 위 아래의 경로 1씩, 총 2만큼의
유량을 흘려 보낼 수 있다.
이럴 때 적용되는 조건이 2.유량의 대칭성이다(f(a,b)=-f(b,a)). 정점 1에서 정점 2로 1만큼의
용량을 흘려 보낼 수 있다면 정점 2에서도 정점 1에 1만큼의 용량을 흘려 보낼 수 있다.
이러한 조건을 기반으로 그래프에 다음과 같은 새로운 간선들을 추가 할 수 있다.

![컴알중간6](https://user-images.githubusercontent.com/101388167/165449733-223793e3-c6aa-47f4-a22c-f05dc40525bd.png)

새로 추가 된 간선들에도 유량을 흘려 보낼 수 있고, 이를 통해 dfs로 
S->2->1->T의 증가 경로를 찾아 낼 수 있다.
이 때, c(S,2)=3 , c(2,1)=1, c(1,T)=2이므로 1만큼의 유량을 흘려 보낼 수 있다.

![컴알중간7](https://user-images.githubusercontent.com/101388167/165449741-269663b2-d459-49f1-8fd3-1915c020cc7f.png)

더 이상 증가 경로의 탐색이 이루어지지 않으므로, 총 2만큼의 유량을 흘려 보낼 수 있다.

포드-풀커슨 알고리즘의 코드는 다음과 같다.

``` C
#include <iostream>
#include <cstring>
#include <vector>
#include <queue>
#include <algorithm>

#define inf 9999
#define max_v 50

using namespace std;

int V;
int capacity[max_v][max_v];
int flow[max_v][max_v];

int networkflow(int source, int sink)
{
	memset(flow, 0, sizeof(flow));

	int totalflow = 0;   // 플로우를 0으로 초기화

	while(true)  // bfs를 통하여 경로 탐색
    {
		vector<int> parent(max_v, -1);
		queue<int> q;

		parent[source] = source;
		q.push(source);
		while(!q.empty() && parent[sink] == -1)
        {
			int here = q.front();
			q.pop();

			for(int there = 0 ; there < V ; there++)  // 잔여 용량이 남아있는 간선을 탐색
            {
				if(capacity[here][there] - flow[here][there] > 0 && parent[there] == -1)
                {
					q.push(there);
					parent[there] = here;
				}
			}
		}

		if(parent[sink] == -1) break;  // 증가 가능 경로가 탐색 안 될 시 종료

		// 증가 경로를 통해 얼마나 보낼지 결정
		int amount = inf;
		for(int p = sink ; p != source ; p = parent[p])
        {
			amount = min(capacity[parent[p]][p] - flow[parent[p]][p], amount);
		}

		// 증가 경로로 유량을 흘려 보냄
		for(int p = sink ; p != source ; p = parent[p])
        {
			flow[parent[p]][p] += amount;
			flow[p][parent[p]] -= amount;  // 조건2. 유량의 대칭성
		}

		totalflow += amount;
	}

	return totalflow;
}

int main()
{
	V = 4;

	capacity[0][1] = 1;
	capacity[0][2] = 2;
	
	capacity[1][2] = 1;
	capacity[1][3] = 3;

	capacity[2][3] = 1;

	int totalflow = networkflow(0, 3);
	cout << totalflow << endl;

	return 0;
}

```

![컴알중간14](https://user-images.githubusercontent.com/101388167/165498339-293b1e42-c753-45b8-a306-f3dd4f1e316b.png)

이러한 포드-풀커슨 알고리즘에도 문제점이 있는데,
다음의 구체적인 예시를 통해 문제점을 파악할 수 있다.

![컴알중간9](https://user-images.githubusercontent.com/101388167/165462794-589426a3-a94c-4678-b59e-cd8c6c69da25.png)

주어진 그래프에서 포드-풀커슨 알고리즘을 적용하기 위해 dfs를 통해
증가 경로를 탐색하면 원하는 결과를 도출하기까지 다음과 같은 과정을 반복한다.

1. S->1->2->T
: 주어진 경로로 1만큼의 유량을 흘려 보내준다.
2. S->2->1->T
: 유량의 대칭성을 이용해 주어진 경로에 1만큼의 유량을 흘려 보내준다.
3. S->1->2->T
: 주어진 경로로 1만큼의 유량을 흘려 보내준다.
4. S->2->1->T
: 유량의 대칭성을 이용해 주어진 경로에 1만큼의 유량을 흘려 보내준다.
     .
     .
     .
     
이 과정을 그래프에 나타내면 다음과 같다.

![컴알중간8](https://user-images.githubusercontent.com/101388167/165462808-fd90a956-4094-48d8-ae89-ade6d8a44ad3.png)

따라서 포드-풀커슨 알고리즘을 통해 이 그래프의 최대유량을 구하려면 dfs를
통하여 20000번의 탐색을 해야 한다. 즉, 시간복잡도를 수치화하면 20000번의
연산이 필요한 것이다. 보다 효율적인 시간복잡도를 지향하는 알고리즘에서 flow가
많을수록 효율이 더욱 떨어지는 포드-풀커슨 알고리즘의 치명적인 단점을 확인 할 수 있다.

그리고 이러한 포드-풀커슨 알고리즘과 더불어 네트워크 플로우를 구현하는 대표적인 알고리즘이자
일반적으로 더욱 효율적인 동작을 수행하는 것이 애드몬드-카프 알고리즘이다.

## Edmonds-Karp Algorithm + 동작방식 / 성능 비교

```
1. bfs(너비 우선 탐색)을 통하여 증가 경로를 탐색하고 찾은 만큼 유량을 흘려 보낸다.
2. 증가 경로가 더 이상 탐색 되지 않을 때까지 1을 반복한다.
```
위와 같이 애드먼드-카프 알고리즘은 포드-풀커슨 알고리즘과 거의 동일한 동작방식으로 작동한다.
단, dfs가 아닌 bfs를 통해 증가 경로를 탐색한다는 차이점이 있다.

위와 같은 예시에 애드먼드-카프 알고리즘을 적용하여 애드워드-카프 알고리즘이 일반적으로 더욱
효율적인 이유를 파악 가능하다.

![컴알중간9](https://user-images.githubusercontent.com/101388167/165462794-589426a3-a94c-4678-b59e-cd8c6c69da25.png)

주어진 그래프에서 애드먼드-카프 알고리즘을 적용하기 위해 bfs를 통해
증가 경로를 탐색하면 원하는 결과를 도출하기까지 다음과 같은 과정을 거친다.

1. S->1->T
: 주어진 경로로 10000만큼의 유량을 흘려 보내준다.
2. S->2->T
: 주어진 경로로 10000만큼의 유량을 흘려 보내준다.

이 과정을 그래프에 나타내면 다음과 같다.

![컴알중간10](https://user-images.githubusercontent.com/101388167/165469064-d2e2ae9c-5985-4cba-a6ad-6a2d52d282fa.png)

이 그래프의 최대 유량을 구하는 데 있어서 포드-풀커슨 알고리즘은 20000번의 탐색 과정을 
거친 데 비해 애드먼드-카프 알고리즘은 단 2번의 탐색으로 원하는 결과를 도출해냈다.
따라서 이 두 알고리즘의 효율성을 결과를 도출한 탐색 횟수에 기반을 둔 간단한 수치상으로 
나타내보면 20000과 2이다. 따라서 이 예시의 경우 애드먼드-카프 알고리즘이 포드-풀커슨 
알고리즘에 비해 약 10000배 효율적인 성능을 보였다.
즉, bfs를 통한 탐색으로 동작하는 애드먼드-카프 알고리즘이 일반적인 경우 dfs를 통한 탐색으로
동작하는 포드-풀커슨 알고리즘보다 좋은 성능을 보이며, flow가 커질수록 성능의 차이는 더 크다.


# 정렬 알고리즘

### 버블 정렬
``` c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define size 32

void makelist(int list[])
{
	for (int i = 0; i < size; i++)
		list[i] = rand() % 100 + 1;
}

void printlist(int list[])
{
	for (int i = 0; i < size; i++)
		printf("[%d] ", list[i]);
	printf("\n");
}

void bubblesort(int list[])
{
	int j;
	int temp;

	for (int i = 1; i < size; i++)
	{
		temp = list[i];
		j = i - 1;
		while (0 <= j && temp < list[j])
		{
			list[j + 1] = list[j];
			j--;
		}
		list[j + 1] = temp;
	}
}

void main()
{
	clock_t finish, start;
	double duration;
	int arr[size];
	srand(time(NULL));

	makelist(arr);
	printlist(arr);

	start = clock();
	bubblesort(arr);
	printlist(arr);
	finish = clock();
	duration = (double)(finish - start) / CLOCKS_PER_SEC;
	printf("걸린 시간: %lf", duration);
}
```
![컴알 9주차과제-버블정렬](https://user-images.githubusercontent.com/101388167/166713472-075cc610-4c01-4549-926c-0beeae0a9c69.png)
![컴알 9주차과제-버블정렬-그래프](https://user-images.githubusercontent.com/101388167/166715067-d1f084a5-59fb-4346-b9c8-5cfe26e463cc.png)


### 선택 정렬
``` c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define SIZE 32

void makelist(int list[])
{
	for (int i = 0; i < SIZE; i++)
		list[i] = rand() % 100 + 1;
}

void printlist(int list[])
{
	for (int i = 0; i < SIZE; i++)
		printf("%d ", list[i]);
	printf("\n");
}

void selectionsort(int list[])
{
	int min, temp;
	for (int i = 0; i < SIZE - 1; i++)
	{
		min = i;
		for (int j = i + 1; j < SIZE; j++)
			if (list[j] < list[min])
				min = j;
		temp = list[min];
		list[min] = list[i];
		list[i] = temp;
	}
}

void main()
{
	clock_t finish, start;
	double duration;
	int list[SIZE];
	srand(time(NULL));

	makelist(list);
	printlist(list);

	start = clock();
	selectionsort(list);
	printlist(list);
	finish = clock();
	duration = (double)(finish - start) / CLOCKS_PER_SEC;
	printf("걸린 시간: %lf\n", duration);
}
```
![컴알 9주차과제-선택정렬](https://user-images.githubusercontent.com/101388167/166714870-15e5a4d3-1074-4db9-8c5f-093d349592ac.png)
![컴알 9주차과제-선택정렬-그래프](https://user-images.githubusercontent.com/101388167/166715090-caaa1769-441f-4e09-9a01-b20de45ff1aa.png)


### 삽입 정렬
```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define SIZE 32

void makelist(int list[])
{
	for (int i = 0; i < SIZE; i++)
		list[i] = rand() % 100 + 1;
}

void printlist(int list[])
{
	for (int i = 0; i < SIZE; i++)
		printf("%d ", list[i]);
	printf("\n");
}

void insertionsort(int list[])
{
	int i, j, key;
	for (i = 1; i < SIZE; i++)
	{
		key = list[i];
		for (j = i - 1; j >= 0 && list[j] > key; j--)
			list[j + 1] = list[j];
		list[j + 1] = key;
	}
}

void main()
{
	clock_t finish, start;
	double duration;
	int list[SIZE];
	srand(time(NULL));

	makelist(list);
	printlist(list);

	start = clock();
	insertionsort(list);
	printlist(list);
	finish = clock();
	duration = (double)(finish - start) / CLOCKS_PER_SEC;
	printf("걸린 시간: %lf\n", duration);
}
```
![컴알 9주차과제-삽입정렬](https://user-images.githubusercontent.com/101388167/166714891-8dc605a7-1801-47a8-8bdc-c9aa11d880aa.png)
![컴알 9주차과제-삽입정렬-그래프](https://user-images.githubusercontent.com/101388167/166715096-767b0008-b18f-48fe-b22b-50081740c0d9.png)


### 쉘 정렬
``` c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define size 32
#define swap(a,b) {int temp; temp=a; a=b; b=temp;}

void makelist(int list[])
{
	for (int i = 0; i < size; i++)
		list[i] = rand() % 100 + 1;
}

void printlist(int list[])
{
	for (int i = 0; i < size; i++)
		printf("[%d] ", list[i]);
	printf("\n");
}

void insertionsort(int list[],int num, int step)
{
	int i, j;
	for (i = step; i < num; i += step)
	{
		for (j = i; j > 0; j -= step)
		{
			if (list[j - step] > list[j])
			{
				swap(list[j - step], list[j]);
			}
			else
				break;
		}
	}
}

void shellsort(int list[])
{
	int i, step;

	for (step = size / 2; step > 0; step /= 2)
	{
		for (i = 0; i < step; i++)
		{
			insertionsort(list + i, size - 1, step);
		}
	}
}

void main()
{
	clock_t finish, start;
	double duration;
	int arr[size];
	srand(time(NULL));

	makelist(arr);
	printlist(arr);

	start = clock();
	shellsort(arr);
	printlist(arr);
	finish = clock();
	duration = (double)(finish - start) / CLOCKS_PER_SEC;
	printf("걸린 시간: %lf", duration);
}

```
![컴알 9주차과제-쉘정렬](https://user-images.githubusercontent.com/101388167/166714900-2056d700-6fbf-400a-b8f1-ab22f1e349a1.png)
![컴알 9주차과제-쉘정렬-그래프](https://user-images.githubusercontent.com/101388167/166715104-e65b14db-6b68-4954-92b5-89be78f5a1cf.png)


### 힙 정렬
``` c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define size 100

typedef int element;

typedef struct heaptype
{
	element heap[size];
	int n;
}heaptype;

void init(heaptype* ht)
{
	ht->n = 0;
}

void upheap(heaptype* ht)
{
	int i = ht->n;
	int k = ht->heap[i];

	while ((i != 1) && (k < ht->heap[i / 2]))
	{
		ht->heap[i] = ht->heap[i / 2];
		i /= 2;
	}
	ht->heap[i] = k;
}

void downheap(heaptype* ht)
{
	int temp = ht->heap[1];
	int p = 1;
	int c = 2;

	while (c <= ht->n)
	{
		if ((c < ht->n) && (ht->heap[c + 1] < ht->heap[c]))
			c++;
		if (temp <= ht->heap[c])
			break;

		ht->heap[p] = ht->heap[c];
		p = c;
		c *= 2;
	}
	ht->heap[p] = temp;
}

void insertitem(heaptype* ht, int k)
{
	ht->n++;
	ht->heap[ht->n] = k;
	upheap(ht);
}

int removeitem(heaptype* ht)
{
	int k = ht->heap[1];
	ht->heap[1] = ht->heap[ht->n];
	ht->n--;
	downheap(ht);
	return k;
}

void heapsort(heaptype* ht1, element H[])
{
	heaptype ht2;
	init(&ht2);

	for (int i = 1; i <= ht1->n; i++)
	{
		ht2.heap[i] = ht1->heap[i];
		ht2.n++;
	}

	for (int i = 1; i <= ht1->n; i++)
		H[i] = removeitem(&ht2);
}

void inplaceheapsort(heaptype* ht)     //제자리 정렬
{
	int n = ht->n;
	int key;
	for (int i = 0; i < n; i++)
	{
		key = removeitem(ht);
		ht->heap[ht->n + 1] = key;
	}
}

void printArray(int H[], int n)
{
	for (int i = 1; i <= n; i++)
		printf("[%d] ", H[i]);
	printf("\n");
}

void main()
{
	clock_t finish, start;
	double duration;
	heaptype ht;
	init(&ht);
	element H[size];

	srand(time(NULL));

	start = clock();
	for (int i = 0; i < 131022; i++)
	{
		int k = rand() % 100 + 1;
		insertitem(&ht, k);
		printf("[%d] ", k);
	}
	printf("\n");
	printf("\n");

	heapsort(&ht, H);
	printf("정방향: ");
	printArray(H, ht.n);
	finish = clock();
	duration = (double)(finish - start) / CLOCKS_PER_SEC;
	printf("걸린 시간: %lf\n", duration);

	printf("\n");
	printf("역방향: ");
	int n = ht.n;
	inplaceheapsort(&ht);
	printArray(ht.heap, n);
}
```
![컴알 9주차과제-힙정렬](https://user-images.githubusercontent.com/101388167/166714930-0aacd6c9-d242-45f7-94dc-3d138fcadf18.png)
![컴알 9주차과제-힙정렬-그래프](https://user-images.githubusercontent.com/101388167/166715117-b915bfae-b7bd-41af-949f-35f123ab3e94.png)


### 퀵 정렬
``` c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define SIZE 32
#define SWAP(x,y,t) ((t)=(x), (x)=(y), (y)=(t))

void makelist(int list[])
{
	for (int i = 0; i < SIZE; i++)
		list[i] = rand() % 100 + 1;
}

void printlist(int list[])
{
	for (int i = 0; i < SIZE; i++)
		printf("%d ", list[i]);
	printf("\n");
}

int partition(int list[], int left, int right)
{
	int pivot, temp, low, high;

	pivot = list[left];
	low = left;
	high = right + 1;

	do
	{
		do
		{
			low++;
		} while (list[low] < pivot);

		do
		{
			high--;
		} while (list[high] > pivot);

		if (low < high)
			SWAP(list[low], list[high], temp);

	} while (low < high);

	SWAP(list[left], list[high], temp);
	return high;
}

void quicksort(int list[], int left, int right)
{
	if (left < right)
	{
		int q = partition(list, left, right);
		quicksort(list, left, q - 1);
		quicksort(list, q + 1, right);
	}
}

void main()
{
	clock_t finish, start;
	double duration;
	int list[SIZE];
	int sorted[SIZE];
	srand(time(NULL));

	makelist(list);
	printlist(list);

	start = clock();
	quicksort(list, 0, SIZE - 1);
	printlist(list);
	finish = clock();
	duration = (double)(finish - start) / CLOCKS_PER_SEC;
	printf("걸린 시간: %lf\n", duration);

}
```
![컴알 9주차과제-퀵정렬](https://user-images.githubusercontent.com/101388167/166714920-3b33f20e-22ad-4bd8-918a-fb2eba02d019.png)
![컴알 9주차과제-퀵정렬-그래프](https://user-images.githubusercontent.com/101388167/166715134-bdf9e2c3-2285-42ee-8f4d-d93e9ee07b72.png)


### 성능 비교를 위한 각 정렬의 종합 그래프
![컴알 9주차과제-종합 그래프](https://user-images.githubusercontent.com/101388167/166723451-dc4fb581-18e8-4cc8-b4c1-deb76e33a8b8.png)

마지막 지점이 높은 순서대로 버블 정렬, 선택 정렬, 삽입 정렬, 힙 정렬, 쉘 정렬, 퀵 정렬이다.
이 순서는 각 정렬의 실행 시간이 큰 순서이므로 곧, 각 정렬의 성능에 역순이다. 


# 최적화 알고리즘을 이용한 회귀식 추정

## 상관분석
때때로 어떤 두 사건 간의 연관성을 분석해야 할 경우가 있다. 둘 혹은 그 이상의 변수들이 서로 관계를 맺고 변화할 때 그 새로운 관계를 파악해야하는데
이 때 가장 잘 알려진 방법 중 하나가 상관분석과 회귀분석이다.
그리고 회기분석을 보다 잘 이해하기 위해서는 상관분석을 간단히라도 알아 볼 필요가 있다.
상관분석은 간단히 말하자면 변수들이 서로 간에 얼마나 조밀하게 직선적인 관계를 형성하는 지를 파악하는 통계적 기법이다.
상관분석을 통해 산점도(Scatter plot)를 구상해 낼 수 있다. 산점도란 직교 좌표계를 이용하여 두 변수 간 관계를 나타내는 방법이다.

![컴알 기말-3](https://user-images.githubusercontent.com/101388167/174134421-6f25b126-a9f5-4518-ad79-27cfbd6862dd.png)

위와 같이 x축을 bmi(체질량지수), y축을 target(당뇨병의 수치)로 두고 두 변수간의 관계를 산점도로 나타내보면 대략 
양의 관계를 이루고 있음을 파악 할 수 있다.


## 회기분석(Regression Analysis)
회기분석이란 한 개 혹은 그 이상의 변수 즉, 독립변수에 대해 다른 변수 즉, 종속변수와의 관계를 수학적 모형을 통해 예측하고 설명하는 분석법이다.
앞서 살펴 본 상관분석에서는 산점도에서 점들의 분포를 통해 일정 패턴을 파악한 후에 서로 다른 두 변수간의 선형 관계를 파악했다면 회기분석은 여기서
더 나아가 일정한 패턴을 활용하여 무엇인가를 예측까지 해내는 분석이라고 볼 수 있다.

![컴알 기말-1](https://user-images.githubusercontent.com/101388167/174134401-8c092db1-9b0a-49cc-8a6b-37bcdce002a0.png)

위의 표는 442명의 사람들을 상대로 10가지의 특성들을 나열한 것이다.
그리고 상관분석을 통하여 target(당뇨병의 수치)와 bmi(체질량지수) 간의 일정한 패턴을 확인 할 수 있었고, 0.59의 상관계수를 통해
양의 선형관계를 이루고 있음을 파악했다. 회귀분석을 이용하면 여기서 더 나아가 변수 간에 관계를 예측해 볼 수 있다.

회기분석은 크게 두 가지 방법으로 나뉘는데 우선 독립변수와 종속변수가 각각 한 개일 때 관계를 분석하는 단순선형회귀분석이 있다.
다음으로는 종속변수는 한 개지만 독립변수가 두 개 이상일 때는 중선형회귀분석이 있다.

### 단순선형회귀분석
```
Y = B0 + B1*X + &
```
구하고자 하는 두 변수 X와 Y의 관계에 적절한 회귀식을 구하기 위해서는 관측된 값으로부터 회귀계수 B0과 B1의 값을 추정해내야 한다.
그리고 이 때 일반적으로 많이 사용되는 방법을 최소제곱법이라고 한다. 위에 제시한 데이터프레임에서 Y는 index로 두고 X는 bmi로 두고 
다음의 과정을 거쳐 회기 분석을 돌려주면 B0는 152.13348416. B1은 949.43526038이 나온다. 

![컴알 기말-2](https://user-images.githubusercontent.com/101388167/174134414-bf02d42a-b61d-4243-9656-ebf6bedf7c8f.png)

위에 제시된 데이터프레임에 구한 B0, B1을 대입하고 원하는 두 변수의 관계를 회귀식으로 표현하면 Target= 949*bmi + 152이다.
이는 곧 bmi가 1 증가할 때마다 Target의 수치가 949의 양만큼 증가한다고 볼 수 있는 것이다.
이 회귀식을 그려보면 다음과 같다.

![컴알 기말-3](https://user-images.githubusercontent.com/101388167/174134421-6f25b126-a9f5-4518-ad79-27cfbd6862dd.png)
=>

![컴알 기말-4](https://user-images.githubusercontent.com/101388167/174134425-aea2c176-7391-40cf-937e-e9bd2e7f983f.png)


## 모의 담금질 기법(simulated annealing)
보통의 알고리즘 문제들은 해법을 가지고 있다. 다만, 그 중에서도 완벽한 최적해를 구할 수 없는 경우도 있다.
이를 보완하고자 있는 개념이 근사해이다. 그리고 이러한 근사해를 구하는 대표적인 알고리즘이 모의 담금질 기법이다.
모의 담금질 기법에 쉽게 접근해보자면 최적해에 다가가는 과정이다. 일반적으로 최적해를 구하기 위해 가능한 해들의 후보를 파악하고
그 중 가장 좋은 해를 도출해내는 것인데 모의 담금질 기법은 굳이 해의 후보를 보지 않는다. 그저 좋은 해가 있을 것 같은 해당 일부 지역
만을 탐색한다. 그리고 현재상태에서의 인접한 상황을 파악하고 손익을 비교해 확률적으로 이동한다.
이러한 과정이 모의 담금질에서 이웃해를 설정하는 과정이다.
모의 담금질 기법을 순서로 나타내면 다음과 같다.

1. 인접상태정의
: 인접한 상태를 찾는 연산은 너무 길어도 안 되고 너무 많이 변해도 안 된다. 최대한 조금 걸리고 조금 변하도록 잡는 게 좋다.

2. 평가함수정의
: 해가 얼마나 최적해에 근접한지 알려주는 방안으로 일반적으로 최소화, 최대화하고 싶은 그 값을 평가함수로 둔다.

3. 이동
: 상태가 더 나아진다면 즉, 에너지가 줄어든다면 혹은 평가함수값이 좋아진다면 확률이 1을 넘어 무조건 해당 방향으로 간다.
반대로 상태가 악화된다면 즉, 에너지가 증가하거나 평가함수값이 나빠진다면 위에서 계산한 확률에 따라 이동한다.
이러한 이유로 보통은 좋은 해를 찾아가지만, 나쁜 곳도 가끔 탐색을 한다.

4. 온도감소
: 해를 탐색하는 초기 즉, 온도가 높을 때는 최대한 많은 곳을 뛰어다니며 어느 곳이 전역 최적에 인접한지를 봐야하지만, 전역최적점
에 대한 "감"이 잡히면 그 근방에서 조금이라도 더 나은 해를 찾기 위한 세밀한 탐색을 진행해야한다.
온도가 높을수록 확률이 0.5에 근사하는 random search가 되는 반면, 온도가 낮아지면 확률이 1에 근사해 ordinary hill climbing이 된다.
정리하자면 초반에는 변동이 심하지만 해에 근접할수록 T(온도)가 감소하여 보다 세밀한 진행이 이루어진다.

![컴알 기말-5](https://user-images.githubusercontent.com/101388167/174142620-cd0f8ea9-3fe9-439b-b5a7-a9a277d5e75c.png)

``` C
#include <bits/stdc++.h>
#include <random>
 
using namespace std;
int n;
int grid[40][40];
 
void turn(int x, int y) { 
	if (y == 0) {
		for (int i = 0; i < n; i++)
			grid[x][i] = 1 - grid[x][i];
	}
	else {
		for (int i = 0; i < n; i++)
			grid[i][x] = 1 - grid[i][x];
	}
}
 
void func() {
	for (int i = 0; i < n; i++) {
		int s = 0;
		for (int j = 0; j < n; j++)
			s += grid[j][i];
		if (s > (n / 2)) turn(i, 1);
	}
}
 
int scoring() {
	int s = 0;
	for (int i = 0; i < n; i++)
		for (int j = 0; j < n; j++)
			s += grid[i][j];
	return s;
}
 
double t=1, d=0.9999, k=10, lim=0.0005;
std::mt19937_64 seed(1919);
std::uniform_real_distribution<double> rng(0, 1);
 
int ret = 999;
 
void simulated_annealing() {
	double e1, e2;
	int ori[40][40];
	while (t > lim) {
		e1 = scoring();
		for (int i = 0; i < n; i++) for (int j = 0; j < n; j++) ori[i][j] = grid[i][j];
		int pos = rand() % n;
		turn(pos, 0);
		func();
		e2 = scoring();
 
		double p = exp((e1 - e2) / (k*t));
		if (p < rng(seed)) for (int i = 0; i < n; i++) for (int j = 0; j < n; j++) grid[i][j] = ori[i][j];
		t *= d;
 
		ret = min(ret, scoring());
	}
}
 
int main() {
	scanf("%d", &n);
	for (int i = 0; i < n; i++) {
		for (int j = 0; j < n; j++) {
			char ch;
			scanf(" %c", &ch);
			grid[i][j] = (ch == 'H' ? 0 : 1);
		}
	}
	simulated_annealing();
	printf("%d", ret);
	return 0;
}
``` 

![컴알 기말-6](https://user-images.githubusercontent.com/101388167/174146525-c1e52631-3395-4285-b659-600794903384.png)


<!--
**jeonjin21/jeonjin21** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
