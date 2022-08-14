# FastAPI 공식문서 따라해보기

모든 출처는 [공식문서](https://fastapi.tiangolo.com/ko/)입니다.


<br>

## 설치

```shell
pip install fastapi
```

```shell
pip install uvicorn[standard]
```

아래 명령어 입력할 때, ```zsh: no matches found: uvicorn[standard]```

이 메시지가 뜬다면

<img width="401" alt="image" src="https://user-images.githubusercontent.com/88086271/184472089-6ac8d9b5-0cd1-4a51-8d57-8f8d33fadd6f.png">

```pip install 'uvicorn[standard]'``` 이렇게 입력해야 합니다.


<br>

## main.py 생성

서버를 실행시키기 위해 ```main.py```를 만들고 아래와 같이 입력해보겠습니다.

<br>

```python
from typing import Union
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"Hello" : "World"} 
```

<br>

그 다음 서버를 실행하고 ```localhost:8000```에 접속해봅니다.

```shell
uvicorn main:app --reload
```

<img width="295" alt="image" src="https://user-images.githubusercontent.com/88086271/184472257-8a396307-b8ff-4782-8cd3-c9085aa1bdd6.png">

<br>

그 다음 아이템에 대한 엔드포인트를 검색해봅니다.

```shell
http://localhost:8000/items/5?q=hihi
```

<img width="261" alt="image" src="https://user-images.githubusercontent.com/88086271/184472325-7953b535-01d3-4772-addc-dcf935eb31ec.png">

<br>

여기까지가 기본 API 호출에 대한 방법이고, 다음엔 예제심화로 들어가겠습니다.
