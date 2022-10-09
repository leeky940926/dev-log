## 코루틴 바깥으로 값 전달하기

<br>

이전에 코루틴 안에서 값을 보냈는데, 밖으로 값을 전달해보겠습니다.

로직을 먼저 간단하게 이야기하면, yield 형식으로 yield에 변수를 지정한 뒤, 괄호를 묶어주면 바깥으로 값을 전달합니다.

그리고 yield를 사용하여 바깥으로 전달한 값은 next 함수와 send 메서드의 반환값으로 나옵니다.

```python
def sum_coroutine():
    total = 0
    while True:
        x = (yield total)    # 코루틴 바깥에서 값을 받아오면서 바깥으로 값을 전달
        total += x
 
co = sum_coroutine()
print(next(co))      # 0: 코루틴 안의 yield까지 코드를 실행하고 코루틴에서 나온 값 출력
 
print(co.send(1))    # 1: 코루틴에 숫자 1을 보내고 코루틴에서 나온 값 출력
print(co.send(2))    # 3: 코루틴에 숫자 2를 보내고 코루틴에서 나온 값 출력
print(co.send(3))    # 6: 코루틴에 숫자 3을 보내고 코루틴에서 나온 값 출력
```

<br>

우선, sum_coroutine()에서 total초기값을 0으로 설정했기 때문에 next(co)를 했을 때 None이 아닌 0이 출력됩니다.

그리고 next(co)를 통해 메인 루틴 실행 후, co.send(1) 했을 때 total=1이 되고 코루틴은 total=1이라는 값을 잡고 대기하게 됩니다.

일반적으로 함수를 실행하고 종료하면 값이 초기화가 되는데, 코루틴의 특징을 통해 대기 상태가 되어 값을 유지할 수 있게 되는 것입니다.

 <br>

next()와 send()에 대해 추가적으로 더 살펴보면, next는 코드는 실행하되 값을 보내지 않을 때 사용하고, send()는 값을 보내면서 코루틴 코드를 실행할 때 사용합니다.

그래서 next(co) 이렇게 쓰는 것도 실행을 하지만 값을 보내지 않기 때문에 이렇게 사용할 수 있는 것입니다.

<br>

그리고 send()를 통해 값을 보내면 코루틴은 대기상태가 풀려서 x = (yield total) 부분이 실행되고 total에 값을 추가합니다.

이러한 과정으로 (yield total)이 바깥으로 전달한 값을 next와 send의 반환값으로 받게 됩니다.

즉, yield를 사용하여 코루틴 바깥으로 전달하면 next와 send의 반환값으로 받는다는 점을 핵심으로 기억하면 됩니다.

<br>

## yield에 ()가 없다면 어떻게 될까?

int로 보낸 값을 ()에 넣었을 때, ()안에 ","가 없으면 타입이 tuple이 아닌 int로 되는데 yield에 ()가 없어도 되지 않을까? 란 생각이 들었습니다.

```python
 def sum_coroutine():
    ...:     total = 0
    ...:     while True:
    ...:         x = yield total    # 코루틴 바깥에서 값을 받아오면서 바깥으로 값을 전달
    ...:         print(type(x))
    ...:         total += x
    ...:
    ...: co = sum_coroutine()
    ...: print(next(co))      # 0: 코루틴 안의 yield까지 코드를 실행하고 코루틴에서 나온 값 출력
```

<br>

```python
def sum_coroutine():
    ...:     total = 0
    ...:     while True:
    ...:         x = (yield total)    # 코루틴 바깥에서 값을 받아오면서 바깥으로 값을 전달
    ...:         print(type(x))
    ...:         total += x
    ...:
    ...: co = sum_coroutine()
    ...: print(next(co))      # 0: 코루틴 안의 yield까지 코드를 실행하고 코루틴에서 나온 값 출력
```

<br>

이렇게 했을 때 타입은 그대로 int로 나오고 값 출력 및 send로 보내는 건 이상없었습니다.

아직 어려운 함수를 다루는 게 아니기 때문에, 지금 현재로선 ()를 제거해도 동작엔 이상없다고 생각해도 될 것 같습니다.

