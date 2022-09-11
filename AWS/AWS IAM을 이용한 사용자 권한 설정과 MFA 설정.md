# AWS IAM 사용자 추가 및 MFA 설정


회사에서 AWS 관련 업무를 하려면 제 회사 AWS 계정을 추가하여 권한을 줘야 합니다.

그래서 오늘 AWS IAM을 통해 신규 입사자가 왔을 때, 어떻게 추가해주면 되는지 설정해보겠습니다.

모든 출처는 [AWS IAM 공식 문서](https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/introduction.html)입니다.

<br>


# AWS IAM

IAM이란 Identify and Access Management의 약자로, 리소스를 사용할 수 있도록 인증 및 권한 부여된 대상을 제어할 수 있습니다.

AWS 계정 생성 시 모든 리소스와 서비스에 접근 가능한 루트 사용자가 있으며, 일상적인 사용 시 루트 사용자 사용을 권장하지 않고 있습니다.

[](https://user-images.githubusercontent.com/88086271/189513120-390462d1-30d9-4fdb-89f6-5c43aa164c98.png)![image](https://user-images.githubusercontent.com/88086271/189514392-d4a7602f-ad4a-4ed3-9789-b6f65eaaad0d.png)

<br> 

그래서 이렇게 그룹을 나누어 역할에 맞는 권한을 부여할 수 있도록 하는 것이 사용 이유 중 하나입니다.

## 관리자 IAM 사용자 및 사용자그룹 생성

1. 루트 사용자 로그인 후, IAM 메뉴에 접속합니다.
2. 상단의 탐색표시줄에서 우측 상단의 제 계정명을 선택한 뒤, 계정을 클릭합니다.

[](https://user-images.githubusercontent.com/88086271/189513280-0c05af06-2092-4cea-b5f1-50e9bcf5041d.png)![image](https://user-images.githubusercontent.com/88086271/189514412-c453b9b8-af13-4d58-8f0e-b8cd9d6f976a.png)

<br>

3. 결제정보에 대한 IAM 사용자 및 역할 엑세스에서 우측에 있는 편집 버튼을 누릅니다.

[](https://user-images.githubusercontent.com/88086271/189513334-dbfdb292-9427-4d8c-a387-6f3bd9442ab7.png)![image](https://user-images.githubusercontent.com/88086271/189514422-6359db1a-6bec-49b8-9c79-d89d40677150.png)

<br>

4. 그러면 하단에 IAM 엑세스 활성화를 선택할 수 있게 화면이 나오고, 선택 후 업데이틀를 합니다. 그 다음 IAM 메뉴로 이동합니다.

[](https://user-images.githubusercontent.com/88086271/189513368-54c8b86a-1c6f-4f15-ab15-5cd03c951252.png)![image](https://user-images.githubusercontent.com/88086271/189514453-9e5d007f-237c-44b2-9171-31eadd327369.png)

<br>

5. IAM -> 엑세스 관리 -> 사용자로 이동해서 사용자 추가를 클릭합니다.
6. 사용자 이름에 Administrator를 입력하고, 그 외에는 아래 이미지와 같이 설정해보겠습니다.

[](https://user-images.githubusercontent.com/88086271/189513549-c34b372c-ecd5-46de-8b3f-3c02c5ad6f63)![image](https://user-images.githubusercontent.com/88086271/189514463-cd3968fe-360b-4e0d-8263-e79e9b30f083.png)


7. 그 다음 나오는 사용자 추가 단계에서 그룹을 생성해보겠습니다.

[](https://user-images.githubusercontent.com/88086271/189513590-d8345a01-5a38-43bb-9448-f4c9ca073ff1.png)![image](https://user-images.githubusercontent.com/88086271/189514475-077132e2-5cba-448b-a264-2c22f083172a.png)

<br>

그룹 이름도 Administrator라고 생성한 후, 최상단의 AdministratorAccess만 체크 후 생성합니다.
태그의 경우, 선택사항이라 지금은 하지 않겠습니다.
이렇게 생성 후 로그인 해보겠습니다.

8. IAM으로 로그인 시, 별칭과 사용자이름, 비밀번호를 입력해야 합니다. 별칭은 IAM 메뉴 우측에 계정ID와 함께 나와 있으며 사용자 이름에는 아까 만든 Administrator와 비밀번호를 입력합니다.

[](https://user-images.githubusercontent.com/88086271/189513911-87cfd41c-66ac-47f2-b406-0efde5ccd40d.png)![image](https://user-images.githubusercontent.com/88086271/189514520-984dcd36-d147-4446-97ff-24183b52bb27.png)

<br>

[](https://user-images.githubusercontent.com/88086271/189513978-0e3522b7-b1ef-403b-ad85-7cc087d82550.png)![image](https://user-images.githubusercontent.com/88086271/189514527-ba829fb8-57a7-462e-940a-9c2d4f4702f1.png)

<br>

이렇게 해서 로그인이 되면 IAM 사용자 생성이 완료되었습니다.

## MFA 설정

<br>

MFA란 멀티 팩터 인증이란 의미로, 인증 단계를 한 번 더 거칠 수 있게 설정하는 것입니다.
AWS 웹 사이트 이외에 다른 서비스에 접근할때도 사용 가능하며 저는 Authenticator 라는 앱을 이용해서 설정했습니다.
(AppStore or PlayStore에서 설치하시면 됩니다.)

1. 방금 만든 Administrator 계정을 클릭하면 보안 자격 증명탭이 있습니다.
2. 보안 자격 증명에 할당된 MFA 디바이스라는 항목이 있는데 관리 버튼을 클릭합니다.
3. 여기서 저는 가상 MFA 디바이스를 선택하겠습니다.

[](https://user-images.githubusercontent.com/88086271/189514192-f27c872c-48ec-4667-be3a-ec436feba1f1.png)![image](https://user-images.githubusercontent.com/88086271/189514546-ec454a23-4471-4f16-a3af-68ed5c1bcb3b.png)

<br>

4. 그러면 가상 MFA 디바이스에서 입력하란대로 입력을 하시면 됩니다.
5. 그 후 다시 로그인을 하기 위해 IAM 계정별칙과 사용자 이름, 비밀번호를 누르면 MFA 인증 번호를 입력하라고 나옵니다.


[](https://user-images.githubusercontent.com/88086271/189514247-417e26c0-fef8-4fe0-98cf-462c576781ab.png)![image](https://user-images.githubusercontent.com/88086271/189514553-423c6b99-087f-42db-9bf0-f140337ac472.png)


입력하면 로그인이 정상적으로 되는 걸 확인하실 수 있습니다.
