# Chapter 17 접근 및 보안 구성

## IAM 관리

* IAM을 작업할 때 수행해야할 공통 업무
    * IAM 할당 계정 조회
    * IAM roles 할당
    * 커스텀 roles 정의

### IAM 할당 계정 조회

* Console - IAM & Admin
* Predefined IAM role
    * 수행 권한의 집합
    * 작업을 수행하는 데 필요한 권한만으로 ID를 제공
* Primitive role
    * IAM 이전에 사용
    * Viewer
        * read-only 동작을 수행하는 권한
    * Editor
        * Viewer 권한과 엔티티를 수정하는 권한
    * Owner
        * Editor 권한을 갖고, 엔티티의 role과 수행권한을 관리
        * 프로젝트의 billing도 설정 가능
* Command Line
    ```bash
    gcloud projects get-iam-policy [PROJECT_ID]
    ```
* 사전에 정의된 role은 서비스와 그룹화
    * App Engine 예시
        * App Engine Admin, 어플리케이션과 구성 설정을 읽고, 쓰고, 수정하는 권한, `roles/appengine.admin`
        * App Engine Service Admin, 구성 설정에 대한 read-only 접근 권한과, 모듈과 버전 수준의 설정에 대한 write 접근 권한, `roles/appengine.serviceAdmin`
        * App Engine Deployer, 어플리케이션 구성 및 설정에 read-only 접근 권한과 신규 버전을 생성하기 위한 write 접근 권한, `roles/appengine.deployer`
        * App Engine Viewer, 어플리케이션 구성 및 설정에 read-only 접근 권한, `roles/appengine.appViewer`
        * App Engine Code Viewer, 모든 어플리케이션 구성, 설정, 배포된 소스코드에 대한 read-only 접근 권한, `roles/appengine.codeViewer`

### 계정 및 그룹에 IAM roles 할당

* Console - IAM & Admin - IAM - Add
    * New member
        * 사용자나 그룹의 이름 입력
    * Select A Role
        * 다수의 role 추가 가능
* 역할을 할당할 때 부여되는 최소 단위의 권한 조회
    * `gcloud iam roles describe`
    * Console - IAM & Admin - Roles - role 선택
* IAM roles은 최소 권한(Least Privilege)과 업무 분리(Separation of Duties)를 지원해야 함
    * 업무 분리
        * 단일 사용자는 위험을 초래할 수 있는 민감한 작업을 수행할 수 없어야 함
    * 최소 권한
        * 사전 정의된 role에 최소한의 권한을 할당

```bash
# 프로젝트 멤버에게 role 할당
gcloud projects add-iam-policy-binding [RESOURCE_NAME] --member user:[USER_EMAIL] --role [ROLE_ID]
```

### 커스텀 IAM role 정의

* 사전 정의된 IAM role이 요구를 충족하지 않는다면, 커스텀 role 정의
* Console - IAM & Admin - Roles - Create Role
    * 이름, 설명, Identifier, launch stage, set of permissions 지정
    * lauch stage
        * Alpha, Beta, General Availability, Disabled
* Command Line

```bash
gcloud iam roles create [ROLE_ID] --project [PROJECT_ID] --title [ROLE_TITLE] --description [ROLE_DESCRIPTION] --permissions [PERMISSIONS_LIST] --stage [LAUNCH_STAGE]
```

## 서비스 계정 관리

* 사용자의 독립적인 ID를 제공하는데 사용
* role을 부여할 수 있는 ID
* 서비스 계정이 VM에 할당되고, 작업을 수행하는 서비스 계정에 사용할 수 있는 권한을 부여

### Scope가 있는 서비스 계정 관리

* Scope
    * 일부 동작을 수행하는 VM에 부여된 권한
    * API 메소드에 접근을 승인
    * VM에 할당된 서비스 계정은 관련된 role이 있음
    * VM에 대한 접근을 구성하기 위해 IAM role과 scope 둘다 구성해야 함
* Scope는 [https://www.googleapis.com/auth/](https://www.googleapis.com/auth/)로 시작하는 URL을 사용하고, 뒤에 리소스의 권한이 추가됨
* 예시
    * Cloud Storage의 데이터를 조회할 수 있는 scope
        * [https://www.googleapis.com/auth/devstorage.read_only](https://www.googleapis.com/auth/devstorage.read_only)
* 인스턴스는 오직 서비스 계정에 할당된 IAM role과 인스턴스에 정의된 scope에 따라서 동작을 수행
* Scope 설정
    * Console - VM Instance - Instance Detail - Edit - Access Scope
    * 옵션
        * Allow Default Access, 기본 (보통 default 접근이면 충분)
        * Allow Full Access To All Cloud APIs
        * Set Access For Each API, 개별적으로 scope 선택
    * Command Line
    ```bash
    gcloud compute instances set-service-account [INSTANCE_NAME] [--serive-account [SERVICE_ACCOUNT_EMAIL] | [--no-service-account]] [--no-scopes | --scopes [SCOPES, ...]]
    ```

### VM 인스턴스에 서비스 계정 할당

* Console - IAM & Admin - Service Account - Create Service Account
    * name, identifier, description 입력
* Console - Compute Engine - VM Instance - VM Instance Detail - Edit - Service Account

```bash
gcloud compute instances create [INSTANCE_NAME] --service-account [SERVICE_ACCOUNT_EMAIL]
```

## 감시 로그 조회

* Console - Stackdriver Logging
* 리소스, 표시할 로그 타입, 로그 레벨, 엔티티를 표시할 기간 선택

[맨 위로](#chapter-17-%ec%a0%91%ea%b7%bc-%eb%b0%8f-%eb%b3%b4%ec%95%88-%ea%b5%ac%ec%84%b1)

[본문 보기](../Chapter_17.md)