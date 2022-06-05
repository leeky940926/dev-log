# DRF APITestCase

[티스토리 바로가기](https://kyleeee.tistory.com/entry/TIL25-APITestCase)


Django에서 기본적으로 TestCase를 이용해 테스트를 할 수 있도록 제공하는 [Unit Test](https://docs.djangoproject.com/en/4.0/topics/testing/overview/)가 있지만, DRF에서는 그에 상응하는(?) 기능으로 APITestCase가 있습니다.

사용해보니, Django에서 제공하는 기본적인 단위테스트와는 동일합니다.

<br>

## View

우선, 테스트를 하기 위해 View로직을 알아야 합니다.

API는 영화리스트 조회(MovieListView), 특정 영화조회(MovieDetailView) 두 개가 있습니다.

여기선 MovieListView의 테스트 경우만 보겠습니다.

<br>

## MovieListView Test

<img width="534" alt="image" src="https://user-images.githubusercontent.com/88086271/171335274-bd5f5e45-eb39-4681-806c-d7bc91e4037e.png">

영화 리스트의 경우, 위의 이미지같은 형태로 나오게 됩니다.

그러면 테스트 코드 역시, 값을 비교할 때 위와 같은 형태가 나와야 한다는 의미입니다.

테스트 코드를 보겠습니다.

```python
from rest_framework.test import APITestCase, APIClient
from movies.models import Movie, Reply

class TestMovieListView(APITestCase):
    def setUp(self) :
        movie1 = Movie.objects.create(
            title = "test1",
            year = 2022,
            rating = 3.5,
            runtime = 95
        )
        movie2 = Movie.objects.create(
            title = "test2",
            year = 2022,
            rating = 6.5,
            runtime = 100
        )
        movie3 = Movie.objects.create(
            title = "test3",
            year = 2022,
            rating = 2.4,
            runtime = 120
        )
        movie4 = Movie.objects.create(
            title = "test4",
            year = 2022,
            rating = 9.4,
            runtime = 80
        )
        movie5 = Movie.objects.create(
            title = "test5",
            year = 2022,
            rating = 7.5,
            runtime = 75
        )
        movie6 = Movie.objects.create(
            title = "test6",
            year = 2022,
            rating = 6.6,
            runtime = 95
        )
        reply1 = Reply.objects.create(
            movie = movie1,
            title = "reply1",
            content = "재미있어요",
            rating = 5
        )
        reply2 = Reply.objects.create(
            movie = movie2,
            title = "reply2",
            content = "흐이로워요",
            rating = 5
        )
        reply3 = Reply.objects.create(
            movie = movie3,
            title = "reply3",
            content = "그저그래여",
            rating = 5
        ) 
        
    def tearDown(self) :
        Reply.objects.all().delete()
        Movie.objects.all().delete()
        
    client = APIClient()
    
    def test_movie_list_view(self):
        url = "/api/movies/"
        response = self.client.get(path=url)
        self.assertEquals(response.status_code, 200)
        self.assertEquals(response.json(),
            [
                {
                    "movie_id" : 1,
                    "movie_title" : "test1",
                    "movie_rating" : 3.5,
                    "movie_runtime" : 95,
                    "movie_replies" : [
                        {
                            "reply_id" : 1,
                            "reply_title" : "reply1",
                            "reply_content" : "재미있어요",
                            "reply_rating" : 5
                        }
                    ]
                },
                {
                    "movie_id" : 2,
                    "movie_title" : "test2",
                    "movie_rating" : 6.5,
                    "movie_runtime" : 100,
                    "movie_replies" : [
                        {
                            "reply_id" : 2,
                            "reply_title" : "reply2",
                            "reply_content" : "흐이로워요",
                            "reply_rating" : 5
                        }
                    ]
                },
                {
                    "movie_id" : 3,
                    "movie_title" : "test3",
                    "movie_rating" : 2.4,
                    "movie_runtime" : 120,
                    "movie_replies" : [
                        {
                            "reply_id" : 3,
                            "reply_title" : "reply3",
                            "reply_content" : "그저그래여",
                            "reply_rating" : 5
                        }
                    ]
                },
                {
                    "movie_id" : 4,
                    "movie_title" : "test4",
                    "movie_rating" : 9.4,
                    "movie_runtime" : 80,
                    "movie_replies" : []
                },
                {
                    "movie_id" : 5,
                    "movie_title" : "test5",
                    "movie_rating" : 7.5,
                    "movie_runtime" : 75,
                    "movie_replies" : []
                }
            ]
        )
```

우선 setUp과 tearDown을 통해 테스트 데이터를 세팅/삭제를 해주는 함수를 만들고 실질적으로 테스트 할 함수를 만들어줍니다.
마지막에  ```response.json()```과 실제로 제가 적은 값이 일치하는지 비교를 하는데, 만든 테스트 데이터의 경우 한 글자도 틀린다면 테스트가 실패했다고 나옵니다.

해당 내용이 담긴 레포는 [이 곳](https://github.com/leeky940926/dockers)에 있습니다.
