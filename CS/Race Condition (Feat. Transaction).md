# Race Condition (Feat. Transaction)

[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL18-Race-Condition-Feat-Transaction)

<br>


## ✔️경쟁조건

Race Condition을 번역하면 경쟁조건이라는 말이 됩니다. 경쟁조건이란 둘 이상의 입력 또는 조작의 타이밍이나 순서 등이 결과 값에 영향을 줄 수 있는 상태를 말합니다. 

조회수를 예로 들어보겠습니다. 현재 조회수는 1입니다. 만약 A라는 스레드와 B라는 스레드가 동시에 클릭을 한다면? 이론 상 조회수는 3이 되어야 하지만 그렇지 않습니다. A, B 모두 기존의 조회수는 1이었으므로 조회수가 2가 됩니다. 이렇게 타이밍이나 순서에 결과 값에 영향을 주는 이런 상황을 경쟁 조건이 발생했다고 하는 것 입니다.

그래서 이 상황을 피하기 위해 사용하는 것이 트랜잭션입니다. 트랜잭션을 설정하면 ACID의 I에 해당하는 고립성의 특징때문에 A, B 스레드가 동시에 작동할 수 없게됩니다. 둘 중 하나가 먼저 실행되고 완료되어야 다른 스레드를 실행할 수 있기 때문입니다. 

<br>

## ✔️경쟁조건 In Django

Django에서는 특정 상황에서 경쟁조건을 피하기 위해 F객체를 사용합니다. F객체는 사칙연산을 이용한 데이터 수정이 이루어질 때 사용하며 아래 포스팅 내용의 출처는 [공식문서](https://docs.djangoproject.com/en/4.0/ref/models/expressions/#f-expressions)입니다.

F객체란 위에서 언급했듯이 수정이 이루어질 때 사용합니다. 단, F객체만의 특징으로는 DB에 직접 접근한다는 것이 있습니다.
값을 수정할 때 Django내의 코드를 통해 바로 수정하는 것과 DB에 접근하여 수정하는 두 가지 방법이 있는데 후자의 방법이 F객체의 수정 방식이며, save() 혹은 update() 메소드가 실행될 때 DB로 접근해서 수정이 이루어진다는 의미입니다. 아래 이미지는 공식문서에서 나온 설명입니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FBpoQy%2FbtrvPI8ArbW%2FhhxJTeXcMt09KkDZRHeyf0%2Fimg.png)

그러면 이제 예시를 들어서 F객체를 이용했을 때와 안 했을 때의 수정 방법에 대해 설명드리겠습니다.


<br>

## ✔️ F객체를 미사용했을 때 수정 방법

우선, 모델링입니다. 포스팅이란 모델을 만들고 제목, 내용, 조회수로 구성되어 있습니다.
```python
from django.db import models


class Posting(models.Model):
    title = models.CharField(max_length=20)
    text = models.TextField(max_length=500)
    view = models.PositiveIntegerField()
    
    class Meta:
        db_table = 'postings'
```

<br>

그리고 View입니다. 클릭했을 때 조회수가 1 늘어나도록 수정해주는 코드입니다. 간단하게 만들었습니다.

특정 포스팅(게시글)을 클릭해야 하므로 URL에서 Path Parameter로 포스팅의 ID를 받으며, 저는 받을 때 변수를 posting_id로 해서 받겠다고 지정했습니다. 그리고 ID가 일치하는 포스팅 객체를 가져온 뒤, 조회수를 기존의 조회수에서 +1 해주도록 했습니다.

```python
class RaceConditionView(View):
    def put(self, request, *args, **kwargs):
        try:
            posting = Posting.objects.get(id=kwargs["posting_id"])
            posting.view = posting.view + 1
            posting.save()
        
        except Posting.DoesNotExist:
            return JsonResponse({'message':'POSTING_DOES_NOT_EXIST'}, status=400)
            
        else:
            return JsonResponse({'message':'update success'}, status=201)
```
그 결과입니다. 이미지가 안보일 것 같아서 그대로 복사해서 붙여넣었는데, view=2로 +1이 되어 들어갑니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fd79eIg%2FbtrvL5qiH7y%2FGhKEODse67K44e2kRD3fKK%2Fimg.png)

```shell
2022-03-12 17:40:21,471 DEBUG (0.001) SELECT "postings"."id", "postings"."title", "postings"."text", "postings"."view" FROM "postings" WHERE "postings"."id" = 1 LIMIT 21; args=(1,); alias=default
2022-03-12 17:40:21,472 DEBUG (0.001) UPDATE "postings" SET "title" = '경쟁조건 테스트', "text" = '내용입니다', "view" = 2 WHERE "postings"."id" = 1; args=('경쟁조건 테스트', '내용입니다', 2, 1); alias=default
```

<br>

## ✔️F객체를 사용했을 때 수정 방법

F객체를 이용한 수정 로직에 대한 View입니다.
```python
class NonRaceConditionView(View):
    def put(self, request, *args, **kwargs):
        try:
            posting = Posting.objects.get(id=kwargs["posting_id"])
            posting.view = F('view') + 1
            posting.save()
        
        except Posting.DoesNotExist:
            return JsonResponse({'message':'POSTING_DOES_NOT_EXIST'}, status=400)
            
        else:
            return JsonResponse({'message':'update success'}, status=201)
```

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb90icS%2FbtrvLBh6Y7g%2FB1TcZBgKXWmlYujHcMTzzk%2Fimg.png)

```shell
2022-03-12 17:46:38,706 DEBUG (0.001) SELECT "postings"."id", "postings"."title", "postings"."text", "postings"."view" FROM "postings" WHERE "postings"."id" = 1 LIMIT 21; args=(1,); alias=default
2022-03-12 17:46:38,709 DEBUG (0.002) UPDATE "postings" SET "title" = '경쟁조건 테스트', "text" = '내용입니다', "view" = ("postings"."view" + 1) WHERE "postings"."id" = 1; args=('경쟁조건 테스트', '내용입니다', 1, 1); alias=default
```

view의 수정 로직이 postings.view + 1로 수정된 게 보이시나요? DB에 직접 접근해서 수정을 한다는 의미입니다. 첫 번째 작성한 코드는 posting_id를 통해 가져온 객체의 view가 1이기 때문에 +1을 해서 바로 2라는 값을 넣어줍니다. 이 후에 해당 객체에 대해 어떠한 작업이 일어나든 가져온 그 순간의 조회수는 1이기 때문에 1+1해서 2가 되어 경쟁조건이 발생합니다. 하지만 두 번째 작성한 코드는 DB에 직접 접근을 하기 때문에 객체를 가져온 뒤 무슨 일이 있었는지 파악할 수 있게 됩니다. 

이렇게, 경쟁조건에 대해 알아보고 Django내에서 수정을 하는 방법들과 어떻게 작동되는지 알아보았습니다. 읽어주셔서 감사합니다.
