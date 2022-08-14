# FastAPI Path Parameters

[FastAPI Path Parameters](https://fastapi.tiangolo.com/tutorial/path-params/) 공식문서를 참고하여 작성했습니다.

[티스토리 블로그 바로가기](https://kyleeee.tistory.com/entry/TIL35-FastAPI-Path-Parameters)

<br>

## API 생성

저는 유저 아이디를 받아, 아이디와 이름을 리턴해보겠습니다.

다만, 아직 DB가 없는 탓에 코드 내에서 그냥 임의로 하나 만들었습니다.

```python
class SpecificUser:
    def __init__(self, user_id: int):
        self.user_id = user_id

    @router.get("/{self.user_id}")
    async def get(user_id: int):
        return get_user_by_user_id(user_id=user_id)
```

<br>

여기서 문제가 __init__에서 self.user_id로 설정해준다면 엔드포인트나 내부함수에서 적용했을 때 될 줄 알았습니다.


<img width="504" alt="image" src="https://user-images.githubusercontent.com/88086271/184525293-15f2db6d-d3cb-4d18-a1ad-70907471292e.png">

<br>

그런데, 엔드포인트가 망가지는 걸 보고 아래와 같이 바꾸고 실행했더니 이번엔 잘 되었습니다.

Django에서 user_id를 가져올 때, self.request.user.id 이런식으로 가져오는데, 이후 FastAPI 더 공부해보면서 봐야 할 것 같습니다.

그리고 지난 포스팅에서 async 때문에 500에러가 발생했었는데, router함수에 설정해줘야 잘 돌아갑니다.

<br>

```
class SpecificUser:
    @router.get("/{user_id}")
    async def get(user_id: int):
        return get_user_by_user_id(user_id=user_id)
```        

<br>

<img width="402" alt="image" src="https://user-images.githubusercontent.com/88086271/184525377-410e25d3-2ecb-4196-bdc7-34b2eb26da8b.png">

<br>

그리고 엔드포인트가 같은데, 두 개의 메소드가 함수가 설정되어 있을 경우, 첫 번째 것이 항상 실행됩니다.

<br>

## Create Enum

Enum을 이용해 값을 비교할 수 있습니다.

```python
class ModelName(str, Enum):
    user = "user"
    company = "company"

def get_user_model(model_name: str):
    return model_name
    
class UserModel:
    @router.get("/models/{model_name}")
    async def get(model_name):
        if model_name == ModelName.user:
            return get_user_model(model_name=model_name)
        else:
            return None
```

<br>

만약 이렇게 작성되어 있다면, Path Parameter로 받은 model_name을 비교합니다.

그 때의 model_name이 user라면 ModelName.user의 값인 user와 일치하므로 함수 값이 리턴되고 아니면 null이 리턴됩니다.

<br>

<img width="449" alt="image" src="https://user-images.githubusercontent.com/88086271/184526013-4936f8db-03f4-4ea4-97bc-325aa939078c.png">

<br>

그런데 ```def get(model_name: ModelName)```과 같이 type annotation을 주면, ModelName에서 정의한 것이 Swagger에 보다 쉽게 표현됩니다.

<br>

<img width="473" alt="image" src="https://user-images.githubusercontent.com/88086271/184526148-ca74c3d7-5980-4fd8-ad03-83c47703b2de.png">

순서는 ModelNamed에서 정의한대로 나오게 됩니다.
