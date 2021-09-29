### 해를 찾아가는 도중, 지금의 경로가 해가 될 것 같지 않으면

### 그 경로를 더이상 가지 않고 되돌아간다

이를 가지치기 라고 하며, 불필요한 부분을 쳐내고 최대한 올바른 쪽으로 간다

⇒ 가지치기를 얼마나 잘하느냐에 따라 효율성이 결정된다 

즉,

- 모든 경우의 수에서 `특정 조건을 만족하는 경우`만 살펴본다
- 답이 될만한지 판단하고 그렇지 않으면 탐색을 더 하지 않고 가지치기 한다
- DFS 에서 많이 사용된다

## 대표적인 문제로는 N-queens 문제 존재

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b5f3b087-061c-451c-99ba-1a20ca77b6a0/Untitled.png)

1) 같은 행에 두개의 queen이 있어선 안된다

2) 같은 열에도 안된다 

3) 같은 대각선에도 안된다 

예) 4-queen일 때

(x1, x2, x3, x4) = (2, 4, 1, 3)

### 상태 공간 트리

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/95b0586c-abaf-4922-9fde-a55a31388c84/Untitled.png)

⇒ 모든 가능한 해를 트리로 구성한 것

- 문제의 state space( 상태 공간을 분석)
- 탐색시, 해로 도달하지 못하는 것을 발견하면 상태 공간을 이용해서 탐색이 필요 없는 부분 제외

```python
#구하고자 하는 정답들(x1,x2...xn)을 배열 x[1], x[2],x[3]에 저장한다
Algorithm backtrack(x,k,n)
#x[1]~x[k-1]이 정해진 상태에서 x[k]~x[n]을 구한다

fo each x[k] such that x[k] ㅌ T(x[1]...x[k-1])
 if(Bk(x[1],x[2]...x[k])
		if(x[1]...x[k])is a path to answer node,
			output x[1],x[2]....x[k]
			return
		else if(k<n)
			Backtrack(k+1)
```