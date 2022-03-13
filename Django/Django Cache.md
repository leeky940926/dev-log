# Django Cache

[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL19-Django-Cache)

<br>

## ✔️ Cache

웹 사이트를 이용해보며 한 번쯤은 캐시라는 단어에 대해 들어보셨을 겁니다. 저 역시 개발자가 되기 이전에도 캐시에 저장한다 같은 말은 들어봤었고, 개발자가 되어 직접 사용할 일이 생겼습니다. 그래서 공부할 겸 포스팅도 진행하게 되었습니다. 

캐시란 데이터나 값을 미리 복사해 놓는 임시 장소를 말합니다. 기존의 데이터에 접근하는 데 시간이 오래 걸릴 경우 값을 미리 복사해 놓으면 더 빠르게 계산이나 접근 시간을 줄여줄 수 있다는 장점이 있습니다.

이 부분은 [장고 캐시 공식문서](https://docs.djangoproject.com/en/4.0/topics/cache/)에 아래와 같이 나와 있습니다.

<br>

Each time a user requests a page, the web server makes all sorts of calculations – from database queries to template rendering to business logic – to create the page that your site’s visitor sees. This is a lot more expensive, from a processing-overhead perspective, than your standard read-a-file-off-the-filesystem server arrangement.

To cache something is to save the result of an expensive calculation so that you don’t have to perform the calculation next time. Here’s some pseudocode explaining how this would work for a dynamically generated web page:

<br>

간단하게 요약하자면

유저가 페이지에 요청을 보낼 때마다 서버는 데이터베이스 쿼리로부터 템플릿으로 렌더링을 하는데 매우 대가가 크다. 캐시에 데이터를 저장하는 건 계산 비용 절감을 해주며 다음 요청 때는 굳이 한 번 더 계산해줄 필요가 없다.

입니다.

그래서 캐시 데이터를 선정함에 있어, 업데이트가 자주 발생하지 않고, 자주 조회되는 데이터를 설정하는 게 바로 가져올 수 있으므로 권장되고 있습니다.

<br>

## ✔️ Cache Setting

[Django 세팅 공식문서](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-CACHES)에 따르면 캐시 세팅 경우의 수는 총 7가지입니다. 

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FboTxp8%2FbtrvL4yZthq%2F0EBkl3BVXGFMX8sKFYmqo0%2Fimg.png)


아무 세팅도 안했을 시, 기본적으로 아래와 같이 세팅됩니다. 바꾸고 싶다면 위의 캐시 설정 중 선택하면 되며, 2개 이상 설정도 가능합니다.
```python
{
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
    }
}
```
<br>

DatabaseCache는 캐시 데이터를 DB로 저장할 때 사용합니다. 빠르고 관리가 잘 되고 있는 DB서버에서 사용한다면 최고의 방법입니다. Django에서는 캐시 데이터를 저장하기 위한 테이블을 만들기 위한 명령어를 입력하여 생성합니다. 명령어는 다음과 같습니다.
```shell
python manage.py createcachetable
```
테이블은 [Location](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-CACHES-LOCATION)에 만들어지게 됩니다. 만약 여러 개의 테이블을 만들고 싶다면 해당 명령어를 여러 번 입력하면 됩니다. 단, Django에서는 migrate를 이용해 데이터베이스에 접근한 뒤 테이블을 생성하는데 createcachetable은 기존의 테이블은 터치하지 않고 누락된 테이블을 체크하여 생성합니다. 

<br>

DummyCache는 실제 캐시가 아닌 dummy라는 이름의 캐시가 있습니다. 이것은 캐싱을 사용하지 않고 개발/테스트하는 환경에서 주로 사용된다고 합니다. 

<br>

FileBaseCache는 캐시 값을 직렬화(Serializer)하고 별도의 파일로 Location 설정된 곳에 저장합니다. Serializer는 DRF에서 주로 사용되는 용어인데, 쉽게 말해 데이터 타입을 변환해준다고 생각하시면 됩니다. DRF에서는 파이썬 데이터를 Json으로 변환해준다는 의미로 사용됩니다. 

<br>

PyMemcacheCache는 메모리 기반 캐시 서버입니다. 주로 페이스북과 위키피디아 같은 사이트에서 사용하며 데이터베이스 접근과 사이트 성능을 크게 향상하기 위해 사용합니다. 모든 캐시 데이터는 서버에 저장되며 데이터베이스 혹은 파일 시스템 사용에 대한 추가적인 시간이 소모되지 않습니다. 

<br>

LocalMemoryCaching은 위에서 언급한 기본 캐시 세팅입니다. PyMemcacheCache의 빠르다는 이점을 이용하고 싶은데 실행할 수 없는 경우 LocalMemoryCaching을 주로 이용합니다. 추가로 스레드로부터 안전하다고 합니다.

