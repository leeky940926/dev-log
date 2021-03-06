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

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FAr9W3%2FbtrvoAqyZua%2FYlCss2Ohk9xlByiyghISe1%2Fimg.png)

<br>

## ✔️MSA : MicroService Architecture

MSA는 애플리케이션 구축을 위한 아키텍처 기반의 접근 방식입니다. 앱별로 분산되어 있고, 독립성이 강하기 때문에 개발팀이 애플리케이션의 새로운 구성 요소를 신속히 빌드하여 변화하는 비즈니스 요구를 충족할 수 있습니다. 덕분에 개별 서비스가 다른 서비스에 영향을 주지 않으면서 작동할 수 있습니다. 

<br>

애플리케이션 초기에는 기존 애플리케이션에 최소한의 변경사항이 있어도 자체적인 QA주기에 따라 대규모 업데이트를 해야 했습니다. 전체 애플리케이션의 소스 코드는 하나의 배포 유닛으로 내장되었는데, 이런 방식을 모놀리식이라고도 불렀습니다. 오류가 발생했을 시, 전체를 오프라인으로 전환한다음 해결해야 하는 불편함이 있었습니다.

<br>

서비스 지향 아키텍처는 애플리케이션을 별개의 재사용 가능한 서비스 단위로 분할하며 이 서비스들은 [엔터프라이즈 서비스 버스](https://www.ibm.com/kr-ko/cloud/learn/esb)를 통해 통신합니다. 이러한 방식은 서비스의 구축, 테스트, 수정을 동시에 수행할 수 있기 때문에 모놀리식 개발 주기는 필요가 없는 것입니다. 

이렇게 MSA의 개념부터 서비스 지향 아키텍처에 대해 알아봤는데, MSA의 장점이 어떤 것이 있을까요?

* 첫 번째로 더 빠른 출시가 가능합니다. <br>
개발주기가 단축되기 때문에 MSA는 보다 빠른 배포 및 업데이트를 지원합니다.
* 확장성이 높습니다. <br>
특정 서비스에 대한 수요가 증가했을 때, 필요에 따라 여러 서버 및 인프라에 배포할 수 있습니다.
* 뛰어난 복구 능력 <br>
위에 언급했듯이 독립적이기 때문에 영향을 주지 않습니다. 즉, 장애가 발생해도 전체 애플리케이션에 영향을 주지 않는다는 뜻입니다.
* 손쉬운 배포 <br>
독립적이고 모듈화가 되었기 때문에 배포에 대한 우려가 많이 사라졌습니다. 덕분에 배포가 모놀리식 개발보다 더 쉬워졌습니다.
* 편한 엑세스 <br>
애플리케이션 분할을 통해 업데이트 개선이 용이합니다. 이로인해 에자일한 개발 방식(EX. 스크럼)과 결합할 경우 개발 주기를 더욱 가속화할 수 있습니다.

<br>

물론, 장점만 있다고 하면 다 도입하겠지만 과제가 많기 때문에 도입이 어렵습니다.

* 구축 <br>
서비스 간 종속성을 파악하는 데 시간을 투입해야 합니다. 이 종속성이 우리 데이터에 어떤 영향을 끼치나 고려해야 합니다.
* 테스트 <br>
통합테스트, E2E테스트를 수행하는 게 중요하면서도 어렵습니다. 아키텍처를 어떻게 구성했는지에 따라 한 부분에 장애가 발생했을 때 다른 부분에 장애를 일으킬 수 있다는 점을 유의해야 합니다.
* 버전 관리 <br>
새로운 버전으로 업데이트할 때 버전간의 호환성 문제가 생길 수 있습니다. 이걸 해결하기위해 조건부 로직을 구현할 수 있지만, 이것은 결국 기술부채로 이어질 수 있습니다. 
* 배포 <br>
배포를 쉽게 하려면 자동화에 투자를 해야 합니다. 독립적으로 나누어져 있다는 것은 배포할 때 신경 쓸 사항이 많다는 뜻이므로 수동배포가 어려워지기 때문입니다.
* 로그 관리 <br>
분산 시스템에서는 모든 내용을 한 곳에 모을 수 있는 중앙집중식 로그가 필요합니다. 그렇지 않으면 확장 시 관리가 불가능해집니다.
* 모니터링<br> 
문제의 근원을 잡아내려면 시스템을 중앙에서 파악할 수 있는 능력이 무엇보다 필요합니다.

<br>

## ✔️마치며..

이렇게 패턴에 대해 알아본 뒤 MSA에 대해서까지 알아보았습니다. 저희 회사의 경우 앱별로 의존성이 커서 이걸 분리하고 MSA실현을 목표로 개발팀이 노력하고 있는 중입니다. MSA가 이런 거구나 라는 걸 직접 구현해보며 느껴보고 싶습니다.
