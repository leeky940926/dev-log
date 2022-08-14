# FastAPI 공식문서 예제심화

모든 출처는 [FastAPI 공식문서 예제심화](https://fastapi.tiangolo.com/ko/#_9)입니다.

<br>

Put 메소드를 이용한 수정입니다.

아래 주석으로 예제심화 추가라고 작성된 부분만 추가해줍니다.

```python
from typing import Union
from fastapi import FastAPI

#예제심화 추가
from pydantic import BaseModel

app = FastAPI()

#예제심화 추가
class Item(BaseModel):
    name: str
    price: float
    is_offer: Union[bool, None] = None

@app.get("/")
def read_root():
    return {"Hello" : "World"} 


@app.get("/items/{item_id}")
def read_item(item_id: int, q: Union[str ,None] = None):
    return {"item_id" : item_id, "q" : q}

#예제심화 추가
@app.put("/items/{item_id}")
def update_item(item_id: int, item: Item):
    return {"item_name" : item.name, "item_id": item_id}
```

<br>

그리고 [Swagger](http://localhost:8000/docs#/default/update_item_items__item_id__put) 이동합니다.

이렇게 Path Parameter와 Request Body를 설정한 뒤 실행하면 입력한 결과가 나옵니다.
Swagger에서 테스트하실 때 ```try it out```을 누르시고 하셔야 합니다.

<img width="490" alt="image" src="https://user-images.githubusercontent.com/88086271/184472791-826f1980-43a9-48cb-bd1a-739d6aca85c4.png">

<img width="428" alt="image" src="https://user-images.githubusercontent.com/88086271/184472825-c7b50986-4cf1-4e82-8541-e15f6d5a573a.png">

<br>

추가로 

```python
is_offer: Union[bool, None] = None
```
여기에서 is_offer는 bool타입이 리턴되는데 없을 경우 null로 리턴된다는 의미입니다.

방금한 Put 메소드에서

```python
@app.put("/items/{item_id}")
def update_item(item_id: int, item: Item):
    return {"item_name" : item.name, "item_id": item_id, "item_status" : item.is_offer}
```

<br>

이렇게 결과값에 is_offer의 값을 item_status로 담는다면, 그리고 Body내역을 아래와 같이 한다면
```python
{
  "name": "strings",
  "price": 0
}
```

<br>

그 결과는 null이 됩니다.


<img width="429" alt="image" src="https://user-images.githubusercontent.com/88086271/184472972-8eca489f-7f1e-48d0-b2d9-d64e0279f0ae.png">

<br>

만약 기본값을 False로 나오게 하기 위해 이렇게 바꾸고 해보면 어떻게 될까요?

이렇게 False가 리턴되는 걸 확인할 수 있습니다.

```python
is_offer: Union[bool, None] = False
```

<img width="319" alt="image" src="https://user-images.githubusercontent.com/88086271/184473010-867719df-e7e8-4d5f-9943-a83d389eceb9.png">

<br>

## 마치며

하지만 하나의 앱에서 모든 걸 할 수 없죠. 장고처럼 각 역할별로 분리될거라 생각합니다.

다음번엔 레이어분리를 해서 연결해보도록 하겠습니다.
