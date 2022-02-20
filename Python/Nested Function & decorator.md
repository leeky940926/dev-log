# Nested Function & decorator

[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL8-Nested-Function-decorator)

<br>

##  Intro

Django 개발자이기전에 Python을 이용하는 개발자로서, 배울 때 가장 헷갈렸던 중첩함수(Nested Function)과  decorator에 대한 포스팅을 해보겠습니다.

<br>

## 중첩함수


함수도 함수안에 중첩하여 사용할 수 있는데, 이를 중첨함수라고 합니다.

중첩함수(내부함수)는 상위부모 함수안에서만 호출 가능합니다.



예를 들어 
```python
def parent_function():
    def child_function():
        return "this is gg"
    return child_function()

print(parent_function())
```

이렇게 쓴다면, child_function에서 작성한 print문이 출력되는 것입니다.



그러면 굳이 이렇게 쓰는 이유가 무엇일까요?

<br>

## Why Nested Function?


이유는 가독성, Closure 두 가지 있습니다.

<br>

### 1. 가독성


우리가 함수를 사용하는 이유 중 하나는 반복되는 코드블럭을 함수로 정의해서 효과적으로 코드를 관리하고 가독성을 높이기 위함입니다.

중첩함수 역시 동일합니다. 함수안의 코드 중 반복되는 코드가 있다면 중첩함수로 선언해서 부모함수의 코드를 효과적으로 관리하고 가독성을 높일 수 있습니다.


```python
def print_all_elements(list_of_things):
    def print_each_element(things):
        print("things : ", things, "types : ", type(things))
        for thing in things:
            print("thing : ",thing)

    if len(list_of_things) > 0:
        print_each_element(list_of_things)
    
    else:
        print("empty things")
        
print_all_elements(list_of_things=[])
print_all_elements(list_of_things=[1,2,4])
print_all_elements(list_of_things=(1,2,3,))
print_all_elements(list_of_things={"a":1, "b":2})
```

print_all_elements를 부모함수로 선언하고 list_of_things라는 변수로 파라미터를 받도록 설정했습니다.

그리고 4가지 경우에 대해 테스트를 했습니다.

1. 빈 리스트

2. 값이 있는 리스트

3. 튜플

4. 딕셔너리



