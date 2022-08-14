# FastAPI 레이어분리

[티스토리 블로그 바로가기](https://kyleeee.tistory.com/entry/TIL34-FastAPI-레이어분리)

공식문서 처음 시작할 때, ```main.py```에서 모든 걸 해결했었습니다.

하지만 실무는 그렇지 않죠

이 부분 역시 공식문서의 [Bigger Applications - Multiple Files](https://fastapi.tiangolo.com/tutorial/bigger-applications/) 에 설명되어 있습니다.

다만, 여기선 url안에서 API를 구현하는데, 그것도 분리해서 한 번 만들어봤습니다.

<br>

우선, 디렉토리를 아래와 같이 설정해주세요

<img width="376" alt="image" src="https://user-images.githubusercontent.com/88086271/184474465-f71ef8d3-335e-4839-aa81-cde727fb1e0d.png">


<br>

저는 해당 레포에서는 유저관련 앱만 다룰 것이기 때문에 app -> users로 설정하고 items.py는 추가하지 않았습니다.

<img width="283" alt="image" src="https://user-images.githubusercontent.com/88086271/184474649-5079aa74-3a28-430b-a4f8-7b22e9e68d38.png">


## APIRouter

<br>

리소스별로 API를 호출하기 위해 APIRouter를 사용합니다.

그리고 users -> routes -> users.py에 아래와 같이 입력합니다.

```python
from fastapi import APIRouter

router = APIRouter()

user_tags = ["users"]

@router.get("/users", tags=user_tags)
async def read_users():
    return [
        {
            "username" : "ky"
        },
        {
            "username" : "hihi"
        }
    ]
```

<br>

## Dependencies

<br>

코드가 작동하는데 필요한 종속성을 의미하며, 이를 친절히 [공식문서](https://fastapi.tiangolo.com/tutorial/dependencies/)에서 설명해주고 있는데, 나중에 한 번 더 봐야할 것 같습니다.

여튼, Dependencies를 설정해줍니다. 예제에서는 헤더의 ```x-Token```를 읽는 것에 대해 설정했습니다.

```dependencies.py```로 이동해서 아래와 같이 입력해줍니다.

```python
from fastapi import Header, HTTPException

async def get_token_header(x_token:str = Header()):
    if x_token != "fake-super-secret-token":
        raise HTTPException(status_code=400, detail="X-Token header invalid")
```



<br>

```users.py```에서 엔드포인트 식별 후 어떤 메소드로 가야하는지 식별해줍니다.

```python
from fastapi import APIRouter, Depends
from users.dependencies import get_token_header

user_tags = ["users"]
router = APIRouter(
    prefix="/users",
    tags=user_tags,
    dependencies=[Depends(get_token_header(x_token="fake-super-secret-token"))],
    responses={404: {"desc":"Not !Found"}}
)

@router.get("")
async def read_users():
    return [
        {
            "username" : "ky"
        },
        {
            "username" : "hihi"
        }
    ]
```

get 메소드의 파라미터로 APIRouter안에 있는 변수들을 설정할 수 있는데, 같은 앱이니 APIRouter안에서 한 번에 식별하도록 하겠습니다.


<br>

그리고 ```main.py```를 이렇게 바꿔줍니다.

```python
from fastapi import Depends, FastAPI
from users.dependencies import get_token_header
from users.routers.users import router as user_router



app = FastAPI(dependencies=[Depends(get_token_header)])
app.include_router(user_router)
```

그리고 서버를 실행시켜준 뒤, 엔드포인트 접속을 해봅니다.

단, 서버실행 명령어를 그대로 치면 안됩니다. ```main.py```가 users안으로 이동했기 때문입니다.

```shell
uvicorn users.main:app --reload
```
이렇게 입력해줘야 합니다.

그리고 ```localhost:8000/users```를 입력하면 아래와 같은 문구가 뜨게 됩니다.

```shell
{"detail":[{"loc":["header","x-token"],"msg":"field required","type":"value_error.missing"},{"loc":["header","x-token"],"msg":"field required","type":"value_error.missing"}]}
```

헤더때문인데, ```main.py```와 ```users.py```에서 설정한 dependency때문에 두 번 발생하게 된 것입니다.

이 의존성을 앱 시작부터 걸거냐, API별로 걸거냐 등 한 번쯤 생각해봐야 할 것 같습니다.

이 부분은 그러면 우선 어떻게 확인해야 할까요? 바로 Swagger 입니다.

<img width="593" alt="image" src="https://user-images.githubusercontent.com/88086271/184476022-c21a5d75-e7c8-4de6-ab33-cf761772d84c.png">

여기서 ```x-token```에 아까 ```dependencies.py```에서 설정했던 토큰 정보를 입력합니다.

그러고 execute를 누르면

<img width="557" alt="image" src="https://user-images.githubusercontent.com/88086271/184476054-be2d1112-e6d7-4d6b-b17c-ae7cced17c35.png">

이렇게 잘 나오는 걸 확인할 수 있습니다.

그런데 뭔가 아쉬운 게 저는 url은 url로, 실제 API는 API별로 묶고 싶습니다.

그래서 apis라는 디렉토리를 추가해서 그 안에, users.py를 만든 뒤 연결해줘보겠습니다.

api경로는 users.apis.users가 되는 것입니다.

<br>

## URL과 API 분리

그래서 user.routers의 users.py를 아래와 같이 바꿨습니다.
Django에서 View를 클래스로 만들고 HTTP Method별로 dispatching하여 위임하는 것과 비슷한 방식인데요.
user_id를 Path Parameter로 바꿀걸 고려해서 user_id를 인자로 받을 수 있게 했습니다.

<br>

```python
from fastapi import APIRouter, Depends
from users.dependencies import get_token_header
from users.apis.users import get_users

user_tags = ["users"]
router = APIRouter(
    prefix="/users",
    tags=user_tags,
    dependencies=[Depends(get_token_header)],
    responses={404: {"desc":"Not Found"}}
)

class UserList:
    def __init__(self, user_id: int):
        self.user_id = user_id
    
    @router.get("")
    def get():
        return get_users()
```

<br>

그리고 users.dtos.users에 User라는 Serializer모델을 만들었습니다.
```python
from pydantic import BaseModel

class User(BaseModel):
    username: str
```

<br>

마지막으로 users.apis의 users.py입니다

```python
from users.dtos.users import User

def get_users():
    data = [User(username=name) for name in ["ky", "lee"]]
    return data
```
    
async가 빠졌는데, async 걸고 호출하면 500 에러가 납니다. 

```shell
ValueError: [TypeError("'coroutine' object is not iterable"), TypeError('vars() argument must have __dict__ attribute')]
```

async를 빼고 호출하면 일단 결과가 호출돼서 URL-API를 분리할 수 있게 됩니다.

<img width="343" alt="image" src="https://user-images.githubusercontent.com/88086271/184477572-5d9bf57e-6106-425d-844f-2959de0d19fe.png">

<br>

우선, 이렇게 진행해야 할 것 같습니다.
