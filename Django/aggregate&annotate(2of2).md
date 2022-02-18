# aggregate&annotate(2/2)

<br>

## Intro


aggregate에 이은 annotate 입니다.



둘 다 집계를 하는 건 똑같은데, 지난 포스팅의 내용을 다시 복기시켜보자면 aggregate에서는 집계한 값이 딕셔너리 형태로 출력됩니다.



annotate에 대해 간단하게 먼저 말하면, SQL의 Group By 기능입니다.



정의에 대해 알아본 후, 직접 테스트를 하겠습니다.



모든 출처는 [공식문서](https://docs.djangoproject.com/en/4.0/ref/models/querysets/#annotate)입니다.

<br>

## 정의


annotate의 뜻은 주석입니다.



우선, 공식문서에 나온 annotate의 정의를 보겠습니다.


```shell
Annotates each object in the QuerySet with the provided list of query expressions.
An expression may be a simple value, a reference to a field on the model (or any related models), 
or an aggregate expression (averages, sums, etc.)
that has been computed over the objects that are related to the objects in the QuerySet.
Each argument to annotate() is an annotation that will be added to each object in the QuerySet that is returned.
```



간단하게 해석해보자면



"제공된 쿼리 표현식 목록으로 QuerySet의 각 개체에 주석을 답니다.

표현식은 단순한 값이 될 수도 있고, 참조된 모델(혹은 연결된 모델)이 될 수도 있고 관련된 객체에 대해 계산된 aggregate 표현식이 될 수도 있습니다.

annotate의 인자는 쿼리셋에 포함되어 나옵니다."



이정도의 이해를 하고  shell_plus를 켜겠습니다.

<br>

## Modeling
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


모델링은 aggregate에서 쓴 걸 그대로 사용하겠습니다.



간단히 요약하자면, 유저별로 책에 별점을 줄 수 있게 만든 테이블들입니다.



유저의 ID는 71번으로 하고 annotate를 해보겠습니다.



제가 궁금한 건



1. 71번 유저가 몇 개의 책에 별점을 줬는지

2. 각 책별로 평점이 몇 점인지

3. DB에 있는 평점의 평균은 몇 점인지



입니다.

<br>

## 71번 유저가 몇 개의 책에 별점을 줬는가?

![image](https://user-images.githubusercontent.com/88086271/154683256-89411a5a-197c-4e42-821d-4d4710fa1cd1.png)


71번 유저가 총 11개의 책에 평점을 부여했습니다.

<br>



## 각 책별로 평점은 몇 점일까?


를 하기엔 데이터를 너무 많이 넣어서 비교가 쉬울 것 같은 데이터 하나를 가져왔습니다.



annotate를 이용해서 데이터를 뽑아보겠습니다.




![image](https://user-images.githubusercontent.com/88086271/154683305-d0a433e1-4554-4c61-b19f-d2d3d1d6f58a.png)




책의 ID가 602번인 책의 평균을 리턴된 QuerySet에 넣어서 함께 받기 위해 annotate를 사용했습니다.

DecimalField는 대충 만들다보니 2.888888 이라는 해괴한 값이 나왔습니다.

다음부터는 모델링에 조금 더 신중해야겠습니다.



annotate를 쓸 때, Group By가 최초모델의 ID로 묶이게 되어 시작 모델을 잘 파악해야 합니다.



<br>

## DB에 저장된 평점의 총 평균은 몇 점일까요?


![image](https://user-images.githubusercontent.com/88086271/154683340-5af816c6-ed5c-422a-a0b8-9778a5db183a.png)



annotate와 aggregate의 차이를 이해하신 분이라면 이거는 aggregate를 이용해야 하는 걸 눈치채셨을 겁니다.



<br>

## annotate 여러개 가능할까?


SQL을 호출할 때, 합계가 여러 행에 대해 필요할 때나 합계/평균/개수등이 필요할 때가 있습니다.



그래서 이번에 annotate만을 이용해서



1. 71번 유저가 여태까지 준 별점의 평균

2. 71번 유저가 여태까지 준 별점의 개수

3. 71번 유저가 여태까지 준 별점의 최대값

4. 71번 유저가 여태까지 준 별점의 최소값



을 가져오려면 어떻게 해야 할까요?


![image](https://user-images.githubusercontent.com/88086271/154683406-c8bf6a82-f529-437c-aa1f-701335f20608.png)



Django의 Group By 특성상, User로 부터 Rating으로 접근해야 합니다.



물론 annotate안에 다 선언 안하고 annotate를 이어서 써도 됩니다.



이거는 Django ORM특성에 대한 내용이 담겨있는데, 이부분은 추후에 포스팅해보도록 하겠습니다.



![image](https://user-images.githubusercontent.com/88086271/154683442-fc7ccd5f-10d6-4e8f-add5-1bfc30bedf55.png)



마지막으로 annotate를 할 때, 첫 번째 케이스처럼 annotate 하나 안에서 다 선언할 경우 변수명을 설정해줄꺼면 다 설정해주던가 아니면 다 안해주던가 해야합니다. 안 해줄 경우 이렇게 에러가 발생하며, 다 해줄경우 내가 원하는 변수명으로 컬럼이 추가되어 나옵니다.


![image](https://user-images.githubusercontent.com/88086271/154683471-6534a41e-5aa2-4cc2-8ca6-f75a4c96f47e.png)


![image](https://user-images.githubusercontent.com/88086271/154683483-16e32467-0c82-4f68-9b91-9ece31535b53.png)




물론 두 번째 방법처럼 annotate를 연달아 붙이는 건 상관없습니다.

각각이 독립적이기 때문입니다.

<br>

# 마치며..


이렇게 aggregate와 annotate에 대해 알아보았습니다.



데이터를 다루는 백엔드개발자라면 집계할 일이 많기 때문에 꼭 알아야 할 ORM 메소드입니다.



저같이 헷갈리는 일 없이 잘 쓰셨으면 좋겠습니다.

