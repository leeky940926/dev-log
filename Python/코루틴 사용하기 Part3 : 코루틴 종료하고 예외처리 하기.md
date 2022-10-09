# 코루틴 종료하고 예외처리 하기

<br>

[티스토리 블로그 바로 가기](https://kyleeee.tistory.com/entry/TIL35-%EC%BD%94%EB%A3%A8%ED%8B%B4-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B036-%EC%BD%94%EB%A3%A8%ED%8B%B4-%EC%A2%85%EB%A3%8C%ED%95%98%EA%B3%A0-%EC%98%88%EC%99%B8-%EC%B2%98%EB%A6%AC%ED%95%98%EA%B8%B0)

<br>

보통 코루틴은 실행 상태 유지를 위해 while True를 사용합니다. 만약 코루틴을 종료하고 싶으면 close 메소드를 사용합니다.

코루틴에 숫자 20개를 보낸 후 종료해보겠습니다.

<br>

```python
def number_coroutine():
    while True:
        x = (yield)
        print(x, end=' ')
 
co = number_coroutine()
next(co)
 
for i in range(20):
    co.send(i)
 
co.close()    # 코루틴 종료
```

<br>

이렇게 하면 0~19까지 출력되고 종료됩니다.

만약 close()가 없다면 co.send() 를 계속 보낼 수 있으며, 종료 후 send()를 했을 떄 

파이썬 스크립트가 끝나면 코루틴도 끝나기 때문에 close를 사용하지 않는 것과 별 차이가 없습니다. 하지만 코루틴의 종료 시점을 알아야 할 때 사용하면 편리합니다.

StopIteration이라는 문구가 떠서 종료되었음을 알려줍니다.

<br>

![image](https://user-images.githubusercontent.com/88086271/194748820-c8096c37-ff90-49ad-8ec9-498025fcd61f.png)

<br>

## GeneratorExit 예외 처리하기

<br>

코루틴 객체에서 close() 메소드를 호출하면 코루틴이 종료될 때 GeneratorExit 예외가 발생합니다. 따라서 이 예외를 처리하면 코루틴의 종료 시점을 알 수 있습니다. <br>

```python
def number_coroutine():
    try:
        while True:
            x = (yield)
            print(x, end=' ')
    except GeneratorExit:    # 코루틴이 종료 될 때 GeneratorExit 예외 발생
        print()
        print('코루틴 종료')
 
co = number_coroutine()
next(co)
 
for i in range(20):
    co.send(i)
 
co.close()
```

이렇게 해서 '코루틴 종료' 부분에 원하는 코드를 작성하면 됩니다.

<br>

## 코루틴 안에서 예외 발생시키기

<br>

코루틴 안에서 특정 예외를 발생시킬 수 있습니다. throw 메소드를 사용하며 말 그대로 에러를 코루틴 안으로 던진다는 의미입니다.

이 때 throw 메소드에 지정한 에러메시지는 exept as 변수에 들어갑니다.

<br>

```python
def sum_coroutine():
    try:
        total = 0
        while True:
            x = (yield)
            total += x
    except RuntimeError as e:
        print(e)
        yield total    # 코루틴 바깥으로 값 전달
 
co = sum_coroutine()
next(co)
 
for i in range(20):
    co.send(i)
 
print(co.throw(RuntimeError, '예외로 코루틴 끝내기')) # 190
```

<br>

이와 같이, throw를 통해 메시지를 정하면 코루틴 안에서 예외가 발생합니다. 

즉, '예외로 ... 끝내기'가 e에 담겨서 print되는 형태입니다.

만약 except RuntimeError 선언 후 yield total만 쓴다면 co.throw()를 통해 메시지를 선정해도 나오지 않습니다.










