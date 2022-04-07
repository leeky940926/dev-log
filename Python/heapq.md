# heapq

[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL23-heapq)

<br>


오늘 포스팅 주제는 파이썬의 heapq 알고리즘입니다.

백준을 풀다가 알게 된 개념인데, 익숙치 않아 정리하려고 합니다.

출처는 [파이썬 공식문서](https://docs.python.org/ko/3/library/heapq.html)입니다.

<br>

✔️ What is heapq?

heapq는 우선순위 큐 알고리즘 구현을 제공합니다.
힙이란 부모노드가 자식노드보다 작거나 같은 값을 갖는 이진 트리를 뜻하며, heap[k] <= heap[2k+1] or heap[k] <= heap[2k+2] 배열을 사용한다고 합니다. 그리고 비교를 위해 존재하지 않는 heapq가 있다면, 그 값은 무한으로 간주한다고 합니다.

트리로 그린다면

```
     1
  2     3
4   5  6  7   
```
이런 식의 익숙한 그림이 보여지게 되는 것입니다.

이제, heapq에서 제공하는 메서드들에 대해 알아보겠습니다.

<br>

✔️ heappush

push는 일반적으로 무언가를 추가할 때 많이 사용하는 용어로, heap에다가 요소를 넣겠다는 의미를 가진 메소드입니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdqFKms%2FbtryKusXk8A%2FRFjTNdGWo4TV69G1bOVyV1%2Fimg.png)


예를 들어 a라는 빈 배열에 heappush(a,1)이라고 하면 a에 1이라는 요소를 넣겠다는 의미이고, a를 출력하면 1이 추가된 리스트가 나오는 걸 확인할 수 있습니다.

<br>

✔️ heappop

pop은 일반적으로 제거할 때 사용하는 용어로, heap의 가장 작은 요소를 제거하겠다는 의미입니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FceQ4hX%2FbtryJwdJvqa%2Fwvm5fm790rBHQVsGLQBPT1%2Fimg.png)


제거할 때, a에서 1을 제거하겠다는 의미로 인수를 두 개 넣었는데 하나의 인수만 가능하다는 에러가 났습니다.

그래서 a만 넣고 해보니 1이 나갔다는 의미로 출력이 된 걸 확인할 수 있습니다. 공식문서에서는 빈 리스트에서 heappop을 하면 IndexError가 발생한다고 해서 진짜 발생하는지 해봤는데 정말로 index out of range에러가 발생했습니다.

<br>

✔️ heappushpop

요소를 push한 다음 가장 작은 요소를 제거하겠다는 의미입니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FytuzD%2FbtryKu0OgdR%2FrAQGRtBRQ9xDMmobTbrEZK%2Fimg.png)

빈 배열이 된 a에 1,2를 추가한 뒤 pop을 통해 1을 제거합니다. 그렇게 되면 a=[2]가 되고, pushpop을 통해 3을 추가한 뒤 제일 작은 2가 제거됩니다. 혹시 같은 수를 넣으면 어떻게 될까해서 [3]만 있는 a에 pushpop(a, 3)을 했더니 3하나가 들어오고 나가서 [3] 그대로 있게 됩니다.

<br>

✔️ heapify

리스트를 힙 형태로 변환하는 메소드입니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FceFHfR%2FbtryIpzDw8Z%2FwXHh73N3kwHnvetM7aKZck%2Fimg.png)

a를 [3, 4, 1, 6, 7, 2, 2]로 선언하고 heapify해주면 최소값이 0번째 인덱스가 되며 heap으로 바꿔줍니다.

우선 1을 기준으로 그 다음 작은 값인 2가 먼저 자식노드로 생성됩니다.
```
    1
2
```

그 다음 낮은 값인 2가 2의 자식으로 생성됩니다.(부모는 자식보다 작거나 같으므로)

```
        1
            2
        2
```

그 다음 3이 2의 자식으로 가서 이진트리를 완성합니다

```
         1
            2
         2    3
```

이제 4가 2와 같은 레벨로 생성됩니다

```
          1
     4        2
            2    3
```

그리고 6이 4의 자식으로 생성됩니다.

```
         1
     4        2
  6         2    3
```

마지막으로 7이 생성되며 트리가 완성됩니다

```
          1
     4          2
  6   7      2    3
```

이렇게 값이 [1, 4, 2, 6, 7, 2, 3] 이렇게 나오게 되는 것입니다.

<br>

✔️ heapreplace

가장 적은 항목을 리턴하고, 새로운 요소를 푸쉬합니다. 힙이 비어있다면 IndexError가 발생합니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FuvmAZ%2FbtryI6fDOTr%2Fa7qyN5VzHNGMTLvA2OX9i1%2Fimg.png)

a에서 가장 작은 값은 1이고 그 자리에 9가 들어간 걸 확인할 수 있습니다.


여기까지가 기본적인 heapq에 대한 메소드들입니다.

아직 익숙치가 않아 계속 사용해봐야겠습니다.
