# Flower

[티스토리 블로그 바로 가기](https://kyleeee.tistory.com/entry/TIL39-flower)

<br>

## Celery Monitoring

[redis 포스팅](https://github.com/leeky940926/dev-log/blob/main/Server/redis.md)에서 task queue에 대한 Flow를 간단하게 설명했었습니다.

```유저 -> 서버 -> 레디스(브로커) -> 워커```인데, queue에 있던 작업들이 실행되면 그걸 모니터링하는것이 필요합니다.

그 때 사용하는 Monitoring Tool이 ```flower``` 입니다.

그래서 ```flower```를 통해 모니터링하는 순서에 대해 알아보도록 하겠습니다.

<br>

우선, ```flower```를 설치합니다.

```shell
pip install flower
```

<br>

설치 후 실행합니다.

프로젝트 이름은 장고 프로젝트 이름을 넣으면 되고, 기본 포트번호는 5555입니다. 

포트번호를 따로 정해서 입력해도 됩니다.

```shell
celery -A projectname flower --port=6666
```

그 다음 주소창에 ```localhost:6666``` 이렇게 입력하면 다음과 같은 화면이 나옵니다.

```Broker```에 제가 ```CELERY_BROKER_URL```로 설정한 redis 주소가 나오게 되며 연결되었다고 나오는 걸 확인할 수 있습니다.

<br>

![image](https://user-images.githubusercontent.com/88086271/206897605-8d5cef1a-6436-401b-a4c4-d09224810908.png)

<img width="548" alt="image" src="https://user-images.githubusercontent.com/88086271/206897775-e6481ad6-539c-4f71-8e19-56622f437baf.png">


<br>

맨 아래 ```plus_task```는 제가 이번에 테스트를 위해 임의로 만든 함수입니다.

```python
from dockers.celery import app as celery

@celery.task(bind=True)
def plus_task(self, value):
    return value
```

<br>

```bind=True```를 안 쓰고 싶다면, 함수안의 self 인자를 제거하면 됩니다.

<br>

그리고 그 상태에서 아래와 같이 입력하면 워커 한 대가 나오게 됩니다.

```shell
celery -A projectname worker -l INFO
```
<br>

![image](https://user-images.githubusercontent.com/88086271/206897719-46996cd4-1f3d-4643-8754-4b41ece30b30.png)

<br>

그러면 이제 비동기 요청을 보내보겠습니다.

shell을 켠 뒤, ```함수명.apply_async(())``` 이렇게 선언해서 실행합니다.

사실 괄호는 한 개써도 됩니다. 그런데 저는 두 개를 쓰는 이유는 ```apply_async```에 값을 넣어 실행할 땐 iterable한 값을 넣어야 하기 때문입니다.

예를 들어 value에 7을 넣으려면, 일반 함수에선 ```plus_task(7)``` 이렇게 실행해도 되지만 ```apply_async```는 ```argument after * must be an iterable, not int``` 의 ```TypeError```를 리턴합니다.

그리고 또 까다로운게, ```iterable```한 걸 파라미터로 받지만 리스트나 튜플만 됩니다. 딕셔너리 같은 걸 넣으면 ```task args must be a list or tuple``` 아래와 같은 ```TypeError```를 리턴합니다.

그러다 보니 괄호를 두 번 써서 인자가 없을 경우엔 ()로 빈 튜플을 보내서 통일성을 유지하기 위해 쓰고 있습니다.

이건 기업이나 사람마다 코드 스타일이니 입맛에 맞게 사용하시면 됩니다.

<br>

설명이 꽤 길었고, 다시 함수로 돌아가서 비동기 태스크를 실행하겠습니다.

```shell
plus_task.apply_async((1,))
``` 

1을 value로 넘겨서 실행하면 ```processed```와 ```succeeded```에 실행된것과 성공된 것이 추가 됩니다.

그리고 worker에 task를 받고, 그 결과까지 나타나는 걸 확인할 수 있습니다.

사실 ```active```에 가장 먼저 생기는데, 워낙 짧은 태스크다 보니 캡처를 못 했습니다.

![image](https://user-images.githubusercontent.com/88086271/206898798-99bdc71b-e348-4661-9717-1ae8cfc1ce48.png)

<br>

![image](https://user-images.githubusercontent.com/88086271/206898806-77eed7c4-5972-4c7f-b3d0-9a443abc7814.png)

<br>

<img width="702" alt="image" src="https://user-images.githubusercontent.com/88086271/206898901-89571432-daf7-43b1-85b0-ce24977d9e8a.png">

<br>

이렇게 태스크 모니터링까지 같이 해보았습니다.

원래는 비트 스케줄 설정, 크론탭 설정까지 해서 예약된 작업을 실행하는 용도로 사용하기도 하는데, 이 부분은 추후 다뤄보도록 하겠습니다.
