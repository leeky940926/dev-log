
## 혼자해보는 코루틴 작동 순서

<br>

[티스토리 블로그 바로 가기](https://kyleeee.tistory.com/entry/TIL35-%EC%BD%94%EB%A3%A8%ED%8B%B4-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B046-%EC%A7%A7%ED%98%BC%EC%9E%90%ED%95%B4%EB%B3%B4%EB%8A%94-%EC%BD%94%EB%A3%A8%ED%8B%B4-%EC%9E%91%EB%8F%99-%EC%88%9C%EC%84%9C)

<br>

이 부분은 파이썬 코딩도장엔 없고, 혼자 궁금해서 해보고 작성하게 되었습니다.

만약 여러 개의 yield가 들어가면 끊어져서 나올텐데 실제로는 어떻게 나올까? 하는 궁금증이 생겨서 직접 해봤습니다.


<br>

```python
def sum_coroutine():
  try:
    total = 0
    while True:
      print("*"*10, "Not yield")
      x = (yield)
      print("*"*10, "X yield", x)
      total += x
      yield total
      print("*"*10, "total yield", total)
  except RuntimeError as e:
    yield total
```

<br>

위와 같이 계속 send()를 통해 값을 보냈을 때 진행상황이 궁금해서 계속 해봤습니다.

```python
co = sum_coroutine()
next(co)
```

<br>

![image](https://user-images.githubusercontent.com/88086271/194817211-9b388d7f-bcdd-49c3-bca6-443cb428dc64.png)

<br>

1. yield를 발생시키기 위해 ```next(co)```를 입력합니다.
2. 그 다음부터 계속 ```send(1)```을 보냈습니다.
3. 39번째 줄에서 1을 처음 보냈을 때, x에 1이 저장되고 대기상태에 들어가며 print()를 실행합니다.
4. 40번째 줄에서 1을 한 번 더 보내면 1이 더해져서 나오게 되고 다시 x를 받기전까지 대기상태에 들어갑니다.

<br>

계속 아래와 같이 반복하여 대기상태에 들어가며 직접 찍어보면서 흐름을 더 자세히 파악할 수 있었습니다.
