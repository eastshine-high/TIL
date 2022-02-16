# Graph

**그래프는 현실 세계의 사물이나 추상적인 개념 간의 `연결 관계`를 표현한다.** 여러 도시들을 연결하는 도로망, 사람들 간의 지인 관계, 웹사이트 간의 링크 관계 등이 우리 주변에서 찾을 수 있는 연결 구조의 예라고 할 수 있다. 트리에 있었떤 부모 자식 관계에 고나한 제약이 없기 때문에 그래프는 트리보다 훨씬 다양한 구조를 표현할 수 있고, 현실 세계의 수많은 문제들을 푸는 데 유용하게 사용된다. 때문에 그래프는 수학과 전산학에서 폭넓게 연구되는 주제이며, 프로그래밍 대회에서도 이에 관련된 문제가 매우 자주 출현한다.

### 그래프의 정의

**그래프 G(V, E)는 어떤 자료나 개념을 표현하는 정점(vertex)들의 집합 V와 이들을 연결하는 간선(edge)들의 집합 E로 구성된 자료구조**이다. 그래프는 정점들과 간선들로 정의되며, 정점의 위치 정보나 간선의 순서 등은 그래프의 정의에 포함되지 않는다.

`G = (V(G),E(G)) = (V,E)`

위 식은 "그래프 G는 노드들의 집합 V(G)와 간선들의 집합 E(G)로 구성된다"는 의미이다.

