# TDD
[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL15-TDD)

<br>

## ✔️Intro

오늘은 TDD(Test-Driven-Development, 테스트 주도 개발)에 대해 포스팅해보겠습니다. 저희 회사에서도 서버 개발팀 방향성에 포함된 것 중 하나가 테스트 코드를 통한 테스트 방법 개선이고, 저 역시 프로젝트를 해보며 Unit Test를 하다 보니 테스트 코드를 작성해놓는 것에 대한 중요성을 알고 있었습니다. 그래서 TDD에 대한 간단한 설명과 Django의 [공식문서](https://docs.djangoproject.com/ko/4.0/topics/testing/overview/)를 보고 Unit Test를 진행해보겠습니다. 

<br>

## ✔️Testing Pyramid

우선, 테스트의 경우 3가지로 분리됩니다. E2E Test(UI Test), Integration Test, Unit Test인데 아래 그림을 보고 각 테스트에 대해 설명을 하겠습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F1orqi%2Fbtru8VXem3w%2FNniUPv1E8uFMqjJSs038yK%2Fimg.png)

이 그림은 [Google Test Automation Conference](https://martinfowler.com/bliki/TestPyramid.html)에서 제안된 테스트 피라미드이며, 비중은 위에서 부터 10, 20, 70%로 수치를 두는 걸 권장합니다.

우선, E2E Test입니다. UI Test라고도 하며, 웹 브라우저를 띄운 다음에 내가 만든 페이지로 들어가서 화면상에 이상없이 잘 나오는지, 로직은 잘 돌아가는지 직접 테스트를 해보는 방식을 뜻합니다. 테스트에 들이는 Cost가 많이 들고 시간이 오래 걸리는 단점이 있습니다.

두 번째로, Integration Test입니다. 쉽게 말해 Postman, httpie, Thunder Client 등으로 잘 돌아가는지 확인하는 걸 의미합니다.

마지막이 제가 이제 이야기할 Unit Test입니다. 가장 쉬우며 효과적으로 빠르게 테스트를 할 수 있는 방법입니다.

<br>

## ✔️TDD란?

짧은 개발 주기의 반복에 의존하는 개발 프로세스로서, [Extreme Programming(XP)](https://ko.wikipedia.org/wiki/익스트림_프로그래밍)의 "Test-First" 개념에 기반을 둔 단순한 설계를 중요시합니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcBmXKD%2FbtrvdRyEFfa%2FXkfeuWyxjIRRIoCCXkChPK%2Fimg.png)

TDD를 쉽게 표현하면 위 그림과 같습니다.

* Red 단계에서는 실패하는 테스트 코드를 우선 작성합니다.
* Green 단계에서는 테스트 코드를 성공시키기 위한 실제 코드를 작성합니다.
* Blue 단계에서는 중복 코드 제거, 일반화 등의 리팩토링을 수행합니다.

중요한 것은 실패하는 테스트 코드를 작성할때까지 실제 코드를 작성하지 않는 것과, 실패하는 테스트를 통과할 정도의 최소 실제 코드를 작성해야 하는 것입니다. 이를 통해, 실제 코드에 대해 기대되는 바를 보다 명확하게 정의함으로써 불필요한 설계를 피할 수 있고, 정확한 요구 사항에 집중할 수 있습니다.

<br>

## ✔️TDD의 장점

단위 테스트를 통한 장점은 동작하는 코드에 대한 자신감을 가질 수 있고, 코드에 대한 지식이 증가하는 점 등입니다. 테스트 코드를 작성함으로써 실제 코드에 대한 이해도를 높일 수 있을 뿐만 아니라, 실제 코드에 좋지 않은 설계 구조를 손쉽게 파악할 수 있습니다. 

실제로 테스트 코드를 이용하면 디버깅 시간이 줄어들뿐만 아니라 잘못된 코드에 대해 빠른 오류 확인이 가능합니다. 물론, 당장은 테스트 코드 작성에 많은 노력이 드는 건 사실입니다. 현업에서의 테이블 구조는 엄청 복잡하기 때문입니다. 다만, 전체 개발 주기를 생각했을 때 이러한 노력이 담긴 테스트를 통해서 결국 생산성이 향상됩니다. 

테스트 코드를 작성함으로써 가장 좋은 건 각 API별 설계를 명확하게 함으로써 과도한 설계를 피할 수 있다는 점입니다. 그리고 이 테스트를 이용해 API문서화를 할 수 있게 됩니다. 아래는 제가 프로젝트를 할 때 Postman을 이용해 API Documentation을 한건데, 실패사례를 이렇게 문서화하는데 기반은 테스트코드였습니다. 이렇게 API별로 문서화를 해놓는다면 소통에 더 편안하다는 장점이 있습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fdxmvu3%2FbtrvhNCV8et%2FbzU1pt2lFBySkFhaJMW5kk%2Fimg.png)

이렇게 TDD에 대해 알아보았으니, Django에서 테스트를 해보겠습니다.

<br>

## ✔️Unit Test In Django

우선 테스트를 하기 위해 모델링과 View로직을 먼저 작성하겠습니다.
```python
from django.db import models


class Role(models.Model):
    name = models.CharField(max_length=20)


class User(models.Model):
    role = models.ForeignKey('tdds.Role', on_delete=models.CASCADE)
    nickname = models.CharField(max_length=20, unique=True)
    password = models.CharField(max_length=500)
```
<br>

우선, 유저라는 모델을 만드는데 유저별로 역할을 부여하기 위해 Role, User를 만들었습니다. 그리고 Role을 User가 참조합니다.
그리고 nickname은 unique=True를 설정했습니다.

```python
class SignUpView(View):
    def post(self, request, *args, **kwargs):
        try:
            with transaction.atomic():
                data = json.loads(self.request.body)
                
                role_id = data["role_id"]
                nickname = data["nickname"]
                password = data["password"]
                password = bcrypt.hashpw(
                    password=password.encode('utf-8'), 
                    salt=bcrypt.gensalt())                
                
                if not Role.objects.filter(id=role_id).exists():
                    return JsonResponse({'message':'ROLE_DOES_NOT_EXIST'}, status=500)
                
                if User.objects.filter(nickname=nickname).exists():
                    return JsonResponse({'meesage':'NICKNAME_ALREADY_EXIST'}, status=500)
                
                User.objects.create(
                    role_id=role_id,
                    nickname=nickname,
                    password=password
                )
        
        except KeyError:
            return JsonResponse({'meesage':'KEY_ERROR'}, status=500)
        
        else:
            return JsonResponse({'message':'CREATE_USER'}, status=201)
    

class UserListView(View):
    def get(self, request, *args, **kwargs):
        users = User.objects.select_related('role').all()
        user_list = [
            {"id":user.id,
             "role":user.role.name,
             "nickname":user.nickname}
            for user in users]
        return JsonResponse({'user_list':user_list}, status=200)
```
<br>

View의 경우, 회원가입을 위한 SignUpView와 유저 리스트를 보기 위한 UserListView를 작성했습니다.

회원가입의 경우,  body로 role_id와 nickname, password를 받으며, 비밀번호 암호화의 경우 Bcrypt를 이용했습니다. PBKDF2의 경우 Django에서 제공하는 User를 사용해야 하는데, 저는 커스터마이즈해서 모델을 만들었기 때문입니다. 그래서 role_id를 받았는데 해당 ID가 DB에 없을 때 RoleDoesNotExist 에러가 발생하는 걸 핸들링했고, unique=True인 nickname이 DB에 있는 경우 Integrity Error가 발생하는 걸 핸들링하기 위해 Already Exist 에러를 설정했습니다. 그리고 Body에 role_id, nickname, password 하나라도 없을 시 KeyError가 발생합니다.

유저리스트의 경우, User가 Role을 정참조하기 때문에 select_related를 사용했고 클라이언트에 id, role의 name, nickname을 담은 유저정보를 리턴하도록 했습니다.

그리고 이 두 개의 API를 테스트 하기 위한 코드입니다.

```python
import bcrypt
from django.test.testcases import TestCase
from django.test.client import Client
from tdds.models import Role, User


class TestUser(TestCase):
    def setUp(self):
        self.client = Client()
        
        role1 = Role.objects.create(id=1, name="admin1")
        role2 = Role.objects.create(id=2, name="normal1")
        
        User.objects.create(
            role=role1, 
            nickname='kylee1', 
            password=bcrypt.hashpw("1234".encode('utf-8'), bcrypt.gensalt())
        )
        User.objects.create(
            role=role2, 
            nickname='kylee2', 
            password=bcrypt.hashpw("1234".encode('utf-8'), bcrypt.gensalt())
        )
    
    
    def tearDown(self):
        Role.objects.all().delete()
        User.objects.all().delete()
        
    
    def test_success_create_user(self):
        
        data = {
            "role_id":1,
            "nickname" : "kylee3",
            "password" : "1234"
        }
        
        response = self.client.post(
            "/tdds/signup", 
            data=data, 
            content_type='application/json')
        
        self.assertEqual(response.status_code, 201)
        self.assertEqual(response.json(), {'message':'CREATE_ROLE'})
    
    
    def test_failure_create_user_raise_role_does_not_exist(self):
        
        data = {
            "role_id":100,
            "nickname" : "kylee3",
            "password" : "1234"
        }
        
        response = self.client.post(
            "/tdds/signup", 
            data=data, 
            content_type='application/json')
        
        assert response.status_code == 500
        assert response.json() == {'message':'ROLE_DOES_NOT_EXIST'}
    
    
    def test_failure_create_user_raise_nickname_already_exist(self):
        
        data = {
            "role_id":1,
            "nickname" : "kylee1",
            "password" : "1234"
        }
        
        response = self.client.post(
            "/tdds/signup", 
            data=data, 
            content_type='application/json')
        
        assert response.status_code == 500
        assert response.json() == {'meesage':'NICKNAME_ALREADY_EXIST'}

    
    def test_success_get_user_list(self):
        
        response = self.client.get("/tdds/users")
        
        self.assertEqual(response.status_code , 200)
        self.assertEqual(response.json() , 
            {"user_list":[
                {
                    "id":8,
                    "role":"admin1",
                    "nickname":"kylee1"
                },
                {
                    "id":9,
                    "role":"normal1",
                    "nickname":"kylee2"
                }
        ]})
```
테스트를 하기 위한 TestUser라는 이름의 클래스를 만들고 TestCase를 상속받았습니다. TestCase와 TransactionTestCase 두 개를 상속받을 수 있는데, TransactionTestCase는 select_for_update같이 락을 걸어야 할 사항이 있을 때 사용한다고 합니다. 

setUp에선 초기값을 세팅하고, tearDown은 만든 초기값을 삭제합니다. MySQL을 사용할 땐 테스트 데이터마다 ID를 만들었는데 PostgreSQL은 테스트 메소드마다 ID를 자동으로 생성해줍니다. 그래서 초기값 세팅 때 유저ID가 1,2로 세팅되고 test_success_create_user 테스트 때 setUp이 실행돼서 ID가 3번으로 만들어집니다. 그리고  test_failure_create_user_raise_role_does_not_exist 테스트 때 setUp이 실행돼서 ID가 4,5번으로 세팅되었다가 삭제되고, test_failure_create_user_raise_nickname_already_exist 때 한 번 더 실행돼서 6,7번으로 세팅되었다가 삭제돼서 get으로 불러올 때 8,9번으로 세팅돼서 저장이 됩니다. Unit Test에서 PostgreSQL의 ID체계가 많이 복잡합니다. 그리고 데이터가 tearDown으로 삭제되며 test_success_create_user에서 만든 게 함께 삭제되는 것입니다.

그래서 client가 http method와 함께 보낸 응답의 결과가 reponse에 저장되는데, 비교 방법이 두 가지 있습니다.
assert를 쓰거나 self.assertEqual을 사용하는 것입니다. 저는 개인적으로 self.assertEqual이 틀린 부분을 집어줘서 후자를 애용합니다. 첫 번째 사진이 assert를 이용했을 때고 self.assertEqual이 두 번째 사진입니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FvDwfx%2FbtrvcGkGzxw%2F3Xbvkpx3FB4SMnuP2AJzFK%2Fimg.png)

<br>
<br>

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FMVQ7d%2Fbtru9pJIL8r%2FsS31zqah9i6F3ICftjQn30%2Fimg.png)


<br>

## ✔️마치며..

Unit Test가 DB마다 방법이 약간씩 다르다보니 헷갈리는 부분도 많으니, 꼭 공식문서 잘 참고하셔서 해야 합니다. 그리고 생각보다 테스트코드가 작성하는 게 까다로우니 계속 연습해보면서 숙지하시길 권장드립니다.
