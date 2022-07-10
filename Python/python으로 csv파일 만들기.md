# CSV

Celery에 이어서 추가 내용인데, 수집한 데이터를 CSV 파일로 만든 뒤 S3에 저장하고 있습니다.

그 다음 데이터베이스에 저장하는 과정을 통해 앱 이용자들에게 정보를 제공합니다.

[나이스 오픈 API](https://open.neis.go.kr/portal/data/service/selectServicePage.do?page=1&rows=10&sortColumn=&sortDirection=&infId=OPEN14020190311111456561190&infSeq=2)에서 경기도 내에 있는 외국어 고등학교에 존재하는 학과 리스트들을 csv 파일로 만드는 과정에 대해 포스팅 해보겠습니다.

<br>

## 사전 준비

우선, csv파일을 생각해보면 가장 첫 줄에는 컬럼명이 들어가 있습니다.

그러면, 그 컬럼들에 대한 정의가 필요합니다.

오픈API 홈페이지에 가면 출력 컬럼들이 나오게 됩니다.

<img width="521" alt="image" src="https://user-images.githubusercontent.com/88086271/178133500-91181720-5307-4ded-92ea-9a60c63ef535.png">

이 해당 컬럼을 우선 정의해줍니다.

 ```python
MAJOR_INFO = {
"ATPT_OFCDC_SC_CODE" : "시도교육청코드",
"ATPT_OFCDC_SC_NM" : "시도교육청명",
"SD_SCHUL_CODE" : "표준학교코드",
"SCHUL_NM" : "학교명",
"DGHT_CRSE_SC_NM" : "주야과정명",
"ORD_SC_NM" : "계열명",
"DDDEP_NM" : "학과명",
"LOAD_DTM" : "수정일"
}
```

<br>

그 다음, 한 줄 한 줄 읽을때마다 하나의 파일로 만들어서 저장해야 합니다.

그 때 사용하는 것이 ```io.StringIO()``` 입니다. 

[점프투파이썬](https://wikidocs.net/122776)에서 ```StringIO```에 대한 설명이 있는데, 문자열을 파일객체처럼 다룰 수 있게 해주는 클래스라고 합니다.

<img width="348" alt="image" src="https://user-images.githubusercontent.com/88086271/178134068-b6dfd1ed-6b63-46ed-a1e4-43578d0bfb39.png">

예를 들어 StringIO("hihi") 라고 저장하면, "hihi"라는 문자열이 담긴 파일 객체로 사용하겠다는 의미로 해석할 수 있습니다.

<br>

이렇게 파일을 지정했으면, 이 파일에 한 줄 한 줄 어떤 것에 대한 값인지 읽을 수 있도록 해야 합니다.

그 때 사용하는 것이 ```DictWriter```라는 메소드입니다.

[python csv 공식문서](https://docs.python.org/ko/3/library/csv.html)에 나와 있는데, 파일을 지정하고 해당 파일에서 읽어올 필드명을 설정하면 그에 맞는 값을 읽을 수 있습니다.

<br> 

마지막으로 데이터도 준비가 되었고, 어떻게 값들을 읽을 지 알았으면 그 값을 저장하는 방법에 대해 알아야 합니다.

그 때 사용하는 것이 ```writerow```입니다.

```writerow```와 ```writerows``` 두 개가 있는데, 전자는 한 줄씩 입력할 때 사용하고 후자는 여러 줄을 한 번에 입력할 때 사용합니다.

자세한 예시는 [여기](https://stackoverflow.com/questions/33091980/difference-between-writerow-and-writerows-methods-of-python-csv-module)에서 확인할 수 있는데, 저는 두 개 다 사용해보겠습니다.

<br>

## writerow

위에서 설명한대로 코드를 작성해보겠습니다.

저는 나이스 오픈 API에서 불러온 정보를 responses로 저장했습니다.

responses는 리스트 안에 딕셔너리 형태로 데이터들이 저장되어 있습니다.

<img width="293" alt="image" src="https://user-images.githubusercontent.com/88086271/178134305-f8d5da03-e382-40fb-8a96-7c4a768cb132.png">


```python
import io

csv_writerow = io.StringIO()

writer = csv.DictWriter(csv_writerow, fieldnames=MAJOR_INFO.keys())

for response in responses:
  if "외국어" in response["SCHUL_NM"]:
    writer.writerow(response)
```

이렇게 했을 떄 ```csv_writerow```에 저장된 값을 확인하기 위해 ```getvalue()```라는 메소드를 사용합니다.

<img width="643" alt="image" src="https://user-images.githubusercontent.com/88086271/178134401-e9429c71-8cf5-4530-b883-374ea8d8e5ae.png">

