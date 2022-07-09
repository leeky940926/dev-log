# Celery - Distributed Task Queue

저는 현재 회사에서 celery를 이용해서 비동기 작업큐들을 돌리고 있는데요.

그냥 Celery설정 후 돌리는 것보다 이게 어떤건지 그 내부를 알고 싶어서 작성하게 되었습니다.

개인적으로 실행을 해보며 작업 돌아가는 건 본 상태라 개념적으로 접근하려고 합니다.

모든 출처는 [Celery 공식문서](https://docs.celeryq.dev/en/stable/index.html)입니다.

<br>

## 왜 Celery를 사용하나요?

Celery는 방대한 양의 메시지를 처리하는 간단하고 유연하며 안정적인 분산 시스템이며 이러한 시스템을 유지 관리하는 데 필요한 도구를 운영에 제공합니다.

라고 공식문서에 나와있습니다.

말 그대로 방대한 양의 작업을 분산해서 처리할 수 있다고 이해하면 됩니다.

지금 저의 상황을 예로 설명하자면, 저는 전국 초/중/고 3주치 시간표를 수집 및 저장하는 작업하고 있습니다.

초등학교 약 1천만개, 중학교 약 500만개, 고등학교 약 500만개로 매 주 2000만개의 데이터를 수집 및 저장하는 작업을 하고 있습니다.

그 외 다른 정보에 대해서도 수집하지만 양이 가장 많은 시간표로 예를 들었습니다.

일반적으로 각 학교별 수집/저장하는 함수를 만드려면

```python
def get_time_tables():
    get_elementary_school_time_tables()
    get_middle_school_time_tables()
    get_high_school_time_tables()
    return True
```
이런식으로 함수가 순차적으로 실행되는데, 대용량데이터를 이렇게 Queue를 두어서 돌리면 시간이 매우 많이 필요합니다.

중간에 한 번 에러나거나 그러면 그 시간만큼 다시 돌려야 하는 불상사가 생기게 됩니다.

또한 위에 읽어보시면 제가 "매 주" 2000만개의 데이터를 수집 및 저장한다고 했는데, 제가 매주 회사에 출근해서 함수를 실행시켜주는 것일까요?

아닙니다. 제가 매 주 해당 작업이 돌아가도록 설정했기 때문인데요. 스케줄링이 가능하다는 것은 Celery의 가장 큰 장점 중 하나입니다.

즉, 이러한 문제를 해결하기 위해 사용하는 것이 Celery입니다.

<br>

## Celery with Django

Celery에서는 Django에서 어떻게 사용하면 되는지 [공식문서](https://docs.celeryq.dev/en/stable/django/first-steps-with-django.html#django-first-steps)에 친절하게 나와있습니다.

우선, Django 프로젝트를 만들게되면 아래와 같은 구조로 디렉토리가 생성됩니다.

```
- proj/
  - manage.py
  - proj/
    - __init__.py
    - settings.py
    - urls.py
```
그리고 나서, Celery 설정을 해줘야 할 모듈을 만들어줘야 하는데, 이에 대해서도 Celery가 권장해주는 부분이 있습니다.

```
then the recommended way is to create a new proj/proj/celery.py module that defines the Celery instance:
```

즉, settings.py의 레벨과 동일하게 모듈을 생성하라는 의미입니다. 그에 맞게 파일을 만들고 안에서 설정해보겠습니다.

```python
import os

from celery import Celery

# Set the default Django settings module for the 'celery' program.
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'proj.settings')

app = Celery('proj')

# Using a string here means the worker doesn't have to serialize
# the configuration object to child processes.
# - namespace='CELERY' means all celery-related configuration keys
#   should have a `CELERY_` prefix.
app.config_from_object('django.conf:settings', namespace='CELERY')

# Load task modules from all registered Django apps.
app.autodiscover_tasks()
```

Celery를 import한 후, app 설정을 해주면 됩니다.

그리고 ```config_from_object```는 [Celery 관련 설정](https://docs.celeryq.dev/en/stable/userguide/configuration.html#configuration)에 대해 읽어오기 위해 사용하는 메소드입니다.

CELERY로 시작하는 설정에 대해 읽어온 후 적용하겠다는 의미입니다.

```autodiscover_tasks```는 모든 앱 내에서 설정된 tasks.py 모듈을 찾아내는 메소드입니다.

```Searches a list of packages for a "tasks.py" module```

파라미터 없이 설정하면 Django에 위임합니다. 특정앱을 지정할꺼면 ['app_name1', 'app_name2'] 이렇게 리스트형태로 설정해주면 됩니다.

그리고 설정해줬다면 Celery를 설정할 함수에 데코레이터를 붙여주면 됩니다

```python
@app.task(bind=True)
def debug_task(self):
    print(f'Request: {self.request!r}')
```

이렇게 task라는 메소드를 가져온 뒤, kwargs형태로 bind=True를 설정해주면 됩니다.

bind=True는 3.1버전에 새로 생긴 옵션이며, 작업 인스턴스를 쉽게 참조한다고 합니다.

<br>

## Celery Options

3.1버전에 추가된 bind=True에 대해서, [bound tasks](https://docs.celeryq.dev/en/latest/userguide/tasks.html#bound-tasks)라는 내용이 공식문서에 작성되어있습니다.

bind=True가 설정된 메소드의 경우, self를 항상 설정해줘야 합니다.

즉, 해당 메소드는 Celery 작업 인스턴스라는 걸 선언한다는 의미로 생각하면 될 것 같습니다.

그리고 작업 인스턴스가 여러 이유로 중간에 끊길 수도 있습니다.

현재 제가 작업하는 것에 대해 예를 들면, 시간표 데이터는 [나이스 오픈 API](https://open.neis.go.kr/portal/mainPage.do)를 통해 수집하는데 나이스에 요청을 보내는 과정에서 작업이 끊길 수 있습니다.

그럴 경우, 작업이 다시 실행되도록 설정해야 하며 그 때 [retry 옵션](https://docs.celeryq.dev/en/latest/reference/celery.app.task.html#celery.app.task.Task.retry)을 설정해야 합니다.

<br>

## Outro

이정도가 제가 현재 작업하고 있는 사항에 대한 내용입니다.

Celery 내부 기능에 대해 더 사용할 일이 있으면 그 때 새롭게 포스팅 하겠습니다.
