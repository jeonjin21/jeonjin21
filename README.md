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

이러한 네트워크 플로우를 구체적으로 구현하는 대표적인 알고리즘이 포드-풀커슨 알고리즘이다.
그리고 네트워크 플로우를 성립시키기 위해서는 다음과 같은 세 가지 조건을 충족해야 한다.

```
1. 용량 제한 속성
: 유량은 용량을 초과 할 수 없다.

2. 유량의 대칭성
: 정점 a에서 정점 b까지 3이 흐른다고 한다면 정점 b에서는 정점 a까지 -3이 흐른다. 
이는 즉, 정점 b에서 정점 a까지 3이 흐른다고 봐도 무방하며, f(a,b)=-f(b,a)라고도 표기한다.

3. 유량 보존의 법칙
: 각 정점에 들어오는 유량과 나가는 유량이 일치해야한다. 단, 이때 소스와 싱크의 정점은 예외이다.
```

## Ford-Fulkerson Algorithm 작동방식

1. dfs(깊이 우선 탐색)을 통하여 증가 경로를 탐색하고 찾은 만큼 유량을 흘려 보낸다.
2. 증가 경로가 더 이상 탐색 되지 않을 때까지 1을 반복한다.






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
