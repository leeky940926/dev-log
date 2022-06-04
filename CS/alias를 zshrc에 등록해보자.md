# alias를 zshrc에 등록해보자


카테고리를 뭘로 설정해야 할 지 몰라 CS로 했습니다..

긱설하고 매번 터미널을 켜면 개발 폴더에 들어간뒤, 가상환경을 켜는 게 귀찮아서 alias에 등록을 했는데 다른 분들도 알고 게시면 좋을 것 같아서 작성하게 되었습니다.

alias 등록하러 바로 슈웃

<br>

## 순서

일단 아래 명령어를 입력을 해야겠죠?

```shell
vi ~/.zshrc
```

mdmy접속 후 쭉 내려보면 ohmyzsh, zshconfig의 alias 예시가 보일겁니다.

<br>

<img width="425" alt="image" src="https://user-images.githubusercontent.com/88086271/171987824-abed4434-e647-4378-a39f-423b1801e7b3.png">

<br>

그리고 아래에 제가 사용 할 alias들을 추가했습니다.

<br>

<img width="609" alt="image" src="https://user-images.githubusercontent.com/88086271/171987869-2266043b-d417-4869-a5e6-07f8dc10eca4.png">

```dockers```라고 입력하면 Desktop -> Development -> dockers 라는 폴더로 이동한 뒤, dockers라는 가상환경을 켜고

```dapi```라고 입력하면 dapi라는 가상환경을 켜고, Desktop -> Development -> blog 라는 폴더로 이동합니다.

```deactivate```라고 입력하면 가상환경을 종료합니다.

이렇게 입력 후 나와서 ```source ~/.zshrc``` 를 입력해줍니다.

그리고 잘 되나 봐야겠죠? 터미널을 닫은 뒤 다시 켜봤습니다.

<br>

보시면, ```dapi```라고 입력하니 가상환경이 켜지고 제가 입력한 디렉토리로 이동합니다.

```deactivate```를 입력하니, 가상환경이 종료되면서 옆에 ```dapi```가 사라지는 것도 확인할 수 있습니다.

마지막으로 ```dockers```를 입력하면 ```dockers```라는 가상환경이 켜지고 제가 설정한 디렉토리로 이동한 걸 확인하실 수 있습니다.

<img width="642" alt="image" src="https://user-images.githubusercontent.com/88086271/171987970-06c9281d-971d-4e50-83bf-8f60f94ba214.png">


