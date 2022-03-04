# Git SSH정의부터 Clone까지

[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL13-SSH-작성중)

<br>


## ✔️Intro

AWS EC2를 이용한 배포를 할 때 SSH에 대해 들어본 후, 회사 입사 후 Repository를 Clone받을 때 한 번 더 봤습니다. 그래서 SSH에 대해 알아보고, 저희 회사 Repository를 제 개인 노트북에 Clone받도록 하겠습니다.

<br>

## ✔️SSH란?

Secure SHell의 줄임말로, 두 컴퓨터 간 통신을 할 수 있게 해주는 Protocol입니다. 프로토콜은 HTTP에 대해 처음 공부를 하면 듣게 되는 단어로 한 번씩은 들어보셨을겁니다. 다 알고 계시겠지만 복습 겸 다시 정의에 대해 설명하면, 프로토콜이란 서로 다른 통신 장비 간 주고 받는 데이터의 양식과 규칙을 말합니다. 우리가 HTTP통신을 할 때 프로토콜을 사용하듯이, Shell 통신을 하기위해 필요한 프로토콜입니다.
그러면 SSH를 왜 사용하는 걸까요? 천천히 알아보겠습니다.

출처는 [SSH 홈페이지](https://www.ssh.com/academy/ssh/protocol)와 [GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)입니다.

<br>

## ✔️Typical uses of the SSH protocol

SSH Protocol은 아래와 같은 이유로 기업 네트워크에서 이용됩니다.

* 유저와 자동화 프로세스에 대한 보안 엑세스 제공
* 대화식 및 자동화된 파일 전송 
* 원격 명령 실행
* 네트워크 인프라 및 기타 핵심 시스템 구성 요소 관리

<br>

## ✔️How does the SSH Protocol work

프로토콜은 Client-Server 구조로 작동하며, SSH Client를 SSH Server로 연결되어 있습니다. SSH Client는 연결 설정 프로세스를 구동하고 공개 키 암호화를 사용하여 SSH 서버의 ID를 확인합니다. 그 후 설정 단계에서 SSH 프로토콜은 강력한 대칭 암호화 및 해싱 알고리즘을 사용하여 Client-Server 사이에 교환되는 데이터의 개인 정보와 무결성을 보장합니다. 이 내용을 그림으로 나타내면 아래와 같습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FCGIXz%2Fbtru1bEMx4B%2FhgdM1WbfcEaihUm4khKgXk%2Fimg.png)

<br>

## ✔️Adding a new SSH Key to GitHub Account

SSH 발급 순서를 알아보겠습니다.
첫 번째로 ssh 키가 있는지 체크합니다.
```shell
ls -al ~/.ssh
```
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FKJeXJ%2Fbtru8V2CgsT%2Fv3odkl5wDyPLJ0uKIj4IgK%2Fimg.png)

<br>

id_rsa.pub 이라는 파일이 존재하면 이미 있다는 뜻입니다.
저는 없으니 생성해보겠습니다.

확인 후 두 번째로 SSH Key를 생성합니다.
```shell
ssh-keygen -t rsa -b 4096 -C "GitHub 이메일 주소"
```
제 이메일 주소에 맞게 입력해보겠습니다.

그러면 Generating public/private rsa key pair 이라는 문구와 함께 Enter로 시작하는 명령어가 3가지 나옵니다.
그 때 아무것도 입력하지 말고 엔터만 계속 누르면 키가 생성됩니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbMR4sZ%2Fbtru6M6sE9N%2FqLakgzJVIgyx1VlsbaeRJk%2Fimg.png)

<br>


생성한 SSH Key를 복사합니다. GitHub에서 추가해주기 위함입니다.
아래의 내용을 그대로 복사해서 터미널에 붙여넣기 하시면 됩니다.

```shell
pbcopy < ~/.ssh/id_rsa.pub
```


이제 GitHub로 가겠습니다.

하기전에 먼저, Git Docs의 순서대로 하면 대부분 아래와 같은 문구를 보시게 될 겁니다. 그래서 키 체크하는 과정부터 추가하게 되었습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FM6VXo%2Fbtru3slFeiN%2Fs2StwjZAKtYsS5y4eqLtg1%2Fimg.png)

<br>

GitHub 우측 상단의 내 프로필 선택 후 Settings를 눌러줍니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fzm7fM%2FbtruXUXZwez%2FVetn5dRPtd11PgG5W9jQvk%2Fimg.png)


그 다음 Access의 SSH and GPG Keys를 눌러서 New SSH Key를 클릭합니다.
저는 회사 Repository를 Clone할 때 생성한 게 하나 있는 상태입니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fd4GN87%2FbtruTDB1tVH%2FRDMxGjs0eIMB8R1f6h1TO1%2Fimg.png)

<br>

Title에는 본인이 이해할 수 있는 이름을 넣고, Key에는 아까 pbcopy를 한 키 값을 넣습니다. 저는 Title에 kymac_ssh라고 작성했습니다. 복사한 키 값은 pbpaste라는 명령어를 통해 확인할 수 있으며 저 부분 전체를 복사하여 붙여넣기 해줍니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbRMp1l%2Fbtru8UJpQPZ%2FxLF6f7Lj3jMnirmM0jpLuk%2Fimg.png)


그러면 암호를 입력하라고 나오는데, 입력 후 Confirm Password를 클릭합니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FKIOQ3%2FbtruQrV8IL9%2FMxQIr9UKVMvGuAfoTUWft0%2Fimg.png)

<br>

그러면 이제 새로 추가한 SSH 커넥션이 제대로 연결되었는지 확인하겠습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fcg9yUq%2FbtrvbgLIFqP%2FkIyiWYjYFNOGRwa2vLXsg1%2Fimg.png)

이렇게 나오게 되면 완료가 되었다는 뜻입니다.  그러면 이제 이걸 이용해서 Clone을 해보겠습니다. 지금 Never used 즉, 사용되지 않았다는 의미입니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbKJoix%2Fbtru9PgQioV%2F791PgNEMxrCqgJQkvW5Rkk%2Fimg.png)

<br>



Clone을 할 때 SSH로 클론을 받습니다. 여기는 저희 회사 Repository이며, 일반적으로 Clone 받는 것과 같이 명령어를 입력해줍니다. 그렇게 되면 Clone이 받아지고 SSH Key가 자동으로 사용되어진 걸 확인할 수 있습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FooG8w%2Fbtru3sAPKRu%2Fb2kr0QlLqjHpn3KoXqwRh0%2Fimg.png)

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FoqNss%2Fbtru9PODKIM%2FkNKFXqau68GKNlSbHSk5z0%2Fimg.png)

<br>


✔️마치며..

입사 첫 날 사수분께서 Clone받는 걸 알려주셨는데, 이전까지는 Clone을 Public Repository만 받다보니 이렇게 하는 건 처음이었습니다. 그래서 나중에 꼭 공부해서 숙지해야겠다고 마음 먹었습니다. 다음에 들어올 신입에게도 멋지게 세팅해주고 싶은 마음도 컸습니다. 단순한 Clone뿐만 아니라 개념에 대해 이해하는 시간을 가지게 된 뜻 깊은 포스팅이었습니다.
