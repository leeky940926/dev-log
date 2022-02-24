# Transaction(2/2)

[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL8-Transaction22)

<br>

## ✔️ ACID

RDBMS는 트랜잭션의 기능을 보장할 수 있도록 ACID라고 하는 4가지 성질을 제공합니다.

<br>

* A : 원자성(Atomicity)
트랜잭션과 관련된 작업들이 부분적으로 실행되다가 중단되지 않는 것을 보장하는 능력입니다.
아마 트랜잭션에서 가장 많이 드는 예시 중 하나인 계좌이체로 예시를 들겠습니다.
계좌이체는 성공할 수도, 실패할 수도 있지만 보내는 쪽에서 돈을 빼오는 작업만 성공하고 받는 쪽에 돈을 넣는 작업을 실패해서는 안됩니다.
원자성은 이와 같이 중간 단계까지 실행되고 실패하는 일이 없도록 하는 것입니다.
즉, 트랜잭션의 작업들이 데이터베이스에 모두 반영되던가 아니면 아예 반영이 안된다는 뜻입니다.

* C : 일관성(Consistency)
트랜잭션이 성공적으로 완료되면 언제나 일관성있는 데이터베이스 상태로 유지하는 것을 의미합니다.
예를 들어, 사용자정보를 담는 테이블에 age(나이)라는 컬럼이 있다고 생각해보겠습니다.
그러면 나이는 항상 숫자인 PostivieInteger형태로 들어가야 합니다.
이 약속이 트랜잭션 과정에도 마찬가지로 적용되어야 한다는 뜻이며, 트랜잭션 과정에서 age라는 컬럼에 문자열을 넣으려고 한다면 트랜잭션은 거부되어야 합니다.

* I : 고립성(Isolation)
트랜잭션을 수행 시, 다른 트랜잭션의 연산 작업이 끼어들지 못 하도록 보장하는 걸 의미합니다.
이것은 트랜잭션 밖에 있는 어떤 연산도 중간 단계의 데이터를 볼 수 없음을 의미합니다.
여러 트랜잭션이 진행될 때, 하나의 트랜잭션이 완료되기 전에는 다른 트랜잭션이 결과값을 읽거나 영향을 끼칠 수 없다는 뜻입니다.

* D : 지속성(Durability)
성공적으로 수행된 트랜잭션은 영원히 반영되어야 한다는 뜻입니다.
혹여나 데이터베이스에 장애가 발생해도 트랜잭션이 성공적으로 수행되었다면 그 결과는 그대로 반영되어 합니다.

<br>

## ✔️ Transaction In Django

Django에서 transaction 설정하는 것에 대한 방법입니다.

첫 번째로는 메소드안에서 특정 구간에서 트랜잭션을 설정할 수 있습니다.

아래 코드를 보면, POST 메소드에서 Body를 통해 보낸 값을 받고 변수 선언을 한 뒤 User라는 모델에 담을 때 트랜잭션을 선언했습니다.

```python
from django.views import View
from django.db import transaction

class SiginInView(View):
    def post(self, request):
        data = self.request.data

        identification = data["identification"]
        password = data["password"]
        
        with transaction.atomic():
            User.objects.create(
                identification=identification,
                password=password
            )
```


그런데 사실 이건 예시를 들기 위해 중간에 선언했지만, 값을 가져오는 순간부터 트랜잭션을 걸어야 조금 더 안전한 상태로 작업이 이루어집니다.

그래서 권장되는 방법이 트랜잭션을 서두에 거는 것입니다.


<br>


이렇게 트랜잭션을 시작부터 설정해놓으면 post 메소드를 시작할 때부터 트랜잭션이 걸리므로, 조금 더 안전하게 데이터 작업을 할 수 있습니다.


```python
from django.views import View
from django.db import transaction

class SiginInView(View):
    @transaction.atomic
    def post(self, request):
        data = self.request.data

        identification = data["identification"]
        password = data["password"]
        
        User.objects.create(
            identification=identification,
            password=password
        )
```
<br>

## ✔️ 마치며..

이상 2일에 걸쳐 트랜잭션에 대해 알아봤습니다.



읽어주셔서 감사합니다.
