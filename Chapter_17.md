# Chapter 17 접근 및 보안 구성

**이 챕터는 구글 Associate Cloud Engineer 인증 시험 과목 중, 아래 내용을 다룬다.**
* 5.1 Identity and Access Management(IAM) 관리
* 5.2 service accounts 관리
* 5.3 프로젝트와 관리 서비스를 위한 감시로그 조회

구글 클라우드 엔지니어는 접근 제어 작업에 상당한 양의 시간을 소비할 수 있다. 이 챕터는 IAM 할당 관리, 커스텀 roles 생성, service account 관리, 감시 로그 조회를 포함하여 몇가지 일반적인 작업을 수행하는 방법에 대한 지침을 제공한다.

사용자, 그룹, 서비스계정에 수행 권한을 할당하는 기본 방법은 IAM 시스템을 통하는 것이다. 그러나, 구글 클라우드에 항상 IAM이 있는 것은 아니다. 그 전에, 수행 권한은 primitive roles로 알려진 것을 사용하여 할당 받으며, 상당히 coarse-grained(큰 단위로 수행)이다. Primitive roles은 사용자가 원하는 것보다 더 많은 수행 권한이 있을 수 있다. scopes를 사용하여 수행 권한을 제한할 수 있다. 이 챕터에서는 primitive roles과 scopes뿐만 아니라 IAM을 사용하는 방법을 설명한다. 앞으로, 접근 제어를 위한 IAM를 사용하는 것이 가장 좋다.

## IAM 관리

IAM을 작업할 때, 수행해야할 몇 가지 공통 업무가 있다.
* IAM 할당 계정 조회
* IAM roles 할당
* 커스텀 roles 정의

각 업무를 수행하는 방법을 살펴보자

### IAM 할당 계정 조회

Cloud Console에서 IAM & Admin 섹션을 열어서 IAM 할당 계정을 조회할 수 있다. 이 섹션에서, 메뉴에서 IAM을 선택하면 그림 17.1과 같은 양식이 표시된다. 그림의 예시는 member 이름으로 필터링된 ID의 리스트를 보여준다.

이 예시에서, dan@gcpcert.com 사용자는 3개의 roles을 갖고 있다: App Engine Admin, BigQuery Admin, Owner. App Engine Admin과 BigQuery Admin은 사전 정의된 IAM roles이다. Owner는 primitive role이다.

![17.1](./img/ch17/17.1.png)

**그림 17.1** member로 필터링된 수행관한 리스트