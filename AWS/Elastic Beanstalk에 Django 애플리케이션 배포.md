# Elastic Beanstalk에 Django 애플리케이션 배포

[티스토리 블로그 바로 가기](https://kyleeee.tistory.com/entry/TIL37-Elastic-Beanstalk에-Django-애플리케이션-배포-1)

<br>

EC2 인스턴스도 생성되었고, Elastic Beanstalk 환경에 배포하는 과정을 해보려고 합니다.

이거 끝나면 Jenkins를 이용한 CI/CD 파이프라인 구축 -> 도커 해볼 생각입니다.

출처는 [AWS Elastic Beanstalk](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/create-deploy-python-django.html) 공식문서 입니다.



<br>

## Setting up your Python development environment

EB로 배포하기 전에 로컬에서 애플리케이션을 테스트 하도록 Python 개발 환경을 설정합니다.

EC2 인스턴스에 연결하여, virtualenv를 설치합니다.

```shell
pip install virtualenv
```

<br>

그 다음, 설치한 virtualenv로 가상 환경을 설정하기 위해 아래 명령어를 입력합니다.
eb_python_app에는 본인의 애플리케이션을 써주시면 됩니다.
저는 제 애플리케이션 이름은 dockers를 입력했습니다.

<br>

```shell
virtualenv /tmp/eb_python_app

-> virtualenv /tmp/dockers
```

<br>

가상 환경이 준비되면 아래 명령어를 입력하여 activate 디렉토리에 있는 bin 스크립트를 실행하여 가상환경을 시작합니다.

```shell
source /tmp/eb_python_app/bin/activate

-> source /tmp/dockers/bin/activate
```

<br>

가상환경이 on되었으니 requirements.txt를 가상환경에 설치합니다.

해당 파일이 있는 디렉토리로 가서 아래와 같이 입력합니다.


```shell
pip install -r requirements.txt
```

<br>

공식문서에는 아래와 같이 입력하라고 나와 있는데, 가상환경을 처음 만들면 아무 패키지도 설치되어 있지 않다보니 pip freeze는 당연히 아무것도 없을 것이기 때문에 위 명령어로 바꾸게 되었습니다(저는)

```shell
pip freeze > requirements.txt
```
<br>

그 다음 EB CLI를 설치해야 합니다.

1. 아래와 같이 명령어를 입력합니다.

```shell
pip install awsebcli --upgrade --user
```

<br>

을 입력했는데, "Can not perform a '--user' install. User site-packages are not visible in this virtualenv." 라는 에러 메시지가 떴습니다.

검색해보니, 위에서 가상환경 설정을 위해 설치한 tmp파일로 이동하면 pyvenv.cfg 라는 파일이 있는데, 여기서 include-system-site-packages의 값을 True로 바꿔줘야 합니다. 바꿔주고 명령어를 입력하면 설치가 되는 걸 확인하실 수 있습니다.

2. PATH 변수에 실행 파일 경로를 추가합니다.

LOCAL_PATH를 입력해야 되며 아래와 같이 입력합니다.
공식문서에선 LOCAL_PATH로 표현되는 경로를 현재 PATH변수에 추가했습니다.

```shell
export PATH=LOCAL_PATH:$PATH
```

<br>

이렇게 해서 eb 설치가 잘 되었는지 버전을 통해 확인해봅니다.

```shell
eb --version
-> EB CLI 3.20.3 (Python 3.7.1)
```

<br>

## EB CLI를 사용하여 배포

EB 설치가 완료 되었으니 배포를 위한 준비를 해보겠습니다.

1. eb init 명령으로 EB CLI 초기화 작업을 합니다.

```shell
eb init
```
을 하면 region 선택이 나옵니다. 

저는 10번을 선택하겠습니다.

<img width="368" alt="image" src="https://user-images.githubusercontent.com/88086271/202842689-1c8634be-b524-4fe5-a81e-8556f268cbb5.png">

<br>

그랬더니 NotAuthorizedError - Operation Denied ... 에러가 발생했습니다.

<img width="467" alt="image" src="https://user-images.githubusercontent.com/88086271/202842780-8ce294e1-d8d5-4a65-9dff-30658752328b.png">

<br>

환경변수에 AWS_ACCESS_KEY_ID와 AWS_SECRET_ACCESS_KEY를 설정 해주지 않아서 발생했던 문제였습니다.

IAM에서 엑세스 키 만들기를 통해 발급받아서 env 파일에 적용하고 eb init을 다시 했더니 다음 단계로 잘 넘어가서 완료되었습니다.

<img width="426" alt="image" src="https://user-images.githubusercontent.com/88086271/202843453-d838198a-93d5-4e21-8972-6c96a1dedef7.png">

<br>

eb status를 입력하면 CNAME의 값이 나오는데, 그걸 ALLOWED_HOST에 추가합니다.

그 다음, 루트 디렉토리에 .ebextensions 폴더를 만들고 django.config파일을 만든 뒤 아래 내용을 입력합니다.

```shell
option_settings:
  aws:elasticbeanstalk:container:python:
    WSGIPath: ebdjango.wsgi:application
```

<br>

이제 세팅이 완료되었으니, eb create를 명령어를 이용해 해당 환경에 애플리케이션 배포를 합니다.

```shell
eb create dockers
```

그래서 Elastic Beanstalk에 들어가보면 생성되고 있는 걸 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/88086271/202843540-83be1e48-9d90-4c81-80dc-7db1ecfda95a.png)

<br>

그리고 추가적으로 세팅이 필요한 데 그 부분은 다음 파트 때 하도록 하겠습니다..
