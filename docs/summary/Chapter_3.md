# Chapter 3 Project, Service Accounts, Billing

## GCP 프로젝트와 계정 구성 방법

* GCP는 리소스를 그룹화하고, 단일 유닛으로 관리하는 방법을 제공
  * Resource hierarchy
* 리소스 계층에서 리소스에 대한 접근은 사용자가 정의할 수 있는 정책의 집합으로 제어

### GCP Resource Hierarchy

* GCP 리소스는 3가지 레벨로 구성
  * Organization
  * Folder
  * Project

#### Organization(조직)

* 조직은 리소스 계층의 root
* G-Suite 도메인과 Cloud Identity 계정은 Organization에 매핑
  * G Suite를 사용한다면 GCP 계층에 Organization을 생성할 수 있음
  * G Suite를 사용하지 않는다면, Cloud Identity를 사용할 수 있음
    * 구글은 Identity as a Service(IDaaS)를 제공
* 단일 Cloud Identity는 최대 하나의 Organization에 연관
* 최고 관리자가 있으며, 조직을 관리하는 사용자에게 Organization Administrator IAM 역할을 부여
* Organization Administrator IAM 권한의 사용자가 갖는 책임
  * 리소스 계층의 구조를 정의
  * 리소스 계정의 IAM 정책을 정의
  * 다른 사용자에게 다른 관리 권한을 위임
* G Suite organization / Cloud Identity 계정의 멤버가 과금 계정이나 프로젝트를 생성할 때, GCP는 자동적으로 조직 리소스를 생성
* 조직이 생성될 때, 조직의 모든 사용자는 프로젝트 생성자와 과금 계정 생성자 역할을 부여

#### Folder(폴더)

* 다층적인 조직 계층의 빌딩 블록으로 조직은 폴터를 포함
* 폴더는 다른 폴더나 프로젝트를 포함할 수 있음
* 폴더 구성
  * 포함된 프로젝트의 리스소에서 제공되는 서비스의 종류
  * 폴더와 프로젝트를 관리하는 정책

#### Project(프로젝트)

* 리소스 생성, GCP 서비스 사용, 권한 관리, 과금 옵션 관리를 포함
* `resourcemanager.project.create` IAM 권한을 갖는 사용자는 누구나 프로젝트를 생성할 수 있음
  * 기본적으로 조직이 생성될 때, 도메인의 모든 사용자는 이 권한을 부여받음
* 조직은 생성할 수 있는 프로젝트의 할당량을 갖음
* 프로젝트 제한에 도달한 후, 다른 프로젝트를 생성한다면 할당량 증가 요청 메시지를 받을 것
* 리소스 계층을 생성한 후, 리소스를 제어하는 정책을 정의할 수 있음

### Organization Policies

* GCP는 Organization Policy Service를 제공
  * 조직의 리소스에 대한 접근을 제어
* IAM은 사용자가 role이 클라우드에서 지정된 동작을 수행할 수 있도록 사용 권한을 할당
* Organization Policy Service는 리소스를 사용할 수 있는 방법에 대한 제한을 지정
* IAM은 누가 할 수 있는지를 지정하고, Organization Policy Service는 무엇을 할 수 있는지 지정

#### 리소스 Constraints

* Constraint는 서비스의 제한
* List Constraint
  * 리소스에 허용되거나 허용되지 않는 값의 리스트
  * List Constraint의 유형
    * 특정 값의 모음을 허용
    * 특정 값의 모듬을 거부
    * 한 값과 그 값의 모든 child 값을 거부
    * 허용된 모든 값을 허용
    * 모든 값을 거부
* Boolean Constraint
  * true와 false를 평가하고, Constraint가 적용될지 아닐 지 결정
  * ex) VM에 시리얼 포트로 접근하는 것을 거부하고 싶다면
    * `constraints/compute.disableSerialPortAccess`를 True로 설정

#### Policy Evaluation

* Organization은 클라우드에서 데이터와 리소스를 보호하기 위한 고정적인 정책은 갖을 수 있음
  * 무엇을 할 수 있는지에 대한 제약을 정책으로 정의하고, 그 정책을 리소스 계층에서 Object에 연결
* 정책을 Organization에 연결하면, 아래에 있는 모든 folder와 project는해당 정책을 상속받음
* 정책은 상속되고, 하위 Object에 의해서 사용되지 않거나 재정의될 수 없음

### Project 관리

* 프로젝트 생성
  * Cloud Console - IAM & Admin - Manage Resource - Create Project
* 프로젝트를 생성할 때, 프로젝트의 남은 할당량이 표시
  * 추가적인 프로젝트가 필요한 경우 할당량 증가를 위해 Manage Quotas를 클릭

## Roles and Identities

### GCP에서 Roles

