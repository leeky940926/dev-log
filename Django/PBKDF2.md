# PBKDF2

[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL9-PBKDF2)

<br>

## ✔️ Intro


Django는 비밀번호 관리를 위해 다양한 기능들을 제공합니다.

오늘은 어떻게 Django가 비밀번호를 저장하는지, 어떻게 스토리지 해싱을 구성하는지 알아보겠습니다.
그리고 해쉬화된 비밀번호와 함께 작동하는 유용한 기능들에 알아보겠습니다.

모든 출처는 [공식문서](https://docs.djangoproject.com/en/4.0/topics/auth/passwords/)입니다.

<br>

## ✔️ How Django stores passwords

Django는 능동적인 비밀번호 저장 시스템을 제공하며, 기본적으로는 PBKDF2라는 걸 기본옵션으로 사용합니다.
PBKDF2는 암호화 함수 중 하나입니다. 일단 지금은 저장 시스템 중 하나로 PBKDF2라는 걸 사용하는구나 정도로 알고 계시면 될 것 같습니다.
Django auth에서 제공하는 기본적인 User 모델의 비밀번호는 아래와 같은 형태를 보입니다.

<br>

```python
<algorithm>$<iterations>$<salt>$<hash>​
```


구성된 각각의 특정은 다음과 같습니다.



첫 번째로 algorithm은 해쉬 알고리즘을 뜻합니다.
두 번째로 iterations는 알고리즘 반복 횟수
세 번째로 salt는 무작위 salt값
네 번째로 hash는 비밀번호 hash의 결과



hash는 임의의 길이를 가진 데이터를 고정된 길이의 데이터로 매핑합니다.
알고리즘이 뜻하는 게 뭔지, salt가 뭔지 익숙하지 않은 용어가 많을 수도 있는데, 지금은 비밀번호가 저런 형태를 가지는구나 정도로 이해하고 넘어가면 될 것 같습니다.


서두에 Django는 PBKDF2를 사용한다고 기입했었습니다. 이는 무려 NIST에서 추천을 받은 방법론이며 SHA256 이라는 알고리즘과 함께 사용됩니다. 개인적으로 대학교 때 Baldrige Performance Excellence Program에 대해 배우며 NIST에 들어가서 글을 읽을 기회가 있었는데, 그 후 이렇게 다시 듣게 될 줄은 몰랐습니다.


각설하고, Django에서 이렇게 기본적인 방법들을 제공한다고 해서 이것만 써야하는 건 당연히 아닙니다. 여러 다른 방법들에 대해 사용이 가능한데, 가장 자주 쓰이는 방법이기도 하고 저희 회사에서도 PBKDF2를 사용하고 있어서 해당 내용만 포스팅 예정입니다.
그러면 각기 다르게 할 수 있는 세팅에 대해 우선 알아보겠습니다.

<br>

```python
PASSWORD_HASHERS = [
    'django.contrib.auth.hashers.PBKDF2PasswordHasher',
    'django.contrib.auth.hashers.PBKDF2SHA1PasswordHasher',
    'django.contrib.auth.hashers.Argon2PasswordHasher',
    'django.contrib.auth.hashers.BCryptSHA256PasswordHasher',
    'django.contrib.auth.hashers.ScrpytPasswordHasher'
]
```
<br>

Django의 settings.py에 위와 같이 선언해주면 PBKDF2를 모든 비밀번호 저장에 사용하게 됩니다.
나머지 4개는 사용 안 하는 것이 아닙니다. 기본값으로 PBKDF2를 사용하고 프로젝트에 따라 나머지 4개 중 하나로 유동적이게 바꿀 수 있습니다.
그러면 사용할 거 하나만 선언하면 되지 않냐? 라고 하실 수 있는데, 회사마다 정책이 어떻게 될지 모르고 세팅 수정은 지향하는 방법이 아니므로 위와같이 선언하는 것이 일반적인 방법입니다.

만약, 우리회사는 Argon2를 메인으로 쓸거라고 하시면 Argon2PasswordHasher를 맨위에 선언하시면 됩니다.

<br>

## ✔️ PBKDF2 setting

PBKDF2와 Bcrypt는 반복할 횟수(iterations의 수)를 선언합니다.  이 두 가지 방법은 우리 서버에 공격이 들어올 때, 공격자의 속도를 늦추어 해시된 암호에 대한 공격을 더 어렵게 만드는 장점이 있습니다. 대신, 그만큼 반복횟수를 증가시켜야 합니다. 
그래서 기존의 PBKDF2를 상속받은 후 커스터마이징 작업이 필요합니다.

세팅 후 저장하기 위해, 프로젝트 디렉토리 내부에 hashers.py라고 만들어서 아래와 같이 입력해주고 저장합니다. 프로젝트 내부에 있기만 하면 됩니다.

```python
from django.contrib.auth.hashers import PBKDF2PasswordHasher

class MyPBKDF2Hahser(PBKDF2PasswordHasher):
    interations = PBKDF2PasswordHasher.iterations * 100
```

<br>

그리고 아까 선언했던 PASSWORD_HASHERS에 내가 만든 MyPBKDF2Hasher를 추가합니다.

```python
PASSWORD_HASHERS = [
    'projectname.hashers.MyPBKDF2PasswordHasher',
    'django.contrib.auth.hashers.PBKDF2PasswordHasher',
    'django.contrib.auth.hashers.PBKDF2SHA1PasswordHasher',
    'django.contrib.auth.hashers.Argon2PasswordHasher',
    'django.contrib.auth.hashers.BCryptSHA256PasswordHasher',
    'django.contrib.auth.hashers.ScryptPasswordHasher',
]
```

<br>

## ✔️ Password methods

비밀번호 관리(저장 및 체크)를 위해 Django auth의 User 모델은 기본적으로 제공하는 메소드가 있습니다.

첫 번째로 ```check_password(password, encoded)``` 입니다.
비밀번호를 체크하는 메소드인데, 파라미터의 첫 번째 값에는 Body로 입력받은 비밀번호를 입력하고 encoded에 인코딩된(암호화된) 비밀번호를 입력합니다.
이게 무슨 말이냐면, 기본적으로 회원가입을 했다면 비밀번호는 암호화되어 저장됩니다.
여기서 사용되는 것이 PBKDF2인것입니다.

예를 들어, 제 비밀번호는 "1234"이고 로그인을 할 때 Body로 비밀번호를 "1234"를 받았다고 해보겠습니다.
그러면 받은 "1234"라는 비밀번호를 PBKDF2로 암호화를 한 뒤, 그 값이 DB에 있는 내 비밀번호(encoded라는 이름의 파라미터)와 일치하는지 확인을 합니다. 그 값이 일치한다면 True를 리턴하고 틀리면 False를 반환하게 됩니다.

두 번째로 ```make_password(password, salt=None, hasher='default')```입니다.
회원가입할 때 내가 입력한 비밀번호를 암호화해서 저장하기 위한 메소드입니다.
그래서 password에는 내가 입력한 비밀번호가 들어가게 되고, hasher는 PASSWORD_HASHERS에서 최상단에 선언된 방법이 기본값으로 들어가게 됩니다. 저는 PBKDF2를 선언했으니 기본값으로 들어가게 됩니다. 

salt는 랜덤데이터를 의미합니다. 정확히 말하자면 비밀번호를 암호화할때 추가적으로 사용되는 랜덤데이터입니다. 단순한 암호화 규칙에 맞게 할 경우, 암호화된 걸 다시 복호화하는데 더 쉽다는 위험에 노출될 수 있기 때문에 그걸 방지하고자 넣는 추가 데이터라고 생각하시면 됩니다. 여기선 hasher에서 내가 default로 선언한 암호화 알고리즘으로 하지 않을 시 입력해줘야 합니다.

예를 들어, 제가 PBKDF2를 안쓰고 Argon2를 쓴다고 했으면 salt의 값을 입력해야 하는 것입니다.
salt input에 대해 잘 설명해놓은 블로그가 있는데 간단하게 설명하자면,
내가 비밀번호를 "farm1990M00" 으로 입력하고 salt에 "f1nd1ngn3m0" 이렇게 입력했다면 두 개가 합쳐져서 "f1nd1ngn3m0farm1990M00"이 되고 이 걸 알고리즘을 통해 암호화 하게 되는 것입니다.

<br>

## ✔️ SHA256

바로 위에 salt를 하는 과정과 비밀번호 구성을 이야기할 때 알고리즘에 대해 이야기했었습니다.
PBKDF2는 SHA256 이라는 알고리즘을 쓰는데, 암호화 하는 과정을 통해 우리가 알 수 있는 건 암호화가 그냥 이루어지는 게 아닌 알고리즘, 규칙에 의거하여 된다는 것입니다.
그래서 SHA256에 대해 간단하게 작성해보겠습니다.

SHA256(Secure Hash Algorithm)은 256비트로 구성되어 64자리 문자열을 리턴하는 알고리즘입니다.
경우의 수는 2^256만큼 만들 수 있다고 합니다!

<br>

## ✔️ 마치며

이제 암호화된 비밀번호를 이용해 유저를 식별한 뒤, 토큰을 발급하는 걸 해보겠습니다.
