# TDD
[티스토리 포스팅 바로가기]()

<br>

## ✔️Intro

오늘은 TDD(Test-Driven-Development, 테스트 주도 개발)에 대해 포스팅해보겠습니다. 저희 회사에서도 서버 개발팀 방향성에 포함된 것 중 하나가 테스트 코드를 통한 테스트 방법 개선이고, 저 역시 프로젝트를 해보며 Unit Test를 하다 보니 테스트 코드를 작성해놓는 것에 대한 중요성을 알고 있었습니다. 그래서 TDD에 대한 간단한 설명과 Django에서 사용하는 Pytest를 이용해 Unit Test를 진행해보겠습니다. 

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

이렇게 TDD에 대해 알아보았으니, Django에서 Pytest를 이용해 테스트를 해보겠습니다.
