# drf-yasg

[티스토리 바로가기](https://kyleeee.tistory.com/entry/TIL26-drf-yasg)

<br>

저는 이전까지 API문서화의 경우, Postman을 주로 사용했었습니다. 

혼자 틈틈히 FastAPI를 할 때 Swagger라는 것에 대해 알게 되었고, 지금 회사에 들어가고 나서는 swagger를 사용해서 본격적으로 사용하게 되었는데요.

yasg는 ```Yet Another Swagger Generator```의 약자로 **또 다른 Swagger 생성기** 라는 의미를 가지고 있습니다.

다만, drf-yasg를 사용하지 않고 직접 yaml 파일을 만들어 사용하고 있지만 drf-yasg도 알아두면 좋을 것 같아서 해보게 되었습니다.

현재 개인적으로 진행하고 있는 [해당 프로젝트](https://github.com/leeky940926/dockers)에서 사용하고 있으니, 더 자세한 내용이나 코드는 해당 프로젝트를 클릭하여 들어가시면 됩니다.

출처는 [drf-yasg Git Repository](https://github.com/axnsan12/drf-yasg) 입니다!

<br>

## Install

우선, drf-yasg를 설치해줍니다.

```shell
pip install -U drf-yasg
```

validation mechanism 사용을 원하면 아래와 같이 입력해서 설치하라고 나와있는데, 저는 하지 않았습니다.

```shell
pip install -U drf-yasg[validation]
```

<br>

## Setting

설치가 되었다면, ```INSTALLED_APPS```에 ```drf-yasg```를 추가해줍니다.

```python
INSTALLED_APPS = [
   ...
   'django.contrib.staticfiles',  # required for serving swagger ui's css/js files
   'drf_yasg',
   ...
]
```

그리고 ```django-admin startproject ?``` 를 하고 만들어진 루트 디렉토리의 urls.py에 아래와 같이 추가합니다.

이렇게 보면 이해가 안 갈 수 있기 때문에, 제가 직접 커스터마이징한 코드를 아래 추가했습니다.

```
from rest_framework import permissions
from drf_yasg.views import get_schema_view
from drf_yasg import openapi

...

schema_view = get_schema_view(
   openapi.Info(
      title="Snippets API",
      default_version='v1',
      description="Test description",
      terms_of_service="https://www.google.com/policies/terms/",
      contact=openapi.Contact(email="contact@snippets.local"),
      license=openapi.License(name="BSD License"),
   ),
   public=True,
   permission_classes=[permissions.AllowAny],
)

urlpatterns = [
   re_path(r'^swagger(?P<format>\.json|\.yaml)$', schema_view.without_ui(cache_timeout=0), name='schema-json'),
   re_path(r'^swagger/$', schema_view.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),
   re_path(r'^redoc/$', schema_view.with_ui('redoc', cache_timeout=0), name='schema-redoc'),
   ...
]
```

<br>

## Customizing urls.py

코드와 아래 이미지를 보면 각각이 하고 있는 역할에 대해 알 수 있습니다.

* title : 문서의 전체 제목을 의미
* default_version : 명시될 버전을 의미
* description : 문서 홈페이지에 대한 설명을 의미
* terms_of_service : 서비스의 주소를 의미
* contact : 말 그대로 컨택 가능한 연락처

```python
from django.urls import re_path
from rest_framework import permissions
from drf_yasg.views import get_schema_view
from drf_yasg import openapi

schema_view = get_schema_view(
   openapi.Info(
      title="기획부터 배포까지!",
      default_version='v1',
      description='''기획부터 배포까지 프로젝트의 영화 API에 대한 문서입니다. 
      각 API에서 Try it out을 통해 서버 내 존재하는 영화 데이터를 직접 확인하실 수 있습니다!
      만약, 내부 코드에 대해 더 알고 싶으시다면, 아래 Terms of service를 눌러주세요!
      Git Repository로 이동합니다!
      문의 및 수정사항 필요 시, 아래 Contact the developer를 눌러서 이메일 부탁드립니다!''',
      terms_of_service="https://github.com/leeky940926/dockers",
      contact=openapi.Contact(email="leeky940926@gmail.com"),
   ),
   public=True,
   permission_classes=[permissions.AllowAny],
)

urlpatterns = [
   re_path(r'^swagger(?P<format>\.json|\.yaml)$', schema_view.without_ui(cache_timeout=0), name='schema-json'),
   re_path(r'^swagger/$', schema_view.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),
   re_path(r'^redoc/$', schema_view.with_ui('redoc', cache_timeout=0), name='schema-redoc'),
]
```

<img width="694" alt="image" src="https://user-images.githubusercontent.com/88086271/171985351-4ec37ef9-0d62-4671-a184-1cfee899cdec.png">

그래서 ```urls.py```에 해당 코드를 입력하고 서버를 켠 다음 
```shell
http://localhost:8000/swagger
```
이렇게 입력하면 해당 페이지로 이동하게 됩니다.

<br>

## 마치며..

여담으로, 제가 회사에 입사할 때 가장 좋게 보였던 부분 중 하나가 문서화를 꼼꼼하게 잘 했다는 점인데요.

위코드에서 프로젝트를 할 때도 문서화의 중요성을 느꼈었는데, 실제로 회사에 입사해보니 더 중요하단 것이 체감되었습니다.

개발자를 꿈꾸는 신입분이시라면 문서화를 하는 작업을 미리 연습해놓것도 좋을 것 같아요!
