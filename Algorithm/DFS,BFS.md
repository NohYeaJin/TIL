- DFS

⇒ 시작 정점 부터 방문하고 현재 방문하고 있는 정점 V와 인접한 정점을 하나씩 검사하면서 아직 방문되지 않은 (V와 인접한) 정점 W가 있으면 W를 방문한다

⇒ 이 과정을 반복하면서 더 이상 갈 곳이 없는 정점 (인접한 정점을 모두 방문한 경우) 에 도달하면 

**마지막에 따라왔던 간선을 따라 뒤로 돌아가서 (back track)** 인접한 정점을 방문하는 탐색을 반복

```python
Algorithm dfs(graph g, int v) //시작 정점이 v
	v를 방문하고 이를 방문했다고 표시
		// v를 방문할 때 필요한 작업 수행
		v와 인접한 각 정점 w에 대해서
			만약 w가 방문되지 않았다면 dfs(g,w)
			// w로부터 backtrack 할 때 필요한 작업 수행
v가 종료되었다고 표시
//dfs 마치고 필요한 작업 수행
```

- 정점 v의 상태

(1) 아직 v를 방문하지 않음

(2) v를 처음으로 방문

(3) v와 인접한 정점을 모두 방문 (v에서 시작한 dfs는 종료된 상태)

⇒ 1,2만 필요할 수도 또는 1,2,3 모두 필요할 수도

- Algorithm dfsVisit(g)

⇒ G의 모든 정점을 "미방문" 상태로 초기화

⇒ G의 각 정점 v에 대하여

만약 v가 미방문 상태라면,  

dfs(g,v)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/63dc7143-91c9-4462-8ba4-b5ad4f0860b8/Untitled.png)

```python
# 단순 리스트로 풀기

graph = {
    'A': ['B'],
    'B': ['A', 'C', 'H'],
    'C': ['B', 'D'],
    'D': ['C', 'E', 'G'],
    'E': ['D', 'F'],
    'F': ['E'],
    'G': ['D'],
    'H': ['B', 'I', 'J', 'M'],
    'I': ['H'],
    'J': ['H', 'K'],
    'K': ['J', 'L'],
    'L': ['K'],
    'M': ['H']
}

def dfs(graph, start_node):
      visit = list()
      stack = list()
 
      stack.append(start_node)
 
      while stack:
          node = stack.pop()
          if node not in visit:
              visit.append(node)
              stack.extend(graph[node])
 
     return visit
```

```python
# 재귀로 풀기

graph = dict()
 
graph['A'] = ['B', 'C']
graph['B'] = ['A', 'D']
graph['C'] = ['A', 'G', 'H', 'I']
graph['D'] = ['B', 'E', 'F']
graph['E'] = ['D']
graph['F'] = ['D']
graph['G'] = ['C']
graph['H'] = ['C']
graph['I'] = ['C', 'J']
graph['J'] = ['I']

def dfs_recursive(graph, start, visited = []):
## 데이터를 추가하는 명령어 / 재귀가 이루어짐 
    visited.append(start)
 
    for node in graph[start]:
        if node not in visited:
            dfs_recursive(graph, node, visited)
    return visited
```

```python

# deque로 풀기

graph = dict()
 
graph['A'] = ['B', 'C']
graph['B'] = ['A', 'D']
graph['C'] = ['A', 'G', 'H', 'I']
graph['D'] = ['B', 'E', 'F']
graph['E'] = ['D']
graph['F'] = ['D']
graph['G'] = ['C']
graph['H'] = ['C']
graph['I'] = ['C', 'J']
graph['J'] = ['I']

def dfs2(graph, start_node):
    ## deque 패키지 불러오기
    from collections import deque
    visited = []
    need_visited = deque()
    
    ##시작 노드 설정해주기
    need_visited.append(start_node)
    
    ## 방문이 필요한 리스트가 아직 존재한다면
    while need_visited:
        ## 시작 노드를 지정하고
        node = need_visited.popleft()
 
        ##만약 방문한 리스트에 없다면
        if node not in visited:
 
            ## 방문 리스트에 노드를 추가
            visited.append(node)
            ## 인접 노드들을 방문 예정 리스트에 추가
            need_visited.extend(graph[node])
                
    return visited
```

- 인접행렬로

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8693301d-3e6d-458e-94ac-6c55b5786625/Untitled.png)

- BFS 코드

```python
graph_list = {1: set([3, 4]),
              2: set([3, 4, 5]),
              3: set([1, 5]),
              4: set([1]),
              5: set([2, 6]),
              6: set([3, 5])}
root_node = 1

from collections import deque

def BFS_with_adj_list(graph, root):
    visited = []
    queue = deque([root])

    while queue:
        n = queue.popleft()
        if n not in visited:
            visited.append(n)
            queue += graph[n] - set(visited)
    return visited
```

```python
def bfs(graph, start_node):
    visit = list()
    queue = list()
    queue.append(start_node)
    while queue:
        node = queue.pop(0)
        if node not in visit:
            visit.append(node)
            queue.extend(graph[node)
            #print(node,end=' ')
            #if(node in graph):
                #queue = queue+graph[node]
    return visit
```