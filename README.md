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
