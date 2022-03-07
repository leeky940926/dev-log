# Pattern
[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL16-Pattern)

<br>

## ✔️Intro

디자인패턴-아키텍처패턴-MSA까지 이어지는 내용의 포스팅입니다.
디자인 패턴의 출처는 이기적 정보처리기사 실기책입니다. 아키텍처 패턴은 MVC에 대해 포스팅 할 예정이며 [towardsdatascience](https://towardsdatascience.com/10-common-software-architectural-patterns-in-a-nutshell-a0b47a1e9013)를 참고했습니다. MSA는 [RedHat](https://www.redhat.com/ko/topics/microservices/what-are-microservices)을 참고했습니다.

<br>

## ✔️디자인패턴

소프트웨어 개발 중 나타나는 과제를 해결하기 위한 방법 중 한 가지이며 자주 사용하는 설계 형태를 정형화하여 유형별로 설계 템플릿을 만들어 둔 것을 의미합니다. 다양한 SW를 개발할 때 공통되는 설계 문제가 존재하는데, 각 해결책 사이에도 공통점이 있으며 이러한 유사점을 패턴이라고 합니다. 객체 지향 프로그래밍 설계 시 유사한 상황에서 구조적인 문제를 해결할 수 있도록 방안을 제공합니다. 디자인패턴 덕분에 개발자 간 원활한 소통, 소프트웨어 구조 파악 용이, 설계 변경에 대한 유연한 대처, 개발의 효율성, 유지 보수성, 운용성 등 품질향상에 도움을 줍니다.

<br>

## ✔️아키텍처패턴 : MVC

아키텍처 패턴은 디자인 패턴의 상위 설계 버전입니다. 즉, 시스템 전체 구조를 설계하기 위한 참조 모델을 아키텍처 패턴이라고 하며, 서브 시스템 내 컴포넌트와 그들 간의 관계를 구성하기 위한 참조 모델을 디자인 패턴이라고 합니다. 그러면 MVC란 무엇일까요?

MVC 패턴이란 Model-View-Controller의 약자로 애플리케이션을 3개의 파트로 나누는 패턴을 말합니다. 

* Model : 핵심 기능과 데이터를 포함합니다.
* View : 사용자에게 정보를 표시합니다.
* Controller : 사용자로부터 입력을 처리합니다.

MVC패턴은 주로 Rails나 Django같은 웹 프레임워크에 사용됩니다. Django의 경우, Template를 이용한 MVT패턴으로도 배우는데, View의 내용도 Template로 전달되기 때문입니다.

<br>

✔️MSA : MicroService Architecture
