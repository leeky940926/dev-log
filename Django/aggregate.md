# aggregate

<br>

## Intro

<br>

개인적으로 ORM 메소드 중 가장 헷갈렸던 annotate와 aggregate입니다.



특히 정규필드 외에도 추가로 집계하여 컬럼을 표기해야되는 경우가 많은데, annotate와 aggregate에 대한 이해가 없다보니 많이 애먹었습니다.



오늘 aggregate에 대해 포스팅 후 내일 annotate에 대해 포스팅해보겠습니다.



모든 출처는 [공식문서](https://docs.djangoproject.com/en/4.0/ref/models/querysets/#aggregate)입니다.

<br>

## aggregate

<br>

```shell
Returns a dictionary of aggregate values (averages, sums, etc.) calculated over the QuerySet
```


공식문서에 나와있는 aggregate의 설명입니다.



QuerySet에 대해 계산된 집계 값(평균, 합계 등)을 딕서너리 형태로 리턴합니다.



테스트를 위해 모델링을 우선 해보겠습니다.

<br>

## Modeling

<br>

```python
class User(models.Model) :
    email = models.EmailField(max_length=50, unique=True)
    
    class Meta:
        db_table='users'
   
class Book(models.Model) :
    name         = models.CharField(max_length=50)
    publish_date = models.DateField()
    author       = models.ManyToManyField(Author,related_name='author', through='querysets.BookAuthor')
    
    class Meta:
        db_table='books'
        
class Rating(models.Model) :
    book   = models.ForeignKey(Book, on_delete=models.CASCADE)
    user   = models.ForeignKey(User, on_delete=models.CASCADE)
    rate   = models.DecimalField(max_digits=3, decimal_places=1)
    
    class Meta:
        db_table='ratings'
```


책에 대해 별점을 주기 위해 User, Book, Rating 모델을 만들었습니다.



Rating의 별점(rate)을 aggregate를 통해 집계해보도록 하겠습니다.



데이터는 faker 라이브러리를 이용해 임의로 넣었습니다.

<br>


## Test


![image](https://user-images.githubusercontent.com/88086271/154487757-4cfb01bb-d242-4217-be19-b6f86e351c2d.png)



1. 우선, DB에 저장된 별점개수는 150개입니다.



2. 그리고 유저ID중 가장 빠른 ID는 71번입니다.



3. aggregate를 이용해 71번유저가 준 별점의 합계를 구해보겠습니다.
그러면 Key값이 fieldname__aggregateclass 의 이름으로 된 값이 나옵니다.



4. 이 Key값을 커스터마이징 할 수 있습니다. <br> aggregate안에 내가 사용하고자 하는 변수의 이름을 써서 aggregate(myCustomizedName = Sum()) 이렇게쓰면 커스터마이징된 이름이 Key인 값이 나오게 됩니다.



5. 반대로 유저에서 접근도 할 수 있습니다. <br> 지금까지는 Rating이라는 모델에서 직접 접근했지만, User에서 접근하려면 테이블명__rate 이렇게 사용해주면 됩니다.

<br>

## 마치며..

<Br>
  
이렇게 aggregate에 대해 알아봤습니다.



내일 Intro부분에 쓴 것 처럼 annotate를 포스팅하겠습니다.

