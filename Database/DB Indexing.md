# DB Indexing

[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL14-DB-Indexing)

<br>


## ✔️What is Indexing?

[위키백과](https://ko.wikipedia.org/wiki/인덱스_(데이터베이스))에 따르면, 인덱싱이란 테이블에 대한 동작 속도를 높여주는 자료 구조를 일컫는 말입니다. 인덱스는 테이블 내의 1개의 컬럼 혹은 여러 개의 컬럼을 이용하여 생성될 수 있습니다. 고속 검색뿐만 아니라 레코드 접근과 관련 효율적인 순서 매김 동작에 대한 기초를 제공합니다. 인덱스를 저장하는 데 필요한 디스크 공간은 보통 테이블을 저장하는 데 필요한 디스크 공간보다 작습니다. 왜냐하면 보통 인덱스는 키-필드만 갖고 잇고, 테이블의 다른 세부 항목들은 갖고 있지 않기 때문입니다.
그러면 인덱스가 동작속도를 높여준다는 것은 개념적으로 알았는데, Django에서는 어떻게 동작할까요? 직접 입력해보면서 알아보겠습니다. 출처는 [공식문서](https://docs.djangoproject.com/en/4.0/ref/models/indexes/)입니다.

<br>

## ✔️Indexing in Django

Django에서 Index 클래스는 Database Index를 쉽게 생성하게 해줍니다. models.py에서 각 모델별로 특징을 설정하는 Class Meta에서 indexes라는 옵션을 통해 사용되며, django.db.models.indexes에 정의되어 있습니다.  인덱스 클래스에 대해 설명 후 적용할 예정이며, 천천히 이 내용에 대해 기술해보겠습니다.

<br>

## ✔️Index Options

Index클래스에 대핸 설명은 이렇게 되어 있으며, 각 파라미터별로 알아보겠습니다.
```python
Class Index(*expressions, fields=(), name=None, db_tablespace=None, opclasses=(), condition=None, include=None)
```
<br>

## ✔️expressions

expressions는 Django 3.2 버전부터 새로 생겼습니다. expressions는 표현식 및 데이터베이스 기능에 대한 기능적 인덱스를 생성할 수 있습니다. 예를 들어 아래의 코드를 보면
```python
Index(Lower('title').desc(), 'pub_date', name='lower_title_date_idx)
```
인덱스를 생성하는데, title 컬럼의 소문자값의 내림차순으로 만들겠다는 의미이고, 그 다음으로 pub_date의 일반 정렬(오름차순)으로 만들겠다는 의미입니다. 그리고 이렇게 만들어진 인덱스의 이름이 lower_title_date_idx입니다.

다른 예를 들면,
```python
Index(F('height')*F('weight'), Round('weight'), name='calc_idx')
```
데이터베이스에 직접 접근하여 값을 가져오는 F객체의 특성을 이용하여 height와 weight의 곱을 인덱스로 만들고 weight는 반올림하겠다는 의미입니다. 
그리고 name은 expressions를 이용할 때만 요구되어지는 파라미터입니다. 또한 Oracle, MySQL, MariaDB, PostgreSQL별로 제약사항이 있습니다. 저는 현재 PostgreSQL을 사용하기 때문에 PostgreSQL에 대해서만 제약사항을 작성해보자면 PostgreSQL은 인덱스에서 참조되는 함수와 연산자가 Immutable해야 합니다. Django에서는 검증하지 않지만 PostgreSQL에서 잡아낸다고 하며 Concat()과 같은 함수를 허용하지 않는다고 합니다. immutable의 의미는 수정불가능한을 의미합니다. 

<br>

## ✔️fields

인덱스가 필요한 컬럼명을 리스트 또는 튜플로 담습니다. 기본값으로 Index는 내림차순으로 각 컬럼마다 생성됩니다. 하지만 어떤 컬럼은 내림차순, 어떤 컬럼은 오름차순으로 정렬이 필요할 때 사용하게 되는 것입니다.
예를 들어 아래 코드를 보면,
```python
Index(fields=['headline', '-pub_date'])
```
headline은 오름차순, pub_date는 내림차순으로 설정하게 되는 것입니다. 단, MySQL에서는 제공하지 않는 기능이며 앞에 -를 붙여도 오름차순으로 정렬되어 나온다고 합니다. 

<br>

## ✔️name

인덱스의 이름입니다. 다만 인덱스의 이름을 설정하지 않으면 Django에서는 자동으로 만들어준다고 합니다. 다른 데이터베이스와의 호환성을 위하여 인덱스의 이름은 30자를 초과할 수 없으며 숫자 또는 밑줄로 시작해서도 안된다고 합니다.

<br>

## ✔️tablespace

처음에 tablespace라는 걸 보고, Meta에서 선언하는 db_table과 같은 개념인 줄 알았는데 그건 아니었습니다. 쉽게 말해 테이블을 저장하는 공간인데 프로젝트를 할 때 마다 여러개의 테이블이 만들어질테고, 그 테이블이 저장될 공간이 필요할 것입니다. 그 공간을 tablespace라고 합니다. 어떻게보면 객체의 기본이 되는 실제 데이터를 보관할 수 있는 저장위치라고 할 수 있는 것입니다. 
인덱스가 두 개 이상 설정되어 있어야 tablespace가 생성됩니다. 아래의 코드로 예를 들면
```python
class TablespaceExample(models.Model):
    name = models.CharField(max_length=30, db_index=True, db_tablespace="indexes")
    data = models.CharField(max_length=255, db_index=True)
    shortcut = models.CharField(max_length=7)
    edges = models.ManyToManyField(to="self", db_tablespace="indexes")

    class Meta:
        db_tablespace = "tables"
        indexes = [models.Index(fields=['shortcut'], db_tablespace='other_indexes')]
```

name과 edges 필드에 대한 인덱스는 class Meta에서 선언된 indexes라는 테이블스페이스에 저장됩니다. 이렇게 해서 index를 만들수 있게 되는 것입니다. 컬럼을 만들 때. db_tablespace를 지정하지 않으면 자동으로 Meta에서 설정한 tablespace로 지정됩니다.

<br>

## ✔️opclasses

opclasses는 인덱스에 사용할 PostgreSQL의 연산자 클래스 이름입니다. 사용자 정의 연산자 클래스가 필요한 경우, 각 인덱스의 필드에 대해 하나씩 설정해야 하는데, 이 부분은 PostgreSQL에만 적용될 내용이므로 넘어가겠습니다.

<br>

## ✔️condition

테이블이 매우 크거나 쿼리가 많을 경우, 유용한 condition 입니다. Django의 Q객체를 이용하여 더 구체화할 수 있습니다.
예를 들어 condition=Q(pages__gte=400) 이라고 선언하면 pages가 400이상인 것만 조건으로 걸어서 인덱싱을 한다는 뜻이됩니다. PostgreSQL, SQLite, Oracle, MySQL, MariaDB에 대해 제약사항이 있는데 PostgreSQL에 대해서만 간단하게 써보겠습니다.

PostgreSQL은 조건에서 참조되는 함수가 immutable해야 합니다. 예를 들어 datetime은 immutable하고 DateTimeField는 immutable하지 않다는 반대의 특징을 가지고 있습니다. 그러면 DateTimeField에 대해 인덱싱을 하려고 할 때 tzinfo라는 값까지 함께 제공해야 합니다. tzinfo는 datetime 및 time 클래스에서 시간 조정에 대한 커스터마이징 기능을 제공하기 때문에 DateTimeField를 인덱싱할 수 있게 도와주기 때문입니다.

<br>

## ✔️Include

include를 선언하면, 그 필드만 선택하고 인덱싱 필드로만 필터링하는 쿼리에 인덱싱 전용 스캔을 사용할 수 있으며 컬럼명을 리스트 혹은 튜플로 나타내야 합니다. 말이 어려우니 코드로 예시를 들어보겠습니다.
```python
Index(name='covering_index', fields=['headline'], include=['pub_date'])
```
이렇게 되면 인덱스에서 데이터를 가져오는 동안 headline에서 필터링하고 pub_date도 선택할 수 있습니다. 쉽게 말해 인덱스안의 작은 인덱스를 하나 더 설정할 수 있게 됩니다. 다만, 정렬이나 필터링은 할 수 없습니다. 정렬 및 필터링이 headline으로는 가능하나 pub_date로는 안된다는 의미입니다. 