![image](https://user-images.githubusercontent.com/88086271/154831864-e6c8dd9c-9f79-4dc4-8d47-5817a8e0f11b.png)



결과를 보면 things는 부모함수의 list_of_things를 받는다는 걸 알 수 있습니다.



중첩함수를 쓰지 않고 나눠쓰게 된다면

```python
def print_each_element2(things):
    for thing in things:
        print(thing)

def print_all_elements2(list_of_things):
    if len(list_of_things) > 0:
        print_each_element2(list_of_things)
    
    else:
        print("empty things")
```


이렇게 되고, 함수를 타고 들어가야 하는 불편함이 있는데 중첨함수로 묶어버리면 따로 선언할 필요가 없게 되는 것입니다.


<br>


### 2. Closure


중첩함수를 사용하는 다른 큰 이유는 Closure인데, 부모의 변수나 정보를 가두어 사용하는 걸 뜻합니다.

부모함수는 중첨함수를 리턴하여 부모함수의 변수를 외부로부터 직접적인 접근을 격리하면서도, 연산은 가능하게 해주는 것입니다.

주로, 함수나 객체를 생성해내는 팩토리패턴에 사용됩니다.

그리고 여기서 사용 될 개념이 데코레이터입니다.


<br>


## Decorator

데코레이터는 그대로 직독직해하자면 장식이라는 뜻으로 많이 알고 계실겁니다.

그러면 파이썬과 장식과 어떤 연관이 있을까요?



간단한 함수를 통해 알아보겠습니다.



예를 들어 저는 투자전문가이며 삼성전자의 주가가 곧 떡상할거라는 정보를 알고 있는데, 이 정보를 제 고객들에게만 알려주고 싶습니다.

그러면 여기서 나올 수 있는 함수는 2개가 있습니다.



첫 번째로 이사람은 내 고객인가? 에 대한 함수입니다.


```python
def is_my_customer(email):
    if not User.objects.filter(email=email).exist():
        return False
    return True
```

is_my_customer라는 함수를 만들고, 파라미터로 이메일을 받습니다.


그래서 파라미터로 받은 이메일이 내 DB에 있다면, 이 사람은 제 고객이어서 True를 리턴하고 아니라면 False를 리턴하게 됩니다.



그 다음은 내 고객인걸 확인되었다는 걸 알려주는 함수입니다.

```python
def get_stock_information():
    return "확인되었습니다. 제 고객이시군요."
```

간단하게 이렇게 함수를 만들어보겠습니다.



이 정보는 아까 말했던 것처럼 내 고객에게만 말해야 하는데, 이 연결고리를 잊어먹지 않기 위해 강제적으로 유저를 식별해줄 수 있는 방법이 필요합니다.

바로 이때 사용하는 것이 데코레이터 입니다.


```python
@is_my_customer
def get_stock_information():
    return "확인되었습니다. 제 고객이시군요."
```

이렇게 선언해주면 되는데, 데코레이터의 가장 큰 특징이 하나 더 있습니다.

바로 중첩함수를 리턴해야 데코레이터를 사용할 수 있다는 것입니다.

왜냐하면 데코레이터의 기능을 다르게 설명하면 chains of functions, 즉 여러개의 함수가 연속적으로 자동호출되도록 해주는 것이기 때문입니다.

그래서  is_my_customer에 수정 작업이 필요합니다.


```python
def is_my_customer(func):
    def wrapper(*args, **kwargs):
        if not User.objects.filter(email=kwargs["email"]):
            return "제 고객이 아닙니다"
        return func(*args, **kwargs)
    return wrapper
```

is_my_customer를 중첩함수로 만들어야 했기에 내부에 wrapper라는 이름의 함수를 만들었습니다.



그리고 이메일 정보를 받아야 하는데, 우리가 값을 보낼 때 email="lee@gmail.com" 형태로 보내다보니 Keyword-Argument 형태로 보내게 되어 kwargs["email"] 이렇게 받을 수 있게 됩니다.

파라미터를 넣을 때 def get_stock_information("lee@gmail.com") 이렇게 넣을 수 있는데, 이럴 경우 wrapper에서 이메일을 받으려면  email = args 이렇게 설정해야 합니다.

보다 명확하게 하기 위해 kwargs를 이용하는 걸 권장드리며, 현업에서도 어떤 변수에 어떤 값을 보냈는 지 알기 위해 kwargs 형태를 많이 이용합니다.

그래서 User로 접근했을 때 계정이 없으면 False를, 있으면 func를 리턴시킵니다.

이 때 func는 제 고객이시군요.. 라는 메시지가 리턴되는 그 함수입니다.



이렇게 is_my_customer를 작성한 후 데코레이터를 다시 덮으면

```python
@is_my_customer
def get_stock_information(*args ,**kwargs):
    return "확인되었습니다. 제 고객이시군요."
```

이렇게 됩니다.



wrapper라는 중첩함수에서 나의 고객이 맞으면 func(get_stock_information)을 리턴하도록 되어 있었고, get_stock_information을 까보니 "확인되었습니다. 제 고객이시군요"가 리턴된다고 나와 있습니다.




제 DB상에는 "1@gmail.com"이라는 메일을 가진 유저가 존재하고, "1@gmail.com1"이라는 이메일은 가진 유저는 존재하지 않습니다.

없는 계정으로 테스트해보니, 고객이 아니라는 말이 뜨는 걸 확인할 수 있습니다.

반대로 있는 계정이면 제 고객이라고 뜨게 되는 걸 확인할 수 있습니다.



<br>

## 마치며..

이렇게 데코레이터에 대해 알아봤습니다.

데코레이터는 현업에서도 굉장히 많이 쓰이는 함수이니, Python을 하며 꼭 알아야 합니다.

저도 처음에 이해하는 데 많이 헷갈렸고, 직접 이렇게 코드들을 입력해보며 이해할 수 있었습니다.
