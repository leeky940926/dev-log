# 야매로 공부한 Python async

현재 회사에서 Django -> FastAPI로 전환을 목표로 천천히 작업하고 있습니다.

저 역시 일부 기능을 FastAPI로 구현했는데, 비동기 프로그래밍이 매우 어려웠습니다.

그래서 이번엔 제가 배운 파이썬 비동기 프로그래밍에 대해 작성해보겠습니다.

[티스토리 블로그 바로가기](https://kyleeee.tistory.com/entry/TIL40-FastAPI에서-야매로-공부한-Python-async)

<br>

## 동기/비동기

우선, 동기/비동기에 대해 알아보겠습니다.

인터프리터 언어로 한 줄씩 읽으며 내려오는 파이썬 특성상 멀티 쓰레드가 되지 않습니다.

그래서 하나의 쓰레드에서 동시 처리를 할 수 있는 비동기 프로그래밍의 중요성이 커졌습니다.

<br>

그러면 동기와 비동기가 뭘까라고 생각하시는 분들이 있을텐데 가장 많이 드는 예시인 커피집 주문을 이야기 해보겠습니다.

동기는 커피 주문 후 커피가 나올 때까지 줄 서서 기다리는 것을 의미하고, 비동기는 커피 주문만 하고 진동벨을 가지고 가서 기다리다가 벨이 울리면 가지러 가는 걸 의미합니다.

즉, 동기식은 순차적으로 대기를 하여 실행하게 되고, 비동기는 대기하지 않고 다른 작업을 할 수 있는 것이 큰 특징입니다.

<br>

이제 의미를 알았으니 코드로 구현해보겠습니다.

<br>

## async

일반적으로 함수를 선언할 때 아래와 같은 형식으로 작성하게 됩니다.

```python
def method_name():
  return value
```

<br>

여기서 앞에 async를 붙이면 이 함수는 비동기로 작동하게 되며, 코루틴 객체를 리턴합니다.

```python
#비동기 함수 선언
async def get_hihi():
  return {"message":"hihi"}

#함수 호출
get_hi()

#결과
<coroutine object get_hi at 0x109ee29c0>
```

<br>

저는 ```{"message":"hihi"}```를 보고 싶은데, 코루틴 객체로 리턴되었습니다. 어떻게 해야 값을 확인할 수 있을까요?


<br>

## 다른 함수에서 await를 이용하여 값 받기

비동기 함수는 async로 선언된 다른 비동기 함수에서 ```await```를 이용해 호출해야 합니다.

```python
async def main():
  value = await get_hihi()
  return value
```

<br>

이렇게 await를 통해 함수 호출해서 값을 받은 뒤 전달할 수 있습니다.

그래서 그 값을 리턴하는 것입니다.

그런데 main 함수도 async기 때문에 호출 시, 코루틴 객체를 리턴합니다.

```python
#main함수 호출
main()

#결과로 코루틴 객체 리턴
<coroutine object main at 0x109c79540>
```

<br>

## asyncio

이렇게 값을 받았는데, 확인하지 못 하면 어떻게 해야 할까요?

바로 ```asyncio```를 사용하는 것입니다.

우선, ```asyncio```를 설치합니다.

```shell
pip install asyncio
```

<br>

```python
import asyncio

# main함수 선언
async def main():
  value = await get_hihi()
  return value

#asyncio로 실행
asyncio.run(main())

#결과 리턴
{"message":"hihi"}
```

<br>

이렇게 ```asyncio```를 사용하는데, 다른 방법도 있습니다.

큰 작업일 경우, 하나의 작업을 큰 루프로 간주해서 실행시키는 것입니다.


```python
async def get_hihi():
  return {"message":"hihi"}

loop = asyncio.new_event_loop()
loop.run_until_complete()
loop.close()
```

<br>

```new_event_loop()``` 대신 ```get_event_loop()```를 사용할 수 있는데, 저는 ```loop.close```때문에 전자를 사용합니다.

이게 왜 문제가 되냐면 

```python
async def get_hihi():
  return {"message":"hihi"}
  
def main_get_hihi():
  loop = asyncio.get_event_loop()
  loop.run_until_complete(get_hihi())
  loop.close()

async def get_bye():
  return {"message":"bye"}

def main_get_bye():
  loop = asyncio.get_event_loop()
  loop.run_until_complete(get_bye())
  loop.close()

def main():
  main_get_hihi()
  main_get_bye()
  return "finish"
```

<br>

이렇게 했을 때 main()함수에서 main_get_hihi()를 실행하고 main_get_bye()를 실행할 때, main_get_hihi()에서 루프를 닫았기 때문에 ```RuntimeError: Event loop is closed```에러가 발생합니다. 각기 다른 함수에서 ```get_event_loop()```를 선언해도 다음 작업에 영향을 줘서 끊기게 됩니다.

하지만 ```new_event_loop()```를 사용한다면 새로운 비동기 함수마다 루프가 생성돼서 독립적으로 작업을 수행할 수 있게 됩니다.

<br>

이렇게 비동기 프로그래밍 방법에 대해 알아봤습니다.

정말 기본적인 문법이고 할 것이 많아 꾸준히 공부해야 할 것 같습니다.

