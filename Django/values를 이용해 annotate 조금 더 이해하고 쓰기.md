# values를 이용해 annotate 조금 더 이해하고 쓰기

[티스토리 블로그 바로가기](https://kyleeee.tistory.com/entry/TIL34-values를-이용해-annotate-조금-더-이해하고-쓰기)

<br>

## 포스팅 계기

<br>

어제 파이콘에서 SQL원리를 알고 쓰는 aggregate와 annotate 세션 시간이 있었는데(정확한 제목 기억 안남), 그 때 "내가 이걸 쓰고 있지만 이걸 조금 더 최적화에서 쓸 수 있었구나!" 라는 걸 느끼게 한 세션이었습니다.

그래서 이번에 포스팅으로 남겨, 다른 사람에게 도움이 되었으면 좋겠고 나부터 최적화된 SQL쿼리를 만들기 위해 지금보다 더 생각하며 코드를 작성해야겠다고 생각했습니다.

<br>

## annotate()

<br>

제 개인적으론 Group By부터 ORM으로 구현하기 까다롭다고 생각하는 레벨이라 이 부분 특히 한 번 더 포스팅하면 좋겠다고 생각이 들었습니다.

예를 들어, 10개의 영화가 있고 10개의 영화에 대한 댓글이 총 800개라고 해보겠습니다.

한 영화에 80개씩은 아니고 어떤 영화의 댓글은 많고, 적을 수 있습니다.

모델링은 아래와 같습니다.

```python
class Movie(TimeStampModel):
    title = models.CharField(max_length=100)
    year = models.PositiveIntegerField()
    rating = models.DecimalField(max_digits=5, decimal_places=2)
    runtime = models.PositiveIntegerField()
    background_image = models.CharField(max_length=1000, null=True)

class Reply(TimeStampModel):
    movie = models.ForeignKey("movies.Movie", on_delete=models.CASCADE)
    title = models.CharField(max_length=40)
    content = models.TextField()
    rating = models.DecimalField(max_digits=3, decimal_places=1, null=True)
```

<br>

그러면 영화별 개수를 구할 때 일반적으로 count() 메소드를 많이 쓰게 됩니다.

예를 들어 아래와 같은 식으로 말이죠.

```python
Reply.objects.filter(movie_id=1).count()
```

```
SELECT COUNT(*) FROM reply WHERE movie_id=1
```

근데 이런 건 movie_id의 유동성을 고려하지 못한 쿼리입니다.

10개의 영화여서 movie_id를 계속 바꿔줘야 하는 불편함 때문입니다.

이럴 경우, annotate()를 써야 하는데, 가장 먼저 생각난 가장 쉬운 ORM은 이렇게 될 것 같습니다.

```python
Reply.objects.filter(movie_id=1).annotate(reply_count=Count("movie_id"))
```

```
SELECT "movies_reply"."id",
       "movies_reply"."created_at",
       "movies_reply"."updated_at",
       "movies_reply"."deleted_at",
       "movies_reply"."movie_id",
       "movies_reply"."title",
       "movies_reply"."content",
       "movies_reply"."rating",
       COUNT("movies_reply"."movie_id") AS "reply_count"
  FROM "movies_reply"
 WHERE "movies_reply"."movie_id" = 1
 GROUP BY "movies_reply"."id",
          "movies_reply"."created_at",
          "movies_reply"."updated_at",
          "movies_reply"."deleted_at",
          "movies_reply"."movie_id",
          "movies_reply"."title",
          "movies_reply"."content",
          "movies_reply"."rating"
```


하지만 이것도 movie_id의 유동성을 고려해주지 못하고, 그렇다고 filter()를 빼고 annotate() 하나만 쓰기엔 Reply 개수가 800개여서 800개가 그대로 호출됩니다.

<br>

## values()를 사용해서 대상화할 컬럼을 구체화 하자

Group By는 어떻게 보면 되게 명확하게 쿼리를 작성할 수 있게 해주는 구문입니다.

Group By - Having을 써야한다면 SELECT * FROM... 이렇게 작성하지 않고 컬럼들을 구체적으로 명시하기 때문이죠.

우리가 구현하려는 ORM의 쿼리는 아래와 같습니다.

```
SELECT movie_id, Count(movie_id) FROM reply Group By movie_id
```

Django ORM에서 특정 컬럼만 빼는 메소드가 어떤거였죠? 바로 values()입니다.

해당 쿼리문의 주인공은 movie_id 이므로 values()를 통해 우선 movie_id를 빼줍니다.

그러면 일단 ORM은 이렇게 됩니다. 

```python
Reply.objects.values("movie_id")
```
그러면 여기서 annotate만 붙여주면 우리가 작성하려는 쿼리가 끝이 납니다.

```python
Reply.objects.values("movie_id").annotate(reply_count=Count("movie_id"))
```

이렇게 되면 SQL은 어떻게 나올까요?

```
SELECT "movies_reply"."movie_id",COUNT("movies_reply"."movie_id") AS "reply_count"
FROM "movies_reply"
GROUP BY "movies_reply"."movie_id"
```

저희가 원한대로 나오게 되었습니다.

```python
<QuerySet [
{'movie_id': 1, 'reply_count': 79}, {'movie_id': 2, 'reply_count': 80}, {'movie_id': 3, 'reply_count': 77}, {'movie_id': 4, 'reply_count': 81}, 
{'movie_id': 5, 'reply_count': 72}, {'movie_id': 6, 'reply_count': 82}, {'movie_id': 7, 'reply_count': 84}, {'movie_id': 8, 'reply_count': 71}, 
{'movie_id': 9, 'reply_count': 93}, {'movie_id': 10, 'reply_count': 81}
]>
```

<br>

## 마치며

저는 Django 개발자로서 처음 공부할 때 가장 어려운 것이 annotate()를 사용하는 것이었습니다.

특히, annotate()는 생각할 것이 많아 처음 접하시는 분들이나 SQL에 대한 지식이 없으실 경우 더 작성하기 힘드실 것입니다.

이렇게 annotate()에 대해 이해하여 데이터를 보다 정확하게 다룰 수 있는 백엔드 개발자가 모두 됩시다!
