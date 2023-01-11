# linux에서 redis permission denied 문제 해결

[티스토리 블로그 바로 가기](https://kyleeee.tistory.com/entry/TIL42-linux에서-redis-permission-denied-문제-해결)

## 문제 

EC2 인스턴스 내에서 ```redis.conf``` 파일에 접근하기 위해 ```cd /etc/redis```를 입력했더니 ```-bash: cd: redis: Permission denied``` 라며 접근할 수 없었다

<br>

## 해결

```chmod 777 redis```를 하니 됐다.

777 모드는 모든사용자가 쓰기, 읽기, 실행하도록 바꿀수 있어서 지양한다고 해서 다른 모드 변환을 고려해봐야 할 것 같다