![https://open4tech.com/wp-content/uploads/2018/12/graph-data-struct.png](https://open4tech.com/wp-content/uploads/2018/12/graph-data-struct.png)

[https://open4tech.com/wp-content/uploads/2018/12/graph-data-struct.png](https://open4tech.com/wp-content/uploads/2018/12/graph-data-struct.png)

### 그래프의 종류

그래프의 정의는 위와 같이 간단하지만 **그래프는 표현하고자 하는 대상에 따라 여러 가지 변형된 형태를 가질 수 있다.** 이들은 **정점이나 간선에 추가적 속성**을 부여할 수도 있고, 존재할 수 있는 **간선이나 정점의 형태에 제약**을 두기도 한다.

**일반적으로 간선의 특성에 따라 그래프의 종류가 구분된다.**

- 간선의 방향성
    - 방향 그래프(directed graph) : 간선에 방향이 있는 그래프(간선에 방향이라는 새로운 속성을 갖는다)
    - 무방향 그래프(undirected graph) : 간선에 방향이 없는 그래프
- 간선의 가중치
    - 가중치 그래프(weighted graph) : 간선에 가중치(weight)라고 불리는 실수 속성을 부여한다.
- 그래프의 형태로 그래프를 분류(구조적 특징)
    - 완전 그래프 : 연결 가능한 최대 간선 수를 가진 그래프
        - 완전 그래프는 그래프 내의 모든 노드가 1:1 간선으로 연결된 경우, 즉 연결 가능한 최대 간선 수를 가진 그래프를 말한다. 노드의 개수를 n, 간선의 개수를 m이라 하였을 때, 무방향 그래프와 방향 그래프에서의 최대 간선 수는 각각 다음과 같이 식으로 정의할 수 있다.
        - 무방향 그래프 m = $m= n(n-1)/2$
        - 방향 그래프 m = $n(n-1)$
    - 부분 그래프 : 원래의 그래프에서 일부의 노드나 간선을 제외하여 만든 그래프
    - 다중 그래프(multigraph) : 두 정점 사이에 두 개 이상의 간선이 있을 수 있는 그래프

### 그래프 관련 용어

인접(Adjacent) : 두 개의 노드를 연결하는 간선이 존재하는 경우 **두 노드는 인접(Adjacent)**되었다고 한다.

부속(Incident) : 두 개의 노드를 연결하는 간선이 존재하는 경우 이 **간선은 두 노드에 각각 부속(Incident)**되었다고 한다.

차수(Degree) : **노드에 부속된(연결된) 간선의 개수를 차수(Degree)라 한다.** 단 방향 그래프의 경우에는 노드로 들어오는 간선의 개수를 진입차수(In-degree), 노드에서 나가는 간선의 개수를 진출차수(Out-degree)라 한다.

경로(Path) : 끝과 끝이 서로 연결된 간선들을 순서대로 나열한 것이다. (1,2), (2,3), (3,4), (4,5)는 끝과 끝이 이어진 간선들이므로 하나의 경로를 이룬다. 따라서 두 노드 A에서 노드 B까지의 경로(Path)는 노드 A에서 노드 B에 이르는 **간선들의 인접(Adjacent) 노드를 순서대로 나열한 리스트**를 말한다.

사이클(Cycle) : 시작한 점에서 끝나는 경로(Path)

루프(Loop) : 그래프의 임의의 노드에서 **자기 자신으로 이어지는 간선**을 루프(Loop)라고 한다.

그래프의 동일성 : 같은 그래프라 할지라도 시각적 표현은 다를 수 있으며, **노드와 간선의 집합이 같으면 같은 그래프**이다.

> 이산 수학(Discrete Mathematics)에서는 자료 구조(Data Sturcture)에서의 경로(path)를 워크(walk), 트레일(trail), 경로(path)로 세분화하여 개념화하기도 한다.

워크(walk) : G(V, E)에서 꼭지점 v(0)와 v(k)가 있다고 하자. v(0)에서 v(k)까지의 워크는 다음과 같은 형태를 가지는 유한한 순서쌍이다. W = v(0)e(1)v(1)e(2)v(2) ... e(k)v(k).

트레일(trail) : 워크 W의 변들 e(1), e(2), ... e(k)가 모두 서로 다를 경우.

경로(path) : 트레일 W의 꼭지점들 v(0), v(1), ... v(k)가 모두 서로 다를 경우.

## 그래프 순회

그래프 순회란 그래프 탐색(Search)이라고도 불리며 **그래프의 각 정점을 방문하는 과정**을 말한다. 크게 깊이 우선 탐색(Depth-First Search)과 너비 우선 탐색(Breadth-First Search)의 2가지 알고리즘이 있다. DFS는 19세기 프랑스의 수학자 찰스 피에르 트레모(Charles Pierre Tremaux)가 미로 찾기를 풀기 위한 전략을 찾다가 고안한 것으로 알려져 있으며, **일반적으로 BFS에 비해 DFS가 더 널리 쓰인다.** 코딩 테스트 시에도 대부분의 그래프 탐색은 DFS로 구현하게 될 것이다.

DFS(Depth-First Search)는 주로 스택으로 구현하거나 재귀로 구현하며, 이후에 살펴볼 백트래킹을 통해 뛰어난 효용을 보인다. 반면, BFS는 주로 큐로 구현하며, 그래프의 최단 경로를 구하는 문제 등에 사용된다. 

그래프를 표현하는 방법에는 크게 인접 행렬(Adjacency Matrix)과 인접 리스트(Adjacency List)의 2가지 방법이 있다. 여기서는 인접 리스트로 표현할 것이다. 인접 리스트는 출발 노드를 키로, 도착 노드를 값으로 표현할 수 있따. 도착 노드는 여러 개가 될 수 있으므로 리스트 형태가 된다. 파이썬의 딕셔너리 자료형으로 다음과 같이 나타낼 수 있다.

```python
graph = {
    1: [2, 3, 4],
    2: [5],
    3: [5],
    4: [],
    5: [6, 7],
    6: [],
    7: [3],
}
```

자바 언어로는 다음과 같이 나타낼 수 있다. 아래는 인접리스트를 만드는 프로그램이다.

```java
public class Graph {
	 	private LinkedList<Integer>[] adj; //인접 리스트 생성
	    private int v; //vertex - 정점
	    
	    // 그래프 생성 및 초기화
	    Graph(int v){
	    	this.v = v; //정점 개수 초기화
	    	//visit = new boolean[v]; // 체크를 위한 값 생성
	    	adj = new LinkedList[v+1]; // v로 하면 0부터 시작하니까, 보기 쉽게 1부터함. 0은 공백으로 둠
	    	//인접리스트 초기화 작업
	    	for(int i=0; i<v+1; i++) {
	    		adj[i] = new LinkedList();
	    	}
	    }
	    
	    // 그래프 인자끼리 연결하기
	    void addEdge(int v1, int v2) {
	    	adj[v1].add(v2);
	    	adj[v2].add(v1);
	    }
	 
	    
	    public static void main(String args[]) {
	    	Graph dfs_g = new Graph(5);
	  
	        dfs_g.addEdge(1, 2);
	        dfs_g.addEdge(1, 3);
	        dfs_g.addEdge(3, 4);
	        dfs_g.addEdge(3, 5);
	        
	        //인자값 1부터 출력
	        for(int i=1; i<dfs_g.adj.length; i++) {
	        	System.out.println(i+" : "+dfs_g.adj[i]);
	        }   
	    }
}
```

### DFS(깊이 우선 탐색)

깊이 우선 탐색(Depth-First Search)는 그래프의 모든 정점을 발견하는 가장 단순하고 고전적인 방법이다. **현재 정점과 인접한 간선들을 하나씩 검사하다가, 아직 방문하지 않은 정점으로 향하는 간선이 있다면 그 간선을 무조건 따라간다.** 이 과정에서 더이상 갈 곳이 없는 막힌 정점에 도달하면 포기하고, 마지막에 따라왔던 간선을 따라 뒤로 돌아간다. 깊이 우선 탐색은 탐색의 각 과정에서 가능한 한 그래프 안으로 '깊이' 들어가려고 시도하며, 막힌 정점에 도달하지 않는 한 뒤도 돌아가지 않는다. **중요한 특성은 더 따라갈 간선이 없을 경우 이전으로 돌아간다는 점이다**.

![https://upload.wikimedia.org/wikipedia/commons/thumb/1/1f/Depth-first-tree.svg/1200px-Depth-first-tree.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1f/Depth-first-tree.svg/1200px-Depth-first-tree.svg.png)

[https://upload.wikimedia.org/wikipedia/commons/thumb/1/1f/Depth-first-tree.svg/1200px-Depth-first-tree.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1f/Depth-first-tree.svg/1200px-Depth-first-tree.svg.png)

일반적으로 DFS는 스택으로 구현하며, 재귀를 이용하면 좀 더 간단하게 구현할 수 있다. 코딩테스트 시에도 재귀 구현이 더 선호되는 편이다. 아래는 재귀 호출을 이용한 DFS 구현이다.

```python
def recursive_dfs(v, discovered = []):
    discovered.append(v)
    for w in graph[v]:
        if not w in discovered:
            discovered = recursive_dfs(w, discovered)
    return discovered
```

### BFS(너비 우선 탐색)

너비 우선 탐색(Breadth-First Search)은 각 정점을 발견할 때마다 모든 인접 정점들을 검사한다. 이 중 처음 보는 정점을 발견하면 이 정점을 방문 예정이라고 기록해 둔 뒤, 별도의 위치에 저장한다. 인접한 정점을 모두 검사하고 나면, 지금까지 저장한 목록에서 다음 정점을 꺼내서 방문하게 된다. 따라서 **너비 우선 탐색의 방문 순서는 정점의 목록에서 어떤 정점을 먼저 꺼내는지에 의해 결정된**다.

동작 과정이 그렇게 직관적이지 않은 깊이 우선 탐색에 비해, 너비 우선 탐색의 동작 과정은 아주 이해하기 쉽다. 너비 우선 탐색은 시작점에서 가까운 정점부터 순서대로 방문하는 탐색 알고리즘이기 때문이다.

이를 구현하는 간단한 방법은, 목록에 먼저 넣은 정점을 항상 먼저 꺼내는 것이다. 시작점에서 멀리 있는 정점일수록 나중에 목록에 추가되기 때문이다. 따라서 정점 목록으로 큐를 사용하면 너비 우선 탐색의 조건을 만족시킬 수 있다.

![https://upload.wikimedia.org/wikipedia/commons/thumb/3/33/Breadth-first-tree.svg/1200px-Breadth-first-tree.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/3/33/Breadth-first-tree.svg/1200px-Breadth-first-tree.svg.png)

[https://upload.wikimedia.org/wikipedia/commons/thumb/3/33/Breadth-first-tree.svg/1200px-Breadth-first-tree.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/3/33/Breadth-first-tree.svg/1200px-Breadth-first-tree.svg.png)

Queue를 이용한 BFS

```python
def bfs(start_v):
    discovered = [start_v]
    queue = [start_v]
    while queue:
        v = queue.pop(0)
        for w in graph[v]:
            if w not in discovered:
                discovered.append(w)
                queue.append(w)
    return discovered
```

---

## 참조

<구종만, 알고리즘 문제해결전략, 인사이트>

<박상길, 파이썬 알고리즘 인터뷰, 책만>
