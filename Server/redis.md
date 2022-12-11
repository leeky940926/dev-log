# redis

[티스토리 블로그 바로 가기](https://kyleeee.tistory.com/entry/TIL38-redis)

## Intro

<br>

celery 공식문서를 보며 설정하던 중, 브로커로 redis, rabbitmq, aws sqs 등이 있는데 저는 redis를 선택했었습니다.

그 이유로, 처리 가능한 데이터의 양이 redis < rabbitmq < sqs 순인데 지금 수준에선 rabbitmq를 쓰지 않아도 redis로 충분히 커버 가능하기 때문입니다.

그래서 서버에 요청이 들어올 경우, 브로커인 redis를 거쳐 worker로 분배합니다.

최종적으로 요청을 실행하는 프로세스를 woker라고 합니다.

간단한 플로우로 요약하면 ```유저 -> 서버 -> 브로커 -> 워커``` 이렇게 됩니다.

여기서 브로커가 전달하는 작업을 메시지라고 합니다.

그런데, ```브로커 역할인 레디스가 어떻게 전달해주지?``` 라는 궁금증이 생겼고 이걸 찾아보기로 했습니다.

모든 출처는 [레디스 공식문서](https://redis.io/docs/)입니다.

<br>

## What is Redis

<br>

레디스는 데이터베이스, 캐시, 메시지 브로커 등을 메모리 안에 저장할 수 있는 데이터 구조 저장소입니다.

메모리에 데이터를 저장하기 때문이 인메모리 데이터 베이스라고 이해하면 될 것 같습니다.

메모리 기반이어서 재부팅시 데이터가 없어질 수 있고, 일시적으로 사용하기 편하기 때문에 흔히 캐시 저장소로 많이 쓰이는 이유기도 합니다.

그리고 key-value 형태로 데이터를 저장하고 있는데, 형태의 제한이 없어 데이터 구조 저장소라고 불리는 이유기도 합니다.

GraphQL을 잠시 공부할 때, NoSQL의 가장 큰 특징은 key-value형태로 데이터를 저장한다라고 배웠는데 그 기준으로 맞추면 NoSQL 분류될 수 있습니다.

<br>

## How to

그러면 이제 레디스를 설정해보겠습니다. 우선 당연히 설치부터 진행되어야겠죠? 아래와 같이 명령어를 입력합니다.

```shell
brew install redis
```

설치 후, redis가 잘 실행되는지 아래 두 명령어를 통해 확인합니다.

```shell
redis-server (레디스 서버 실행)
redis-cli (레디스 접속)
```

<br>

<img width="629" alt="image" src="https://user-images.githubusercontent.com/88086271/206892422-e7df6d72-f8bc-4a6a-b0f0-eebec90b52f1.png">

<br>

첫 명령어를 입력했을 때 위 이미지 같이 나오고, 두 번째 명령어 입력했을 때 ```127.0.0.1:6379 >``` 이렇게 나온다면 정상작동이 되고 있는 것입니다.

key-value형태로 저장하는 레디스에 데이터를 한 번 설정해보겠습니다.

```shell
set redis-keyname "welcome to redis"
get redis-keyname
```

<br>

<img width="445" alt="image" src="https://user-images.githubusercontent.com/88086271/206892631-ca2cdeed-0901-4ee8-8d16-eeff56d18e36.png">

이렇게 key의 이름을 정하고 값을 입력한 뒤, get keyname(키 이름)을 하면 내가 저장한 값을 가져옵니다.

레디스의 기본 ttl(time to live, 시간제한)은 영구적으로 존재하기 때문에(즉, 시간제한 없음), 삭제하려면 두 가지 과정을 거쳐야 합니다.

첫 번째로 ```flushall``` 입니다.

레디스에서 ```flushall```을 입력한 뒤 get keyname을 입력하면 ```(nil)```이라는 값이 나오게 됩니다.

우리가 알고 있는 null과 똑같습니다.

두 번째로 expire를 설정해주는 것입니다.

seconds자리에 

```shell
expire key seconds [NX/XX/GT/LT]
```

<br>

NX : 키가 만료되지 않은 경우에만 만료 설정
XX : 키에 기존 만료가 있는 경우에만 설정
GT : 새 만료가 현재 만료보다 큰 경우에만 만료 설정
LT : 새 만료가 현재 만료보다 작은 경우에만 만료 설정

<br>

그런데 키 하나하나 만료설정을 해주긴 리소스 문제가 있기 때문에, 이걸 일괄설정하거나 종류별로 설정할 수 있는데, 이 부분은 나중에 코드로 작성해보도록 하겠습니다.

그리고 redis 설정까지 완료했으니, 다음 포스팅엔 모니터링 설정과 비동기 task queue가 실행되는 과정을 작성해보겠습니다.








