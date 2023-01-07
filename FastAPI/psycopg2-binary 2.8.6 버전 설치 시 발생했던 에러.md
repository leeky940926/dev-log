# psycopg2-binary 2.8.6 버전 설치 시 발생했던 에러

어떻게 이름을 써야할 지 몰라 제목을 이렇게 쓰게 되었습니다.

[티스토리 블로그 바로 가기](https://kyleeee.tistory.com/entry/TIL41-psycopg2-binary-286-버전-설치-시-발생했던-에러)

<br>

## 문제

FastAPI 프로젝트를 위해 psycopg2-binary를 설치하는데, 2.8.6 버전으로 설치하려고 하니 에러가 발생했습니다

```shell
ld: library not found for -lssl
clang: error: linker command failed with exit code 1 (use -v to see invocation)
error: command '/usr/bin/clang' failed with exit code 1
[end of output]

note: This error originates from a subprocess, and is likely not a problem with pip.
error: legacy-install-failure

× Encountered error while trying to install package.
╰─> psycopg2-binary
```

<br>

## 해결

우선, Postgresql 설치(했다면 스킵)

```shell
brew install postgresql
```

<br>

파이썬 버전 확인

```shell
python --version

-> Python 3.9.4
```

<br>

아래 명령어 적용

```shell
export CPPFLAGS="-I/opt/homebrew/opt/openssl@1.1/include"
export LDFLAGS="-L/opt/homebrew/opt/openssl@1.1/lib -L${HOME}/.pyenv/versions/3.9.4/lib"
```

<br>

다시 설치

```shell
pip install psycopg2-binary==2.8.6
```