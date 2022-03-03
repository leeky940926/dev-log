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
