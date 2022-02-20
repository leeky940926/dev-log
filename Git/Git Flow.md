# Git Flow

[티스토리 블로그 바로가기](https://kyleeee.tistory.com/entry/TIL2-Git-Flow)

<br>

## Intro


2021년 8월, 개발자의 꿈을 가지고 위코드에 처음 들어선 후 Git을 배울 때 add - commit - push - pull 이 순서가 Git Flow 인 줄 알았습니다.

master에서 브랜치 하나 파고 거기서 commit - push를 했기 때문인데, 당시에 멘토님께서 주신 글 하나를 읽고 내가 잘못알고 있었구나를 느꼈습니다.

[배달의 민족에서 작성한 Git Flow에 대한 글](https://techblog.woowahan.com/2553/)이었는데 취업 후 웹개발자로서 다시 한 번 읽는 시간을 가졌습니다.

지금 포스팅은 이 글에 대한 요약이 아닌, 일반적인 Git Flow에 대해 다시 한 번 곱씹어보고 정리해보려고 합니다.



목차는



1. Flow
2. branch 

<br>


순서이며, branch에서는 branch별 역할에 대해 작성했습니다.


<br>


## Flow


![image](https://user-images.githubusercontent.com/88086271/154072298-2716ac5b-5525-4f02-8f6b-0cab744e5a21.png)



출처 : https://nvie.com/posts/a-successful-git-branching-model/



Git Flow를 검색하면 가장 익숙하게 보는 이미지일 것입니다.

<br>

## branch


위코드에서는 두 번의 프로젝트를 하는데 1차 프로젝트 때는 아까 말한대로 master에서 브랜치를 만들었습니다.


그리고 2차 프로젝트 때, 배달의 민족 글을 보고 develop라는 브랜치를 만들어서 실제처럼 해보고 싶었는데 아무래도 기간이 2주밖에 되지 않고 각각의 실력이 다르다보니 도입하지 못 했습니다.


그래서 수료 후 지금 회사에 취업하고 본격적인(?) Git Flow에 대해 적용해볼 수 있었습니다.


이미지에서 보이는 것처럼, 브랜치는 총 5단계로 나뉘어집니다.



다만, 여기서 경우에 따라 바로 develop -> production으로 하는 곳도 있어서 release는 설명은 생략하겠습니다.



* production
* develop
* release
* feature
* hotfix

<br>

## Production


Production이라는 이름을 사용하지만 master입니다. 우리 서비스가 실제로 제공되는 코드의 브랜치여서 Production이라고도 부릅니다.


여기서 develop라는 브랜치를 생성하게 됩니다.

<br>



## Develop


말 그대로 우리가 개발한 내용이 우선적으로 적용되는 브랜치입니다. 회사마다 다른 말을 쓰는데, 제가 다니는 회사는 스테이징 단계라고도 합니다. 


우리가 개발한 내용을 적용하기 위해서는 Develop 브랜치로 이동 후 여기서 feature 브랜치를 만들어서 작업을 해야 합니다.



<br>

## Feature


feature 브랜치는 Git을 단 한 번이라도 다뤄본 사람이라면 굉장히 익숙하실 이름입니다.


내가 작업할 기능을 담당하는 브랜치이며 여기서 작업 후 Develop 브랜치로 Pull Request를 보내야 합니다.


그런데 몰랐던 게, push 명령어를 먼저 보겠습니다

```shell
git push origin feature/users
```

예를 들어 내가 master에서 만든 브랜치가 feature/users 이라면 origin에 내가 작업한 내용을 올린다는 의미로 명령어를 썼기 때문에 Develop에 올릴 때 명령어를 아래처럼 바꿔야 하는 줄 알았습니다.

```shell
git push develop feature/users
```



예를 들어 아래이미지를 보면 main -> develop -> feature/devtest 라는 브랜치를 만들었습니다.

![image](https://user-images.githubusercontent.com/88086271/154072589-d7ff4cd7-a168-4c4a-8821-1afd6b604b29.png)





feature에서 수정 후 add - commit 한 다음 develop로 push를 날리면, 아래 문구가 떴는데 회사에서 제가 이 메시지를 본 것입니다.


그래서 사수분께 내가 develop로 push했더니 이런 문구가 떠서 안되는데 이거 PR Template에서 직접 base를 수정해야 되냐고 물어봤었습니다.



돌아온 대답은 그렇다 였습니다. 그래서 PR날릴 때 잘 확인해야 한다고 이야기를 저에게 해주셨습니다.

![image](https://user-images.githubusercontent.com/88086271/154072774-62c423a4-96cb-492e-8a96-ab041d2c2967.png)



PR을 날리면 기본으로 base에 main이 설정되어 올라가는데, 이걸 develop로 바꿔서 올려야 한다는 것입니다.



![image](https://user-images.githubusercontent.com/88086271/154072830-ecc2a127-86ff-48d4-afaa-8db1a2786c3c.png)


그렇게 올리면 develop -> main으로 자동으로 Pull Request가 올라가고 여기서 최종 Production으로 적용되는 것입니다.


![image](https://user-images.githubusercontent.com/88086271/154072847-43c8a9aa-f173-4436-8bcd-37936b48d2c1.png)





## HotFix


서비스가 돌아가던 중 문제가 생겨서 급하게 해결해야 할 때 사용하는 브랜치입니다.



production에서 브랜치를 만들어서 바로 적용시키면 되는, 일반적으로 맨 처음 Git에 입문할 때 master에서 바로 브랜치를 만들어서 작업 후 올리는 것과 같은 맥락입니다.





## 마치며..


여기까지가 기본적인 GIt Flow입니다.



release는 production과 develop사이의 중간과정이며, 말 그대로 배포를 앞둔 브랜치여서 아까 말했듯이 생략했습니다.



제가 취업준비할 때 Git Flow에 대해 아냐고 질문을 받았었습니다.



만약, 제대로 공부하지 않고 add commit.. 이랬으면 당연히 떨어졌겠죠? 



개발자라면 꼭 알고 있어야 하는 Git Flow, 회사마다 조금씩 상이하지만 확실한 건 저 형태가 기본적인 것이라는 겁니다.



이상 글 마치겠습니다.
