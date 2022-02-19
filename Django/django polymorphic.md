# django polymorphic

<br>

## Intro
객체지향을 공부하실 때 다형성은 많이 들어보셨을겁니다. 이 다형성이라는 단어를 영어로 바꾸면 바로 Polymorphic이 됩니다.
정확한 단어는 phism인데, 이 부분은 유도리있게 넘기도록 하겠습니다.



하나의 객체가 여러 가지 타입을 가질 수 있다는 뜻인데, 요즘 이 부분에서 문제가 생겨서 작업에 들어갈 예정입니다.
그래서 미리 공부할 겸 포스팅하게 되었습니다.



모든 출처는 [공식문서](https://django-polymorphic.readthedocs.io/en/stable/quickstart.html)입니다.

<br>

## Polymorphic Start!


첫 번째로, django-polymorphic을 설치해줍니다.

```shell
pip install django-polymorphic
```

<br>

두 번째로, settings.py의 INSTALLED_APPS에 설치한 polymorphic을 추가해줍니다.

```python
INSTALLED_APPS = [
...,
"polymorphic"
]
```

<br>

세 번째로 모델링을 해줍니다. 마이그레이션까지 해주는 거 잊지마세요!
```python
from polymorphic.models import PolymorphicModel

class Project(PolymorphicModel):
    topic = models.CharField(max_length=30)

class ArtProject(Project):
    artist = models.CharField(max_length=30)

class ResearchProject(Project):
    supervisor = models.CharField(max_length=30)
```


네 번째로 Shell_Plus를 켜서 데이터 생성 및 조회해보겠습니다.


<br>

```python
Project.objects.create(topic="폴리모픽을 주제로한..")
ArtProject.objects.create(topic="예술프로젝트를 주제로한..", artist="아트록스")
ResearchProject.objects.create(topic="연구프로젝트를 주제로한..", supervisor="디지몬카이저")
```

그리고 프로젝트를 모두 가져오기 위해

```python
Project.objects.all()
```

을 입력하면 ArtProject, ResearchProject까지 모두 조회됩니다.



A라는 모델을 B라는 모델이 상속받고, 각 모델마다 데이터를 한 개씩 만들었다면 A모델을 조회했을 때 B라는 모델도 A를 이용해서 만들었으므로 조회가 되는 것입니다.

이건 폴리모픽에 상관없이 해당하는 Django의 특징입니다.


![image](https://user-images.githubusercontent.com/88086271/154788661-c85d0b60-246e-4743-84b3-0caf2b72443e.png)


Instance_of 와 not_instance_of를 이용하여 결과를 특정 하위 모델로 좁혀 값을 뽑을 수 있습니다.

이 두 메소드가 폴리모픽의 가장 큰 특징이며, 폴리모픽을 상속받지 않으면 위 두 메소드를 사용할 수 없습니다.



아래 이미지를 보면 Profile이란 모델을 Developer가 상속받게 만들은 뒤 테스트를 해봤는데, instance_of라는 특성이 없다고 나옵니다.



이렇게 폴리모픽을 설정한 모델이 없다면, filter를 타고 들어가거나, prefetch_related를 이용해서 접근해야 합니다.

하지만 폴리모픽이 설정되어 있다면 instance_of / not_instance_of를 이용해 연관된 데이터를 모두 뽑아낼 수 있는 것입니다.



![image](https://user-images.githubusercontent.com/88086271/154788665-b4d9655d-93f8-41b5-8249-4ae612bf43ed.png)



![image](https://user-images.githubusercontent.com/88086271/154788675-07023d39-5226-45da-80f2-395de4064c1b.png)





데이터를 보니 instance_of, not_instance_of 모두 prefetch_related와 같은 느낌을 받았습니다.

기준 모델에 대해 SQL을 한 번 보여준 다음, instance_of에 들어간 모델에 대해 SQL이 호출되기 때문인데요.

첫 번째 발생한 SQL에서 polymorphic_ctype_id는 django_content_type에 저장된 ID를 불러옵니다.


왜 django_content_type의 id를 불러오냐면, import한 PolymorphicModel의 특성에 나와있습니다.

![image](https://user-images.githubusercontent.com/88086271/154793151-18585bf0-ba36-4e8e-8c60-26898be335b4.png)


Polymorphic모델로 타고 들어가면 모델에 polymorphic_ctype이라는 컬럼이 있고, ContentType이라는 모델을 참조합니다.

ContentType으로 한 번 더 타고 들어가면 테이블명(db_table)이 django_content_type으로 설정되어 있는 걸 확인할 수 있습니다.



이렇게 모델이 연결되어 있기 때문에 django_content_type의 ID를 불러오게 되는 것입니다.


![image](https://user-images.githubusercontent.com/88086271/154793154-71204787-3e5b-4d39-be0f-0b7bc1af6f2d.png)




그리고 제 DB에 저장된 django_content_type의 17번은 instance_of에 들어간  ArtProject입니다.

Project에서 ArtProject로 접근하기 위해 where 조건에 추가되어 SQL이 나가는 것 같습니다.


![image](https://user-images.githubusercontent.com/88086271/154788680-b220539b-50fb-4235-8c4b-d9d1896c80d2.png)



Project에서 ArtProject로 접근할 수 있게 최초 쿼리에서 조건을 걸었으니, 그 다음 쿼리에서는 그에 맞는 ArtProject가 나오게 되는 것입니다.

그런데 여기서 prefetch_related와는 다른 점이, prefetch_related는 look_up에 해당하는 쿼리를 발생시키는데, 여기서는 기준이 되는 모델과 instance_of에 설정한 모델의 컬럼들이 모두 추출됩니다.

그리고 Join의 On 조건에서 보면 알 수 있듯이, ArtProject의 project_prt_id는 Project의 ID값이 됩니다.



<br>

## instance_of 조금 더 파헤치기


그러면 여기서 instance_of에 여러 개 혹은 관련없는 모델이 들어간다면 어떻게 될까 궁금해졌습니다.

SQL을 보면 Inner Join을 통해 데이터가 절대 안나올 것 같지만 그래도 한 번 해봤습니다.


![image](https://user-images.githubusercontent.com/88086271/154788695-dc33629d-9fd6-4db4-995b-e1c9585d6c05.png)



![image](https://user-images.githubusercontent.com/88086271/154788699-2e27d9bb-bce8-4598-9f7a-82c113630d9c.png)





네 역시 Join 조건에서 걸러지게 되어 User 부분은 아무 데이터도 안나오게 됩니다.



not_instance_of는 이 모델은 제외하고 출력해달라는 뜻이어서 해당 모델만 제외하고 결과가 리턴됩니다.


<br>

## 마치며..

회사 내 코드 중 폴리모픽이 설정된 모델들이 있는데, 그렇다보니 서로 의존성이 너무 강해 폴리모픽 제거 작업을 하게 되었습니다.

물론, 혼자하는 게 아닌 팀원들과 함께 하게 되는데, MSA에 맞게 관리하기로 방향이 정해지면서 가장 먼저 최초로 하게 된 프로젝트(?)가 되었습니다.



화이팅!
