## 문자열 검색 코루틴 만들기

[티스토리 블로그 바로 가기](https://kyleeee.tistory.com/entry/TIL35-%EC%BD%94%EB%A3%A8%ED%8B%B4-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B066-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%BD%94%EB%94%A9%EB%8F%84%EC%9E%A5-%EB%AC%B8%EC%A0%9C)

<br>



다음 소스 코드를 완성하여 문자열에서 특정 단어가 있으면 True, 없으면 False가 출력되게 만드세요.

<br>

```python
f = find('Python')
next(f)
 
print(f.send('Hello, Python!'))
print(f.send('Hello, world!'))
print(f.send('Python Script'))
 
f.close()

...
True
False
True
```

<br>

우선, find()는 문자열을 받으므로 def find(word: str) 이렇게 시작합니다.

그리고 send()를 통해 문장을 보내므로  

```python
def find(word: str):
  result = False
  while True:
    sentence = (yield result)
    result = word in sentence
```

<br>

우리가 보내야 할 값은 T/F기 때문에 코루틴 값 외부로 보내기에서 사용했던 yield ? 형태가 됩니다.

sentence에는 파라미터로 받은 word가 있는지 체크해서 result로 넣어줘 결과를 보여줍니다.
