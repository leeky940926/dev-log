# EC2 인스턴스에 Pull받기

EC2 생성도 했으니, 제 프로젝트를 서버로 띄어보겠습니다.

[티스토리 블로그 바로 가기](https://kyleeee.tistory.com/entry/TIL36-EC2에-Pull받기)

<br>

생성된 EC2에서 연결을 누르면 인스턴스의 연결이 모달로 나옵니다. 저는 독립 실행형 SSH 클라이언트를 선택했습니다.

![image](https://user-images.githubusercontent.com/88086271/201511291-f47075e2-3e42-402f-b157-58af461bf861.png)

<br>

Continue Connecting 물음에는 yes 입력 후 연결합니다.

![image](https://user-images.githubusercontent.com/88086271/201511528-21e047b8-3afc-4da5-8fd1-bca09305bb84.png)

<br>

그러면 이렇게 화면이 전환되는데, 이걸 확인하셨으면 접속이 잘 된 것입니다.

제 프로젝트를 Clone 받기 위해 아래 명령어를 입력합니다.

```shell
sudo yum update -y

sudo yum install git -y
```

-y는 update, install 과정에서 발생하는 모든 질문에 대해 yes라고 응답하겠다는 의미입니다.

그리고 clone을 받으면 이렇게 프로젝트가 생성된 걸 확인할 수 있습니다.

<img width="423" alt="image" src="https://user-images.githubusercontent.com/88086271/201512056-521f70e0-7f0e-46d8-8389-768b9bc5469c.png">

<br>

그 다음 라이브러리 일치를 위해 requirements 파일을 설치합니다.

```shell
pip install -r requirements.txt
```

<br>

처음 파이썬 버전을 확인하면 2.7 버전일텐데 이 부분을 3.x 버전으로 업그레이드 시켜야 합니다.

아래 명령어를 통해 사용가능한 Python3.x 버전을 확인합니다

```shell
yum list python3
```

![image](https://user-images.githubusercontent.com/88086271/201512163-a449c5b7-f6df-4b0f-9416-49ec047769d4.png)

<br>

저는 3.7 버전을 사용할 수 있다고 나와서, 해당 버전으로 업그레이드 시키겠습니다.

```shell
sudo yum install python3

echo $PATH
-> /usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/ec2-user/.local/bin:/home/ec2-user/bin

sudo ln -s /usr/bin/python3.7 /usr/local/bin/python

ls -la /usr/local/bin/python
```

<br>

위 명령어들을 통해 /usr/local/bin/python의 타겟을 python3으로 변경합니다. 

그리고 세션을 끊고 재접속해서 파이썬 버전을 확인합니다.

```shell
python --version
-> Python 3.7.10
```

<br>

마지막으로 pip버전도 최신버전이 아니어서 업데이트 해줘야 합니다. 

```shell
sudo python3.6 -m pip install --upgrade pip
```

```shell
pip -V
-> pip 22.3.1 from /usr/local/lib/python3.7/site-packages/pip (python 3.7)
```

이렇게 해서 모든 준비가 끝났고, 서버를 실행시켜보겠습니다.

```shell
python manage.py runserver 0:8000
```

그랬더니 아래와 같은 에러가 발생했습니다.

![image](https://user-images.githubusercontent.com/88086271/201511386-82c0fa12-b098-4884-884d-56688ab33fff.png)

<br>

해당 프로젝트는 DB로 SQLite를 사용하고 있는데, SQLite버전에 문제가 생겼습니다. 결국 버전을 맞춰줘야 하는데 찾아보니 명령어 몇 줄만 입력하면 해결할 수 있는 문제였습니다.

```shell
wget https://kojipkgs.fedoraproject.org//packages/sqlite/3.10.2/1.fc22/x86_64/sqlite-3.10.2-1.fc22.x86_64.rpm
wget https://kojipkgs.fedoraproject.org//packages/sqlite/3.10.2/1.fc22/x86_64/sqlite-devel-3.10.2-1.fc22.x86_64.rpm
sudo yum -y install sqlite-3.10.2-1.fc22.x86_64.rpm sqlite-devel-3.10.2-1.fc22.x86_64.rpm
```

<br>

![image](https://user-images.githubusercontent.com/88086271/201512900-3224db77-4aa0-4c8f-b328-7a784e37672b.png)

<br>

이렇게 서버가 켜졌습니다. migrate는 해준적이 없으니 안돼있다는 것인데, 배포 시 자동 migrate되도록 배포 프로세스 구축으로 다음에 찾아뵙겠습니다.
