# 코루틴(Coroutines)

회사에서 FastAPI 도입을 앞두고 있어, 코루틴을 시작으로 비동기 프로그래밍에 대해 공부하려고 합니다.

모든 출처는 [파이썬 코딩도장 코루틴(Coroutine)](https://dojang.io/mod/page/view.php?id=2469) 입니다.

<br>

## Coroutine

우선, 코루틴을 설명하기에 앞서 이해를 돕기 위한 함수 하나를 만들어보겠습니다.

```python
def add(a, b):
    c = a + b    # add 함수가 끝나면 변수와 계산식은 사라짐
    print(c)
    print('add 함수')
 
def calc():
    add(1, 2)    # add 함수가 끝나면 다시 calc 함수로 돌아옴
    print('calc 함수')
 
calc()
```

calc() 함수를 실행하면 add(1,2)함수가 실행되어 1+2의 값인 3이 출력된 후 'add함수'라는 문구가 출력됩니다.
그리고 'calc'함수라는 문구가 실행됩니다.

여기서 calc()와 add()의 관계를 보면, calc()가 메인 루틴이며 add()는 서브루틴입니다.

이미지로 보면 아래와 같습니다.

<img width="722" alt="image" src="https://user-images.githubusercontent.com/88086271/194742364-e5f41d06-9abc-44c3-9b29-1a63b586917b.png">

<br>

메인 루틴에서 서브 루틴을 호출하면 서브 루틴의 코드를 실행한 뒤, 다시 메인 루틴으로 돌아옵니다.

특히, 서브 루틴이 끝나면 서브 루틴의 내용은 모두 사라집니다. 메인 루틴에 종속되었기 때문입니다.

하지만, 코루틴의 경우 조금 다릅니다.

코루틴(Coroutine)은 Cooperative routine을 의미하는데, 서로 협력하는 루틴이라는 의미로, 종속 관계가 아닌 대등한 관계이며 특정 시점에 상대방 코드를 실행한다는 의미입니다.

이미지화하면 아래와 같습니다

<img width="702" alt="image" src="https://user-images.githubusercontent.com/88086271/194742451-99adc8f3-7ffc-4cca-9b3c-9a5c7aff4c86.png">


<br>

이처럼 코루틴은 함수가 종료되지 않은 상태에서 메인 루틴의 코드를 실행한 뒤, 다시 돌아와 코루틴 코드를 실행합니다.

따라서, 코루틴이 종료된 것이 아니어서 코루틴의 내용도 그대로 유지됩니다.

일반 함수를 호출하면 코드를 한 번만 실행할 수 있는데, 코루틴은 여러 번 실행가능합니다. 함수의 코드를 실행하는 지점을 진입점(entry point)라고 하는데, 코루틴은 진입점이 여러 개인 함수입니다.

<br>

## 코루틴에 값 보내기

<br>

코루틴은 제너레이터의 특별한 형태입니다. 제너레이터는 yield로 값을 발생시켰지만 코루틴은 yield로 값을 받아올 수 있습니다.
다음과 같이 코루틴에 값을 보내면서 코드를 실행할 땐 send 메소드를 사용합니다. 그리고 send를 통해 보낸 값을 받아오려면 (yield) 형식으로 yield를 괄호를 묶어준 뒤 변수에 저장합니다.

```python
def number_coroutine():
    while True:        # 코루틴을 계속 유지하기 위해 무한 루프 사용
        x = (yield)    # 코루틴 바깥에서 값을 받아옴, yield를 괄호로 묶어야 함
        print(x)
 
co = number_coroutine()
next(co)      # 코루틴 안의 yield까지 코드 실행(최초 실행)
 
co.send(1)    # 코루틴에 숫자 1을 보냄
co.send(2)    # 코루틴에 숫자 2을 보냄
co.send(3)    # 코루틴에 숫자 3을 보냄
```

이렇게 되면 1,2,3이 차례대로 출력됩니다.

일반적으론 number_coroutine()에는 파라미터가 없는데 어떻게 전달되지? 라는 생각이 들 수 있습니다.

그래서 이 부분을 간단하게 설명하면

<br>

while True로 코루틴 종료를 막아 줍니다(종료 방법은 차후 기술), 그리고 x = (yield)와 같이 코루틴 바깥에서 보낸 값을 받아서 x에 저장하고 print로 출력합니다.

코루틴의 바깥에선 co = number_coroutine()과 같이 코루틴 객체를 생성한 뒤, next(co)로 코루틴 안의 yield까지 코드로 실행합니다.
```__next__()```를 통해 호출해도 상관없습니다.

next()는 코루틴 함수 실행을 해주기 위한 함수로 반복 가능 객체의 다음 요소를 리턴합니다.

만약 co = number_coroutine()만 쓰고 next(co)를 안 썼다면 send() 했을 때 TypeError: can't send non-None value to a just-started generator가 발생합니다.

첫 번째 yield표현식은 최초 yield가 발생한 다음 일어나는데, 아직 yield가 발생하지 않았기 때문입니다.
즉, 필요할 때 사용하기 위한 값 혹은 값의 형태가 정해지지 않았기 때문에 할 수 없다고 생각하면 될 것 같습니다. <br>
![image](https://user-images.githubusercontent.com/88086271/194743930-b598a532-6d91-4377-b34a-a2f6f734f11c.png)

<br>

그리고 send()로 보낼 때 값은 하나만 보낼 수 있습니다.

```python
 def number_coroutine():
    ...:     while True:        # 코루틴을 계속 유지하기 위해 무한 루프 사용
    ...:         x = (yield)    # 코루틴 바깥에서 값을 받아옴, yield를 괄호로 묶어야 함
    ...:         y = (yield)
    ...:         print(x,y)
    ...:
    ...: co = number_coroutine()
```

설정한 뒤, co.send(1,2) 이렇게 보냈을 때 TypeError: send() takes exactly one argument (2 given) 가 발생하며 메시지로 하나의 argument만 받을 수 있다고 나와 있습니다.

<br>


number_coroutine()은 내부에 while문을 통해 iterable하다는 걸 확인할 수 있지만 초기 값이 없어 None이 리턴됩니다.

그래서 next()를 통해 실행하기도 했지만 co.sned(None)과 같이 None을 지정해도 코드를 최초로 실행할 수 있습니다.

그 다음, co.send()로 숫자 1,2,3을 보내면 코루틴 안에서 숫자를 받은 뒤 print로 출력합니다.

이미지화하면 아래와 같이 됩니다 <br>
<img width="759" alt="image" src="https://user-images.githubusercontent.com/88086271/194743027-a4be760e-c2d5-446e-a893-ccfecd88771c.png">

<br>

메인 루틴에서 co.send(1)을 제너레이터로 보내면 코루틴은 대기상태가 풀리고, x = (yield)를 통해 print(x)가 출력됩니다. 
여기서 send()는 제너레이터로 값을 보내는 메소드라고 인식할 수 있습니다.
그리고 while True의 구분이기 때문에 yield 상태에서 대기하고, 다시 메인 루틴으로 돌아옵니다.
