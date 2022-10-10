# 하위 코루틴 반환값 가져오기

[티스토리 블로그 바로 가기](https://kyleeee.tistory.com/entry/TIL35-%EC%BD%94%EB%A3%A8%ED%8B%B4-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B056-%ED%95%98%EC%9C%84-%EC%BD%94%EB%A3%A8%ED%8B%B4-%EB%B0%98%ED%99%98%EA%B0%92-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)

<br>

## yield from으로 값을 여러 번 바깥으로 전달하기

<br>

일반적으로 yield를 통해 값을 하나씩 전달했습니다. 대표적으로 ```__next__()``` 메소드를 이용하기도 했습니다.

예시 함수 하나 보겠습니다.

<br>

```python
def number_generator():
  x = [1,2,3]
  for i in x:
    yield i

for i in number_generator():
  print(i)
```

<br>

이렇게 했을 때 

1 <br>
2 <br>
3

이렇게 값이 출력 됩니다.

<br>

이러한 경우, 매번 반복문을 사용하지 않고 yield from을 사용하면 됩니다. yield from에는 반복 가능한 객체, 이터레이터, 제너레이터 객체를 지정합니다.(Python 3.3부터 사용 가능)

<br>

그러면 예시 하나 보겠습니다.

```python
def number_generator():
  x = [1,2,3]
  yield from x

for i in number_generator():
  print(i)
```

<br>

## yield from에 제너레이터 객체 지정하기

<br>

yield from에 제너레이터 객체를 지정해보겠습니다

```python
def number_generator(stop):
    n = 0
    while n < stop:
        yield n
        n += 1
 
def three_generator():
    yield from number_generator(3)    # 숫자를 세 번 바깥으로 전달
```

<br>

number_generator()를 통해 n을 계속 증가시켜 값을 만들어냅니다.

만들어진 값을 이용해서 그걸 제너레이터로 만들업니다.

이 부분이 이번 하위 코루틴 반환값 가져오기에 필요한 기본으로 깔려야 될 지식입니다.

<br>

## 하위 코루틴의 반환값 가져오기

<br>

```python
def accumulate():
    total = 0
    while True:
        x = (yield)         # 코루틴 바깥에서 값을 받아옴
        if x is None:       # 받아온 값이 None이면
            return total    # 합계 total을 반환
        total += x
 
def sum_coroutine():
    while True:
        total = yield from accumulate()    # accumulate의 반환값을 가져옴
        print(total)
 
co = sum_coroutine()
next(co)
 
for i in range(1, 11):    # 1부터 10까지 반복
    co.send(i)            # 코루틴 accumulate에 숫자를 보냄
co.send(None)             # 코루틴 accumulate에 None을 보내서 숫자 누적을 끝냄
 
for i in range(1, 101):   # 1부터 100까지 반복
    co.send(i)            # 코루틴 accumulate에 숫자를 보냄
co.send(None)             # 코루틴 accumulate에 None을 보내서 숫자 누적을 끝냄
```

<br>

accumulate()의 경우, 값을 계속 받되 None을 받을 경우 합계를 리턴하고 반복문을 종료합니다.

sum_coroutine()의 경우, accumulate() 반환값을 받아 total에 저장하여 출력합니다.

10번의 반복문을 돌려 send()로 값을 보내면 accumulate()의 total에 계속 누적이 됩니다.

그리고 None을 보낼 경우, 1~10까지 합계인 55가 출력되고, sum_coroutine()의 total에 저장되어 값이 출력 됩니다.

아래 1~101까지의 반복문도 같은 원리로 작동합니다.

<br>

## StopIteration 예외 발생시키기

<br>

```oython
def accumulate():
    total = 0
    while True:
        x = (yield)                       # 코루틴 바깥에서 값을 받아옴
        if x is None:                     # 받아온 값이 None이면
            raise StopIteration(total)    # StopIteration에 반환할 값을 지정(파이썬 3.6 이하)
        total += x
 
def sum_coroutine():
    while True:
        total = yield from accumulate()    # accumulate의 반환값을 가져옴
        print(total)
 
co = sum_coroutine()
next(co)
 
for i in range(1, 11):    # 1부터 10까지 반복
    co.send(i)            # 코루틴 accumulate에 숫자를 보냄
co.send(None)             # 코루틴 accumulate에 None을 보내서 숫자 누적을 끝냄
 
for i in range(1, 101):   # 1부터 100까지 반복
    co.send(i)            # 코루틴 accumulate에 숫자를 보냄
co.send(None)             # 코루틴 accumulate에 None을 보내서 숫자 누적을 끝냄
```

<br>

코루틴도 제너레이터이므로 return을 사용하면 StopIteration이 발생합니다. 그래서 raise StopIteration(value) 처럼 사용할 수 있습니다.

단, Python 3.6이하만 사용한다고 합니다. 3.7부터는 raise StopIteration 발생 시, RuntimeError가 되어 사용할 수 없다고 합니다.

저는 3.8.10버전을 쓰는데, 진짜 그런지 한 번 해봤습니다.

<br>

정말 RuntimeError가 발생하는 걸 확인했으니 return을 쓰도록 합시다.

![image](https://user-images.githubusercontent.com/88086271/194825435-062c3403-abb5-4fa1-830a-f2437f76d7bc.png)