* `role`은 사용 권한의 집합
* 사용자에게 역할을 바인딩하여 사용자에게 부여
* Role의 유형
  * Primitive roles
    * Owner, Editor, Viewer를 포함
    * 대부분 리소스에 적용될 수 있는 기본적인 권한
    * 가능하면 predefined roles 대신 primitive roles를 사용
    * 사용자에게 항상 필요하지 않을 수 있는 넓은 범위의 사용권한을 부여
  * Predefined roles
    * 기능을 수행하는데 필요한 사용권한을 부여
    * `Principle of least privilege(최소 권한 원칙)`
      * 필요한 권한만 할당하고, 더 이상 할당하지 않는 관행
    * GCP 리소스에 세밀한 접근을 제공하고, GCP 제품에 한정
  * Custom roles
    * 클라우드 관리자가 자신의 role을 생성하고 관리
    * IAM에 정의된 사용권한을 사용하여 구성

## Service Account

* Identity(ID)는 사용자 개별적으로 할당
* 어플리케이션이나 VM이 사용자를 대신하여 수행하거나 사용자에게 권한이 없는 동작을 수행하는 것
  * ex) 어플리케이션이 데이터베이스에 접근하고, 사용자가 직접 데이터베이스 접근을 막는 것
* service account는 어플리케이션에 할당될 수 있음
  * 사용자에게 데이터베이스 접근 권한을 부여하지 않아도 사용자를 대신하여 쿼리를 실행
* 떄로는 Service account를 리소스로 다루고, 때로는 Identities로 취급
  * 접근권한을 부여할 때는 리소스로 취급
  * role을 지정할 때는 Identity로 취급
* 사용자가 관리하는 service account
  * 사용자는 프로젝트당 100개의 Service account를 생성할 수 있음
* 구글이 관리하는 service account
  * Compute Engine을 이용하는 프로젝트를 생성할 때, Compute Engine Service Account는 자동으로 생성
* 프로젝트 수준에서 Account 그룹이나 Service account 개별적으로 관리될 수 있음
* service account는 리소스가 생성될 때 자동적으로 생성
* 어플리케이션에 service account를 생성하려면, IAM & Admin 콘솔 - Service Account에서 생성

## Billing(과금)

* Billing API는 리소스 사용에 대해 어떻게 비용을 지불하는지 관리하는 방법을 제공

### Billing Accounts

* 리소스 사용에 대해 비용을 지불하는 방법에 대한 정보를 저장
* 하나 이상의 프로젝트와 연관
* 무료 서비스를 사용하지 않는다면, Billing Account를 반드시 가져야함
* Billing Account는 리소스 계층과 비슷한 구조를 갖음
* Cloud Console - Billing 에서 Billing Account 리스트 조회 및 생성
* Billing Account의 유형
  * Self-service
    * 신용카드나 은행 계좌로부터 직접 인출하여 지불
  * Invoiced
    * 사용자에게 영수증이나 인보이스를 전송
* Billing과 관련된 role
  * Billing Account Creator
    * 새로운 Self-service billing account 생성
    * 재무 역할을 하는 담당자
  * Billing Account Administrator
    * Billing Account를 관리하지만, 생성할 수 없음
    * 클라우드 어드민
  * Billing Account User
    * 사용자가 프로젝트를 Billing Account에 연결할 수 있음
    * 프로젝트를 생성할 수 있는 모든 사용자
  * Billing Account Viewer
    * 사용자가 Billing Account 비용과 트랜잭션을 확인할 수 있음
    * 감사 담당자와 같은 사용자

### Billing Budget and Alerts

* 예산을 정의하고 과금 알림을 설정하는 옵션
* Cloud Console - Billing - Budget & Alerts
* Budget은 프로젝트가 아닌 Billing Account에 연관된다는 점을 기억
  * Billing Account와 연결된 모든 프로젝트에서 쓰여질 것으로 예측되는 금액을 기준을 해야함
* Budget에서 50%, 90%, 100%가 기본적으로 설정됨
  * Set Budget Alerts에서 임계값을 추가할 수 있음
* 임계 값을 넘었을 경우, Billing 관리자와 사용자는 이메일로 알림을 받을 것
  * 프로그램 방식으로 알림을 받고싶다면, Manage Notification에서 선택

### Exporting Billing Data

* 과금 데이터는 추후 분석을 위해 BigQuery 데이터베이스나 Cloud Storage 파일로 추출할 수 있음
* Cloud Console - Billing - Billing Export
  * BigQuery Export / File Export 중 선택
  * Edit Setting에서 프로젝트 선택

## Enabling API

* 프로그램 방식으로 접근이 가능한 서비스인 API를 사용
* 모든 GCP 서비스는 관련된 API를 갖고 있으나 default로 사용하지 않음
* Cloud Console - APIs & Service에서 확인 

## Provisioning Stackdriver Workspace

* 어플리케이션과 리소스의 모니터링, 로깅, 추적, 디버깅을 위한 서비스
* 모니터링과 로깅 데이터를 저장하기 위해 Workspace를 생성해야 함
* Cloud Console - Stackdriver - 프로젝트 선택 - Create Workspace

[맨 위로](#chapter-3-project-service-accounts-billing)

[본문 보기](../Chapter_3.md)