<br>

RedisCache는 Django 4.0부터 나오게 되었습니다. 캐싱에 사용할 수 있는 메모리 내 데이터베이스입니다. 시작하려면 로컬 또는 원격 시스템에서 실행되는 Redis 서버가 필요합니다.

저는 캐시를 설정하는 기본 방법에 대해 알아본 뒤, Redis에 대해서만 해 볼 예정입니다.

<br>

## ✔️ Basic Usage

기본적으로 Cache 데이터를 설정하고 가져오는 방법에 대해 알아보겠습니다.
cache.set()을 이용해 캐시를 설정하고 cache.get()을 이용해 캐시를 불러오는데, cache.set()의 파라미터에 어떤 것이 있는지 알아보겠습니다.

```python
cache.set(key, value, timeout=DEFAULT_TIMEOUT, version=None)
```

캐시가 Key-Value 형태로 저장되기 때문에, Key-Value를 지정해줘야 하며 timeout은 설정해주지 않으면 300초가 됩니다.
그리고 버전의 경우 캐시 키 생성시 버전에 대한 파라미터이며 기본 값은 1입니다. 이 두 내용은 Django 세팅 공식문서를 통해 확인하실 수 있습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbMjszI%2FbtrvNG5t78i%2FbKWazyAv8trGCkEgrA9mRK%2Fimg.png)

자, 이제 직접 해보겠습니다. 저는 shell_plus를 통해 테스트를 했습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdrTYIi%2FbtrvNIoJiBR%2FikjOKFIj5hwpkPYma3k3Wk%2Fimg.png)

보면, 저는 Key값에 my_key라는 값을 주었고, Value에는 'hello_world'로 설정했습니다. 그리고 저장 시간은 20초입니다.
제가 설정한 뒤 바로 get()을 통해 가져왔을 때는 20초가 안 지났으니 값이 출력되는데, 제가 20초 후에 해보니 아무 값도 나오지 않았습니다. 저희 회사의 경우 정보의 중요도에 따라 각기 다르게 Timeout 설정을 했습니다. 

이제 기본적으로 캐시 세팅/불러오기에 대해 알아보았으니, Redis를 설정해보겠습니다.

<br>

## ✔️ Redis

이제 redis를 사용해보겠습니다.

우선, settings.py에 Redis 세팅을 합니다.

```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.redis.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379',
    }
}
```

포트번호 6379는 Redis를 설치하면 기본 포트번호가 6379로 되어 있어서 맞춰줘야 합니다.
제가 처음에 6739라고 한 다음 캐시 설정을 할 때마다 아래와 같은 오류가 발생했었습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbtoxiD%2FbtrvSfyS1mg%2FxT2Qn0vFbKj7Q2XpwABZqK%2Fimg.png)


그다음, redis를 설치해줍니다.

```shell
pip install django-redis
brew install redis
```

설치가 되셨으면 터미널 화면이 두 개 필요합니다. 각 터미널에 명령어 하나씩 입력합니다.

```shell
redis-server
redis-cli
```

redis-server를 입력하면 아래와 같이 나오게 됩니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FeiQPW1%2FbtrvSBhmlAu%2FfOt4LtswkXC2kKkZMmqybK%2Fimg.png)


그리고 redis-cli를 입력하면 아래와 같이 나오게 됩니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdswjLq%2FbtrvS8GhYly%2FfUKmmo0Ocd5Y0V9C2klrG0%2Fimg.png)

이제 모든 세팅은 끝났습니다. 

이제 shell_plus에서 캐시를 세팅한 후 redis에서 확인해보겠습니다.
저는 redis_key라는 Key에 hello_redis라는 값을 설정했습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcvJ33W%2FbtrvPfl6s1y%2FE2kMfsL8UPHB5w0mG21jWk%2Fimg.png)

<br>

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbgMO3X%2FbtrvRzRV173%2F1ovwpZ5FLKQxalNAWNpWo0%2Fimg.png)

그리고 redis-cli에서 keys *라는 명령어를 이용하여 조회를 하면 설정한 캐시 키가 나오게 됩니다. keys * 는 전체를 보여주는 명령어로서 redis-cli도 여러 명령어가 있으니 확인해보시면 좋을 것 같습니다.

<br>

## ✔️ 마치며..

이상 캐시에 대해 알아보았습니다. 데이터베이스 인덱싱과 마찬가지로 무작정 설정하는 건 좋지 않습니다. 데이터베이스든 서버든 공간을 갉아먹기 때문입니다. 필요한 데이터에 맞게 설정하셔서 원활한 서버 운영을 할 수 있도록 하는 것이 가장 좋다고 생각합니다.(당연한 말이지만..)
