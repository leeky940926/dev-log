# get_fieldname_display


Django에서 모델링을 할 때, 보기 중 선택을 해야 하는 필드가 있을 수 있습니다.


대표적으로 S, M, L, XL 등으로 나눠진 옷 사이즈가 대표적이죠.



이 때 해당 필드명으로 접근하는 게 일반적이지만 Django에서 접근할 수 있게 지원해주는 메소드가 있어서 포스팅해보겠습니다.



역시 모든 출처는 [공식문서](https://docs.djangoproject.com/en/4.0/ref/models/instances/#django.db.models.Model.get_FOO_display)입니다.

<br>

## Modeling

<br>

```python
class Person(models.Model):
    SHIRT_SIZES = (
        ('S', 'Small'),
        ('M', 'Medium'),
        ('L', 'Large'),
    )
    name = models.CharField(max_length=60)
    shirt_size = models.CharField(max_length=2, choices=SHIRT_SIZES)
```



사람이라는 모델을 만들고, 사이즈는 S, M, L을 선택할 수 있게 튜플형태의 데이터로 만들었습니다.


그리고, 필드속성 중 choices에 SHIRT_SIZES를 선언하겠습니다.



<br>

## 구현


데이터의 빠른 추가를 위해 shell_plus를 사용해서 데이터를 넣어줍니다.


(shell_plus 진짜 편합니다 굿굿)

<br>


![image](https://user-images.githubusercontent.com/88086271/154270732-32c7ac0d-18d1-48e1-8023-aeead27739f8.png)
<br>

p라는 변수에 name="django king", shirt_size="L"인 데이터를 하나 만들었습니다.



choices를 사용하는 이유는 내가 "S"를 선택하면 "Small"이 리턴되도록 설정하기 위함인데(쉽게 말해 딕셔너리의 key를 선택하면 value가 나와야 하는데), shirt_size로 접근하면 저장한 값이 나오지 choices에서 내가 선정한 그에 상응하는 값이 나오지 않는다는 것이죠.



그래서 choices를 쓰지 말고 그냥 "Small", "Large" 이렇게 데이터를 넣자! 하는 경우도 종종 있습니다.



하지만 문자열로 긴 값을 넣는 특성상,상황에 따라 대소문자구분 케이스나 "Large"로 넣어야 하는데 오타 실수로 "Laege"라고 넣으면 데이터 필터링에 문제가 생기는 등 별로 권장되지 않는 방법입니다.



이럴 때 사용하기 위해 Django에서 지원하는 기능이 있습니다.



바로 get_필드명_display() 입니다.

<br>


![image](https://user-images.githubusercontent.com/88086271/154270786-6938f039-de70-4a4d-95e4-59145ab73494.png)

<br>

바로 이렇게 쓰면 choices를 쓰는 이유에 맞는 값을 가져올 수 있게 됩니다.



오늘 리팩토링을 하는데 get_필드명_display()가 나와서 기존에 어떤 사람이 작성했는지 내용을 보려고 검색했는데 해당 메소드가 우리 회사 레포 어디를 찾아봐도 안 나왔었습니다.



그래서 사수분께 이러한 메소드가 선언되어 있는데, 내용을 보고 싶어서 찾아봤더니 어디에도 없다고 이야기를 한 뒤 리팩토링하는 해당 API의 히스토리를 물어봤었습니다.



그 때 이제 Django에 이런 기능이 있다고 하면서 설명을 해주셨습니다.



공부할 때 공식문서 좀 읽어볼 걸..
