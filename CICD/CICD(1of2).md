# CI/CD(1/2)

[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL11-CICD12)

<br>


## ✔️Intro

CI/CD는 애플리케이션 개발 단계를 자동화하여 애플리케이션을 보다 짧은 주기로 고객에게 제공하는 방법입니다.
CI/CD는 새로운 코드 통합으로 인해 개발 및 운영팀에 발생하는 문제를 해결하기 위한 솔루션이며, 애플리케이션의 통합 및 테스트 단계에서부터 제공 및 배포에 이르는 라이프사이클 전체에 걸쳐 지속적인 자동화와 지속적인 모니터링을 제공합니다.
이러한 구축사례를 일반적으로 CI/CD 파이프라인 이라고 부릅니다.

이번 글은 [RedHat](https://www.redhat.com/ko/topics/devops/what-is-ci-cd)을 참고하여 작성했습니다.

<br>

## ✔️CI 란?

Continuous Integration의 약자로 지속적 통합이라는 뜻을 가지고 있습니다. 우리는 개발할 때 각자만의 작업공간은 Branch를 만들어서 동시에 작업합니다. 작업 후 Merge를 할 때, 특정한 날에 한 번에 한다고 하면 어떻게 될까요? 엄청난 Conflict가 발생할 가능성이 매우 농후합니다. 그러면 그만큼의 아까운 시간이 소모되는 참사가 발생하게 되는 것입니다.

CI를 통해 개발자들은 이 작업을 더욱 수월하게 할 수 있습니다. 변경사항이 Merge된다면, 애플리케이션이 손상되지 않도록 자동으로 애플리케이션을 구축하고, 각기 다른 레벨의 자동화 테스트 실행을 통해 변경사항이 잘 적용됐는지 확인하기 때문입니다. 다시 말해, 클래스와 함수부터 전체 애플리케이션을 구성하는 서로 다른 모듈에 이르기까지 모든 것에 대한 테스트를 수행합니다. 자동화된 테스트에서 기존코드와 신규 코드 간의 충돌이 발견되면 CI를 통해 이러한 버그를 더욱 빠르게 수정할 수 있습니다.

즉, CI는 개발자를 위한 자동화 프로세스를 의미하며, 성공적으로 구현할 경우 애플리케이션에 대한 새로운 코드 변경 사항이 정기적으로 빌드 및 테스트 되어 공유 레포지토리에 통합되므로 여러 명의 개발자가 동시에 애플리케이션 개발과 관련된 코드 작업을 할 경우 서로 충돌할 수 있는 문제를 해결할 수 있습니다.

<br>

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Flhoz2%2FbtruyM55wBl%2FGDXFQFDwsfOXUEToNQJW31%2Fimg.png)

일반적인 CI 프로세스입니다.

CI의 workflow는 사용 툴, 프로그래밍 언어, 프로젝트와 그 외 다른 요소 등에 많은 영향을 받습니다.
하지만 일반적인 workflow는 다음과 같습니다.

<br>

* Pushing Code Repository
* Static analysis
* Pre-deployment testing
* Packaging and deployment to the test environment
* Post-deployment testing

<br>

각 단계에 대해 설명을 해보자면

* Push Code Repository <br>
우리의 Repository에 commit-push를 하는 것을 의미합니다. workflow에서 commit을 하는 것부터 CI의 시작이기 때문입니다.

* Static analysis <br>
Static analysis는 소프트웨어를 시작할 필요 없이 애플리케이션의 기본 코드로부터 행해집니다. Static analysis의 목표는 코드가 버그 발생할 가능성이 없다는 것을 증명하는 것과 표준 형태와 스타일을 확정 짓는 것입니다. RedHat에서는 FIndBugs라는 프로그램을 설치해서 보여주는 것까지 있는데, 자바 기반인 점과 개념에 대해 알고가려는 포스팅이어서 생략하겠습니다. 읽어보니 배포 전 테스트에 관한 내용은 Pre-deployment testing에 대한 부분도 있으며, 시간 나실 때 읽어보시는 것도 추천드립니다.

* Packaging and deployment to the test environment <br>
프로젝트 종류에 따라, 애플리케이션이 빌드, 패키징, 테스트 또는 스테이징 환경으로 전송됩니다. 이렇게 하면 통합된 변경 사항이 다른 코드들과 잘 합쳐져서 기능 테스트를 수행할 수 있도록 배포 될 수 있습니다. 애플리케이션을 배포해야 하는 테스트의 경우, CI 파이프라인에서 테스트가 실행됩니다. 테스트는 사용 툴, 프레임워크와 언어 등에 따라 다르지만 대체로 기능 테스트이며 퍼포먼스 테스트입니다. 이게 성공적으로 종료된다면 유저들에게 만족을 줄 수 있게 되는 것입니다. 또한 애플리케이션은 사용자 또는 QA팀의 수동 테스트를 거쳐 클라이언트의 요구사항에 맞는지 확인할 수 있습니다. 그 결과 Production으로 배포할 수 있게 되고 성공적인 CI를 도입했다고 말 할 수 있게 되는 것입니다.

<br>

## ✔️마치며..


이상 CI/CD 중 CI 대해 알아보았습니다.
CI/CD 포스팅은 CI/CD/Jenkins 순서로 진행 예정입니다.
다만, Jenkins 부분은 공부가 더 필요하여 시간이 더 걸릴 수 있습니다.

