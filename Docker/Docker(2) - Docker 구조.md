# ✔️ Docker 구조

[티스토리 바로가기](https://kyleeee.tistory.com/entry/TIL24-Docker-2-Docker-구조?category=1055770)

<br>

도커는 크게 4가지로 분류되어 있습니다.

* Docker client와 server (server는 docker engine으로 불리기도 합니다.)
* Docker 이미지
* Docker registries
* Docker containers

<br>

## ✔️ 1. Docker client와 server

도커는 기본적으로 서버와 클라이언트 구조로 이루어져 있습니다. 클라이언트가 서버에 명령을 전달하고 서버가 실행시키는 구조입니다. 우리가 일반적으로 개발할 때 요청-응답과 같은 개념으로 이해하시면 됩니다. <br>

Docker Binary 커맨드가 도커 클라이언트고 dockerd가 Docker Daemon 혹은 Docker engine입니다. Docker engine과 상호작용 하기 위해 Restful API도 제공됩니다. 클라이언트와 서버는 동일한 호스트 안에서
운영될 수 있으며 서로 다른 호스트에서 운영할 수 있습니다. 

즉, API요청을 도커 데몬이 수신하고, 이미지, 컨테이너 등과 같은 도커 객체를 관리합니다. 

<br>

## ✔️ 2. Docker Image

도커 이미지는 애플리케이션 코드 뿐만 아니라 애플리케이션 실행에 필요한 최소한의 환경들을 포함하고 있는 바이너리 파일입니다. 하나의 이미지에 여러 Container를 띄울 수 있어 확장성이 좋습니다.
하지만 하나의 컨테이너를 띄우는 걸 권장한다고 합니다. 

<br>

## ✔️ 3. Docker registries

Docker Image를 저장하는 Repository라고 보면 됩니다. 소스코드를 Github에 저장하여 관리하듯 Docker Image는 Dockerhub 같은 Docker Registries에 저장한다고 생각하시면 됩니다. 
Github와 마찬가지로 public, private가 있습니다. 

<br>

## ✔️ 4. Docker Containers

Docker Image가 실행되는 가상화 공간을 의미합니다. 컨테이너 관련 내용은 [이전 포스팅](https://github.com/leeky940926/dev-log/blob/main/Docker/Docker(1)%20-%20Container.md)에 자세히 설명되었으니 스킵하겠습니다.

<br>

## ✔️ 5. Docker Compose and Swarm

Docker에서 여러 Docker Container들로 이루어진 stack이나 cluster들을 관리하는 서비스도 제공하는데, 이를 Docker Compose와 Docker Swam이라고 합니다.

Docker Compose는 복수의 Docker container들을 모아서 종합적인 Application Stack을 정의하고 운영할 수 있도록 해주는 서비스입니다. Compose 파일을 사용하여 전체적인 Application 서비스를 설정한 후,
Application을 이루고 있는 각각의 컨테이너들(Web Server Container, API Server Container 등)을 따로 실행시킬 필요없이 한 번에 생성, 실행할 수 있도록 해줍니다.

Docker Swam은 Docker Containers들로 이루어진 cluster들을 관리할 수 있도록 해주는 서비스입니다. 즉, Docker Container를 위한 Clustering Tool입니다.

Cluster : 도커에서 클러스터란 Workload를 실행하고 높은 가용성을 제공하기 위해 함께 작동하는 시스템 그룹을 의미합니다.
Workload : 고객 대면 애플리케이션이나 백엔드 프로세스 같이 비즈니스 가치를 창출하는 리소스 및 코드 모음을 의미합니다.
Compose : 기본적으로 Compose는 도커내에서 다양한 Application들을 정의하고 실행하는 방법 중 하나입니다. Compose를 통해 여러 컨테이너를 하나의 이미지에서 정의할 수 있습니다.

<br>



