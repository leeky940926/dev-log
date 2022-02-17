# prefetch_related에서 to_attr로 DB Hits 줄이기

<br>

## Intro



Django로 개발하는 사람이라면 N+1 Problem을 줄이기 위해 여러 노력들을 해봤을 것이다.



나 역시 회사에서 코드 리팩토링을 통해 모델도 파악하고, 몰랐던 쿼리셋 메소드들도 알아가고 있다.



오늘 포스팅 할 주제는 prefetch_related에서 to_attr  이다.



기존에 N+1 Problem을 해결하려고 prefetch_related를 사용할 땐 아래와 같은 두 가지 방법으로 사용했다.



1. prefetch_related("model_set") 

2. Prefetch("model_set", queryset=) 



prefetch_related에서 조금 더 조건을 줄 때 Prefetch를 Import해서 사용했는데, 여기에 있는 to_attr을 사용하면



쿼리수가 획기적으로 줄어들 수 있다.



나는 공식문서의 Pizza, Topping이 아닌 주소의 시-구-동을 이용해서 이걸 해볼 것이다.

<br>

모든 출처는 [공식문서](https://docs.djangoproject.com/en/4.0/ref/models/querysets/#prefetch-related)이다.

<br>


## models.py
```python
class City(models.Model):
    name = models.CharField(max_length=30)

class Goo(models.Model):
    name = models.CharField(max_length=30)
    city = models.ForeignKey(City, on_delete=models.CASCADE)

class Dong(models.Model):
    name = models.CharField(max_length=30)
    goo = models.ForeignKey(Goo, on_delete=models.CASCADE)
```
이렇게 모델 생성을 하고 마이그레이션을 진행했다.


<br>


## 데이터 세팅
```python
city = City.objects.create(name="서울시")
goo = Goo.objects.create(name="영등포구", city=city)
Goo.objects.create(name="구로구", city=city)
Goo.objects.create(name="서초구", city=city)
Goo.objects.create(name="강남구", city=city)
Goo.objects.create(name="마포구", city=city)
Dong.objects.create(goo=goo, name="당산1동")
Dong.objects.create(goo=goo, name="당산2동")
Dong.objects.create(goo=goo, name="당산3동")
Dong.objects.create(goo=goo, name="당산4동")
Dong.objects.create(goo=goo, name="당산5동")
Dong.objects.create(goo=goo, name="당산6동")
Dong.objects.create(goo=goo, name="당산7동")
Dong.objects.create(goo=goo, name="당산8동")
Dong.objects.create(goo=goo, name="당산9동")
Dong.objects.create(goo=goo, name="당산10동")
```

데이터는 간단하게 이렇게 세팅을 했으며,



반복문을 돌려 서울시 영등포구에 있는 10개의 동을 한 개씩 추출할 것이다.


<br>


## 기존에 내가 작성했던 코드 
```python
class BeforePrefetchView(View):
    def get(self, request, city_id):
        citys = City.objects.prefetch_related("goo_set__dong_set").get(id=city_id)
        
        dong_list = []
        goo_list = []
        
        for goo in citys.goo_set.all():
            for dong in goo.dong_set.all():
                dong_list.append(dong.id)
                goo_list.append(goo)

        for goo in citys.goo_set.all():
            for dong in goo.dong_set.all():
                dong_list.append(dong.id)
                goo_list.append(goo)
        
        return HttpResponse(dong_list, goo_list)
```

만약 이렇게 반복문을 두 번 쓰게 된다면 어떻게 될까?



그렇게 되면 prefetch_related를 이용해 DB Hits를 한 번 했음에도 반복문이 돌 때 마다 계속 발생한다


```shell
SELECT "querysets_city"."id", "querysets_city"."name" FROM "querysets_city" WHERE "querysets_city"."id" = 1 LIMIT 21; args=(1,); alias=default
2022-02-14 22:08:12,379 DEBUG (0.000) SELECT "querysets_goo"."id", "querysets_goo"."name", "querysets_goo"."city_id" FROM "querysets_goo" WHERE "querysets_goo"."city_id" IN (1); args=(1,); alias=default
2022-02-14 22:08:12,380 DEBUG (0.001) SELECT "querysets_dong"."id", "querysets_dong"."name", "querysets_dong"."goo_id" FROM "querysets_dong" WHERE "querysets_dong"."goo_id" IN (1, 2, 3, 4, 5); args=(1, 2, 3, 4, 5); alias=default
2022-02-14 22:08:12,382 DEBUG (0.000) SELECT "querysets_goo"."id", "querysets_goo"."name", "querysets_goo"."city_id" FROM "querysets_goo" WHERE "querysets_goo"."city_id" = 1; args=(1,); alias=default
2022-02-14 22:08:12,382 DEBUG (0.000) SELECT "querysets_dong"."id", "querysets_dong"."name", "querysets_dong"."goo_id" FROM "querysets_dong" WHERE "querysets_dong"."goo_id" = 1; args=(1,); alias=default
2022-02-14 22:08:12,383 DEBUG (0.000) SELECT "querysets_dong"."id", "querysets_dong"."name", "querysets_dong"."goo_id" FROM "querysets_dong" WHERE "querysets_dong"."goo_id" = 2; args=(2,); alias=default
2022-02-14 22:08:12,383 DEBUG (0.000) SELECT "querysets_dong"."id", "querysets_dong"."name", "querysets_dong"."goo_id" FROM "querysets_dong" WHERE "querysets_dong"."goo_id" = 3; args=(3,); alias=default
2022-02-14 22:08:12,384 DEBUG (0.000) SELECT "querysets_dong"."id", "querysets_dong"."name", "querysets_dong"."goo_id" FROM "querysets_dong" WHERE "querysets_dong"."goo_id" = 4; args=(4,); alias=default
2022-02-14 22:08:12,384 DEBUG (0.000) SELECT "querysets_dong"."id", "querysets_dong"."name", "querysets_dong"."goo_id" FROM "querysets_dong" WHERE "querysets_dong"."goo_id" = 5; args=(5,); alias=default
2022-02-14 22:08:12,385 DEBUG (0.000) SELECT "querysets_goo"."id", "querysets_goo"."name", "querysets_goo"."city_id" FROM "querysets_goo" WHERE "querysets_goo"."city_id" = 1; args=(1,); alias=default
2022-02-14 22:08:12,385 DEBUG (0.000) SELECT "querysets_dong"."id", "querysets_dong"."name", "querysets_dong"."goo_id" FROM "querysets_dong" WHERE "querysets_dong"."goo_id" = 1; args=(1,); alias=default
2022-02-14 22:08:12,386 DEBUG (0.000) SELECT "querysets_dong"."id", "querysets_dong"."name", "querysets_dong"."goo_id" FROM "querysets_dong" WHERE "querysets_dong"."goo_id" = 2; args=(2,); alias=default
2022-02-14 22:08:12,386 DEBUG (0.000) SELECT "querysets_dong"."id", "querysets_dong"."name", "querysets_dong"."goo_id" FROM "querysets_dong" WHERE "querysets_dong"."goo_id" = 3; args=(3,); alias=default
2022-02-14 22:08:12,387 DEBUG (0.000) SELECT "querysets_dong"."id", "querysets_dong"."name", "querysets_dong"."goo_id" FROM "querysets_dong" WHERE "querysets_dong"."goo_id" = 4; args=(4,); alias=default
2022-02-14 22:08:12,387 DEBUG (0.000) SELECT "querysets_dong"."id", "querysets_dong"."name", "querysets_dong"."goo_id" FROM "querysets_dong" WHERE "querysets_dong"."goo_id" = 5; args=(5,); alias=default
```

이건 로그를 찍어 가져온 DB Hits 횟수



그러면 to_attr을 사용한다면 어떨까?


<br>


## to_attr을 이용한 개선된 코드
```python
class BeforePrefetchView(View):
    def get(self, request, city_id):
        citys = City.objects.prefetch_related(
           Prefetch("goo_set",
                    queryset=Goo.objects.all().prefetch_related(
                        Prefetch(
                            "dong_set",
                            queryset=Dong.objects.all(),
                            to_attr="prefetch_dong_set"
                        )
                    ),
                    to_attr="prefetch_goo_set")
       ).get(id=city_id)
        
        dong_list = []
        goo_list = []
        
        for goo in citys.prefetch_goo_set:
            for dong in goo.prefetch_dong_set:
                dong_list.append(dong.id)
                goo_list.append(goo)
        
                
        for goo in citys.prefetch_goo_set:
            for dong in goo.prefetch_dong_set:
                dong_list.append(dong.id)
                goo_list.append(goo)

        
        return HttpResponse(dong_list, goo_list)
```

Prefetch를 이용하여 queryset 특성에 Dong에 대해 접근할 수 있게 한 뒤,



Goo에는 prefetch_goo_set 이라는 이름의 to_attr을, Dong에는 prefetch_dong_set 이라는 이름의 to_attr을 정의했다.



이렇게 하고 똑같이 반복문을 두 번 돌리면 DB Hits가 3번밖에 되지 않는다.

```shell
 SELECT "querysets_city"."id", "querysets_city"."name" FROM "querysets_city" WHERE "querysets_city"."id" = 1 LIMIT 21; args=(1,); alias=default
2022-02-14 22:12:38,072 DEBUG (0.009) SELECT "querysets_goo"."id", "querysets_goo"."name", "querysets_goo"."city_id" FROM "querysets_goo" WHERE "querysets_goo"."city_id" IN (1); args=(1,); alias=default
2022-02-14 22:12:38,074 DEBUG (0.002) SELECT "querysets_dong"."id", "querysets_dong"."name", "querysets_dong"."goo_id" FROM "querysets_dong" WHERE "querysets_dong"."goo_id" IN (1, 2, 3, 4, 5); args=(1, 2, 3, 4, 5); alias=default
```

이 세 번은 처음에 citys를 정의할 때 가져온 건데 왜 발생하지 않냐면 바로 to_attr 덕분이다.



공식문서에 나와 있는 글을 인용하자면


```shell
You can also assign the prefetched result to a custom attribute with the optional to_attr argument. The result will be stored directly in a list.
```
to_attr을 이용하여 prefetch_related의 결과를 저장할 수 있다는 뜻이다.


이미 저장을 해놔서 굳이 DB Hits를 하지 않고 가져올 수 있게 된 것이다.



회사에서 to_attr을 이용해 쿼리수를 최적화하고 있으며, 오늘 작업한 API 중 하나는 쿼리가 35개에서 3개로 줄었다!



Django를 어느정도 해본 사람은 알겠지만 처음에 get()으로 가져와서 바로 접근가능한 것이지, filter()를 이용해 가져왔다면



반복문을 통해 접근하는 걸 잊지말자!

