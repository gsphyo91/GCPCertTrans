# Official Google Cloud Certified Associate Cloud Engineer Study Guide

# Contents
[TOC]

---

# 소개
구글 클라우드 플랫폼(GCP)는 구글에서 제공하는 동일한 소프트웨어, 하드웨어, 네트워킹 인프라를 사용자에게 제공하는 퍼블릭 클라우드다. 사업, 기업, 개인은 몇 분안에 서버를 설치하고, 페타바이트의 데이터를 저장하고, GCP에 글로벌 가상 클라우드를 구현할 수 있다. GCP에는 쉽게 사용할 수 있는 콘솔, CLI 툴, 클라우드에서 리소스를 관리하기 위한 API를 포함하고 있다. 사용자는 가상머신(VM), 디스크와 같은 일반적인 리소스를 사용하거나 IoT를 위한 서비스, 머신러닝, 미디어, 다른 특정 도메인에 특화된 서비스를 선택할 수 있다.

GCP에서 어플리케이션과 서비스를 배포하고 관리하는 것은 구글이 사용자 계정을 구성하고, identities와 접근 제어를 관리하는 방법에 명확한 이해가 필요하다. 또한 다양한 서비스의 장단점을 이해해야 한다. Associate Cloud Engineer 인증은 구글 클라우드에서 인프라, 서비스, 네트워크를 배포하고 운영하는 데에 필요한 지식과 스킬을 증명한다.

이 스터디 가이드는 Google Cloud에서 동작하는 리소스들의 필요를 충족시키기위한 GCP를 자세하게 이해하는데 도움이 되도록 설계되었다. 물론 이 책은 Associate Cloud Engineer 인증 시험을 통과하는데 도움을 주지만, 벼락치기를 위한 가이드는 아니다. 당신은 시험에 통과하기 위해 필요한 것보다 더 많이 배울 것이다. 서비스 선택, 사용자 관리, 배포, 모니터링, 클라우드 기반 솔루션에서 사업 요구사항 매핑과 같이 클라우드 엔지니어가 직면하는 이슈를 해결하는 방법을 배울 것이다.

이 책의 각 챕터는 하나의 주제를 다루고, 인증 시험을 통과하기위해 알아야하는 핵심 정보를 설명하는 "Exam Essentials" 섹션을 포함한다. 그리고 복습을 도와주고, 각 챕터의 이해력을 강화하기위한 연습을 포함한다. 시험에서 볼 수도 있는 질문의 유형 감각을 얻기 위한 샘플 문제는 각 챕터의 마지막에 포함되어있다. 이 책은 또한 쉽게 이해할 수 있도록 만든 카드(flashcard)와 가이드를 통해 배우는 모든 토픽에 대한 연습 문제를 포함하고 있다.

[맨 위로](#Contents) 

## 이 책에서 다루는 것

이 책은 GCP의 제품과 서비스를 설명한다. G Suite 관리자 주제는 포함하지 않는다.

**Chapter 1 : 구글 클라우드 플랫폼 Overview**
이 챕터에서는 compute, storage, 머신러닝 상품과 같은 networking 서비스를 포함하여 GCP에서 제공하는 서비스들의 유형을 확인한다. 이 챕터에서는 또한 클라우드 컴퓨팅과 데이터 센터 혹은 on-premise 컴퓨팅의 핵심 차이에 대해 설명한다.

**Chapter 2 : 구글 클라우드 컴퓨팅 서비스** 이 챕터에서는 computing, storage, networking과 같은 인프라스트럭쳐 서비스에 대한 기초를 제공한다. ID 관리와 관련 서비스의 컨셉도 소개한다. 또한 DevOps 주제와 어플리케이션과 리소스를 배포하고 모니터링하기 위한 도구들을 소개한다.GCP는 머신러닝과 자연어 처리 서비스와 같은 특화 서비스의 리스트를 포함한다. 이 챕터에서는 특화 서비스들을 간단하게 다룬다. 이 챕터는 regions과 zones을 살펴보면서 구글 클라우드의 조직 구조를 소개한다. 이 챕터에는 패키지된 어플리케이션을 배포하기 위한 클라우드 런쳐의 논의 내용이 포함되어있다.

**Chapter 3 : 프로젝트, 서비스계정, 빌링** GCP와 작업하기 시작할 때 해야할 일 중에 하나는 계정을 세팅하는 것이다. 이 챕터에서, 조직, 폴더, 프로젝트에서 계정별 리소스를 구성하는 방법에 대해 배울 것이다. 이 구조를 생성하고 수정하는 방법을 배울 것이다. 특정 프로젝트를 위한 API 활성화 뿐만 아니라 특정 프로젝트의 접근 제어와 사용자 ID를 관리하는 방법에 대해 배울 것이다. 이 챕터는 빌링 계정을 생성하고 프로젝트에 연결하는 방법에 대해 설명한다. 또한 예산을 생성하고, 비용환리를 도와주기위한 빌링 알림을 정의하는 방법에 대해 배울 것이다. 마지막으로 이 챕터는 GCP의 모니터링 시스템중 하나로 사용되는 Stackdriver 계정을 생성하는 방법에 대해 설명한다.

**Chapter 4 : 구글 클라우드 컴퓨팅 소개** 이 챕터에서는 GCP에서 어플리케이션과 서비스가 동작하기 위해 이용할 수 있는 다양한 옵션들을 확인할 것이다. 옵션들은 리눅스나 윈도우OS에서 동작하는 VM을 제공하는 Compute Engine을 포함한다. App Engine은 개발자들이 스스로 VM을 관리해야하는 걱정없이 그들의 어플리케이션을 실행할 수 있는 Platform as a service(PaaS) 옵션이다. 다수의 어플리케이션과 서비스를 실행할 것이라면, VM을 대체할 수 있는 가벼운 컨테이너를 원할지도 모른다. 이 챕터에서는 컨테이너와 이를 관리하는 Kubernetes Engine에 대해서 배울 것이다. 또한 Cloud Storage에서 로그되는 이미지의 처리 동작과 같이 이벤트 중심의 짧은 실행 작업을 위한 Cloud Functions를 소개한다. 또한 모바일 어플리케이션의 백엔드 인프라를 제공하는 데 적합한 서비스인 Firebase에 대해서 배울 것이다.

**Chapter 5 : Compute Engine 가상 머신의 Computing** 이 챕터에서는 CPU, 메모리, 저장소 옵션, OS 이미지를 선택하는 것을 포함한 VM 설정 방법에 대해 배울 것이다. VM을 동작하기 위한 GCP 콘솔과 Cloud Shell을 사용하는 방법을 배울 것이다. 게다가 VM을 기동하거나 정지할 때 사용할 CLI와 SDK를 설치하는 방법을 확인할 것이다. 이 챕터는 또한 VM으로 네트워크 접속을 허용하는 방법에 대해 설명한다.

**Chapter 6 : 가상머신 관리** 이전 챕터에서 VM을 생성하는 방법을 배웠고, 이 챕터에서는 VM 단위와 그룹별로 관리하는 방법을 배울 것이다. GCP 콘솔을 사용하여 VM 하나의 인스턴으를 관리하는 것으로 시작할 것이다. 그리고 Cloud Shell과 CLI를 사용하여 같은 동작을 수행할 것이다. 다음으로, 하나의 단위로 관리할 수 있는 VM 집합을 생성하도록 허용한 인스턴스 그룹에 대해서 배울 것이다. 인스턴스 그룹 섹션에서 관리된/관리되지 않은 인스턴스 그룹의 차이를 배울것이다. 또한 구글에 의해서 정지될 수도 있는 저비용의 VM인 Preemptible instances를 배울 것이다. preemptible 인스턴스의 비용 절충에 대해서 배울 것이다. 마지막으로, 이 챕터는 VM 관리를 위한 가이드라인으로 마무리된다.

**Chapter 7 : Kubernetes 컴퓨팅** 이 챕터는 구글에서 관리하는 Kubernetes service인 Kubernetes Engine을 소개한다. Kubernetes는 구글에 의해서 생성되고, 오픈소스로 릴리즈된 컨테이너 오케스트레이션 플랫폼이다. 이 챕터에서는 컨테이너, 컨테이너 오케스트레이션, Kubernetes 아키텍처의 기초에 대해서 배울 것이다. Pods, Services, Volumes, Namespace와 같은 Kubernetes 오브젝트 뿐만 아니라, ReplicaSets, deployments, jobs과 같은 Kubernetes 컨트롤러의 소개를 포함한다.
다음으로, 이 챕터는 GCP 콘솔, Cloud Shell, SDK를 사용하여 Kubernetes 클러스터를 배포하는 것을 배운다. 또한 존재하는 Docker 이미지 다운로드, Dokcer 이미지 빌드, pod 생성과 같이 Pods를 배포하는 방법과 Kubernetes 클러스터에 어플리케이션을 배포하는 것에 대해 학습한다. 서버의 클러스터를 확인하는 방법에 대해 알 필요가 있다. 이 챕터에서는 구글의 어플리케이션, 서비스, 컨테이너, 인프라 모니터링 서비스인 스택드라이버를 이용하여 모니터링과 로깅을 세팅하는 방법에 대한 설명을 제공한다.

**Chapter 8 Kubernetes 클러스터 관리** 이 챕터에서는 클러스터의 상태 확인, 이미지 저장소의 컨텐츠 확인, 저장소에 저장된 이미지의 세부정보 확인, node/pod/service의 추가/수정/삭제에 대한 Kubernetes 클러스터 관리의 기초에 대해서 배울 것이다. VM 관리 챕터처럼 이 챕터에서는 3가지 관리도구(GCP 콘솔, Cloud Shell, SDK)를 이용하여 관리 동작을 수행하는 방법을 배울 것이다. 이 챕터는 Kubernetes 클러스터 관리를 위한 좋은 예시와 가이드라인에 대한 논의로 마무리한다.

**Chapter 9 App Engine 컴퓨팅** 구글 App Engine은 구글의 PaaS 서비스다. 어플리케이션, 서비스, 버전, 인스턴스와 같은 App Engine 컴포넌트에 대해 배울 것이다. 이 챕터는 또한 설정 파일을 정의하고, 어플리케이션의 dependencies를 지정하는 방법을 다룬다. 이 챕터에서는 GCP 콘솔, Cloud Shell, and SDK를 사용하여 App Engine 리소스를 확인하는 방법을 배울 것이다. 또한 분산 매개변수로 트래픽을 조정하여 워크로드를 분산하는 방법을 설명한다. 또한 App Engine의 오토 스케일링에 대해서 배울 것이다.

**Chapter 10 Cloud Functions 컴퓨팅** Cloud Functions은 이벤트 기반을 위한 서버리스 연산이다. 이 챕터에서는 Cloud Functions를 사용하여 이벤트를 수신하고, 서비스를 실행하고, 결과를 리턴하는 것을 소개한다. 다음으로 3rd party API 통합, 이벤트 기반 처리와 같은 Cloud Functions의 사용 사례를 확인할 것이다. publications, subscription 기반의 처리를 위한 구글의 Pub/Sub 서비스와 Pub/Sub과 함께 Cloud Functions을 사용하는 방법에 대해 배울 것이다. Cloud Function은 Cloud Storage에서 이벤트를 응답하는데 잘 맞는다. 이 챕터에서는 Cloud Storage 이벤트와 이 이벤트를 수신하고 응답하기 위한 Cloud Functions의 사용 방법에 대해 설명한다. Cloud functions 실행의 상세 로그와 모니터링을 위한 Stackdriver 사용 방법을 배울 것이다. 마지막으로 이 챕터는 Cloud Functions 사용과 관리를 위한 가이드라인으로 마무리한다.

**Chapter 11 클라우드에서 storage 계획** GCP의 다양한 컴퓨팅 옵션에 대해서 설명했고, 이제는 Storage로 변경할 때이다. 이 챕터는 접속, 지속 시간, 데이터 모델과 같은 storage 시스템의 특정을 설명한다. 이 챕터에서는 캐쉬, persistent storage, archival storage의 차이에 대해 배울 것이다. regional과 multi regional persistent storage와 nearline vs coldline archival storage를 사용하는 것의 비용 절감 효과에 대해서 배울 것이다. 이 챕터에는 blob storage를 위한 Cloud Storage, 관계형 저장소 옵션인 Cloud SQL과 Spanner, NoSQL 저장소를 위한 Datastore, Bigtable, BigQuery 옵션에 해당하는 다양한 GCP 저장소 옵션에 대한 상세정보를 포함한다. 이 챕터는 지속성, 이용가능성, 트랜잭션 지원, 비용, 지연, 다른 read/write 패턴을 위한 지원의 요구사항을 기반에 기반한 데이터 저장소를 선택하는 자세한 가이드라인을 포함한다.

**Chapter 12 : 구글 클라우드 플랫폼에 저장소 배포** 이 챕터에서는 GCP의 스토리지 시스템의 각각에서 데이터베이스를 생성하고, 데이터를 추가하고, 레코드를 나열하고, 데이터를 삭제하는 방법을 배울 것이다. 이 챕터는 MySQL과 PostgreSQL을 제공하는 관리형 데이터베이스 서비스인 Cloud SQL을 소개하는 것으로 시작한다. Cloud Datastore, BigQuery, Bigtable, Spanner에 데이터베이스를 생성하는 방법을 배울 것이다. 다음으로, 메시지 큐에 데이터를 저장할 수 있는 Cloud Pub/Sub을 배우고, 큰 데이터 셋을 처리하기 위한 Hadoop, Spark 클러스터 서비스과 같은 Cloud Dataproc에 대해 논한다. 다음 섹션에서는 오브젝트를 위한 Cloud Storage에 대해 배울 것이다. 이 챕터는 특정 요구사항 집합을 위한 데이터 저장소를 선택하는 방법에 대한 가이드로 마무리한다.

**Chapter 13 : 스토리지에서 데이터 불러오기** GCP에서 데이터를 얻는 방법은 다양하다. 이 챕터는 Cloud SQL, Cloud Storage, Datastore, BigQuery, BigTable, Dataproc에서 데이터를 로드하기 위해 CLI SDK를 사용하는 방법에 대해 설명한다. 또한 같은 서비스에 대해 벌크로 데이터를 적재하거나 추출하는 것을 설명한다. 다음으로, Cloud Storage로부터 데이터를 옮기고, Cloud Pub/Sub으로 데이터를 스트리밍하는 2가지 공통 데이터 로딩 패턴에 대해 배울 것이다.

**Chapter 14 : 클라우드 네트워킹: Virtual Private Clouds와 Virtual Private Networks** 이 챕터에서는 아래 내용같은 기본 네트워킹 컨셉의 소개와 함께 네트워킹을 배울 것이다.
* IP 주소
* CIDR blocks
* Network와 subnetworks
* Virtual Private Clouds (VPCs)
* Routing과 rules
* Virtual Private Network (VPNs)
* Cloud DNS
* Cloud routers
* Cloud interconnect
* External peering
네트워킹 컨셉의 핵심이 소개된 후에, VPC를 생성하는 방법에 대해 배울 것이다. 특히, VPC 정의, 방화벽 정책 지정, VPN 생성, 로드 밸런서 동작이 포함될 것이다. 로드밸런서의 다른 유형과 이를 사용하는 시기에 대해서 배울 것이다.

**Chapter 15 : 클라우드 네트워킹 : DNS, 로드 밸런싱, IP할당** 이 챕터에서는 서브넷 정의, VPN 서브넷 추가, CIDR 블럭 관리, IP 주소 확보와 같은 공통 네트워크 관리 업무에 대해서 배울 것이다. Cloud 콘솔, Cloud Shell, Cloud SDK를 사용하여 각 업무를 수행하는 방법에 대해 배울 것이다.

**Chapter 16 : Cloud Launcher와 배포 매니저로 어플리케이션 배포** 구글 클라우드 런처는 미리 설정된 stacks과 서비스의 마켓플레이스다. 이 챕터에서는 Cloud Launcher를 소개하고, 몇몇 어플리케이션과 현재 이용가능한 서비스를 설명한다. Cloud Launcher을 통해서 탐색하고, 어플리케이션을 배포하고, 어플리케이션을 중지하는 방법을 배울 것이다. 또한 이 챕터에서는 어플리케이션의 배포를 자동화하는 Deployment Manager 템플릿, GCP 리소스 할당을 위한 템플릿, 자동적으로 어플리케이션 설정하는 것을 논의한다.

**Chapter 17 : 접근과 보안 설정** 이 챕터에서는 ID 관리를 소개한다. 특히, ID, roles, 권한 할당과 제거를 배울 것이다. 또한 이 챕터에서는 서비스 계정을 소개하고, 계정을 생성하고, 계정에 VM을 할당하고, 프로젝트를 동작하는 방법을 소개한다. 그리고 프로젝트와 서비스를 위한 감시 로그를 확인하는 바업을 배울 것이다. 이 챕터는 접근 제어 보안 설정을 위한 가이드라인으로 마무리한다.

**Chapter 18 : 모니터링, 로딩, 비용 예측** 마지막 챕터에서는 Stackdriver 알림, 로그, 분산 추적, 어플리케이션 디버깅을 논한다. GCP 서비스에 해당하는 것들은 더 효과적이고, 기능적이고, 신뢰적인 서비스로 설계되었다. 이 챕터는 GCP에서 리소스의 비용을 예측하기위해 도움이 되는 비용 계산의 리뷰로 마무리한다.

[맨 위로](#Contents) 

## 시험 과목

Associate Cloud Engineer 인증은 GCP에 기업 어플리케이션과 인프라를 생성하고, 배포하고, 관리하는 사람을 위해 설계되었다. Associate Cloud Engineer는 Cloud Console, Cloud Shell, Cloud SDK와 동작하기 적합하다. 또한 GCP의 한 부분으로 제공되는 제품과 적합한 사용사례를 이해한다.
이 시험은 아래 내용에 대해서 당신의 지식을 테스트한다.
* 하나이상의 GCP 서비스를 사용하여 클라우드 솔루션을 기획하는 것
* 조직을 위한 클라우드 환경을 생성하는 것
* 어플리케이션과 인프라를 배포하는 것
* 클라우드 솔루션의 이용가능성을 보증하기위해 모니터링과 로깅을 사용하는 것
* ID 관리, 접근 제어, 다른 보안 감지를 세팅하는 것

### 과목 map

아래는 [https://cloud.google.com/certification/guides/cloud-engineer](https://cloud.google.com/certification/guides/cloud-engineer) 에서 구글이 정의한 과목이 명시한다.

**Section 1 : 클라우드 솔루션 환경 세팅**
**1.1 클라우드 프로젝트와 계정 세팅**
* 프로젝트 생성
* 프로젝트 내에서 사전 정의된 IAM(Identity and Access Management)룰을 사용자에게 할당하는 것
* G suite ID에 유저를 연결하는 것
* 프로젝트 내에서 API를 활성화하는 것
* 하나 이상의 Stackdriver 계정을 프로비저닝 하는 것
**1.2 과금 설정 관리**
* 하나이상의 과금 계정을 생성하는 것
* 빌링 계정을 프로젝트에 연결하는 것
* 과금 예산과 알림을 설정하는 것
* 일/월 단위 비용을 측청하기위한 과금 내보내기 설정하는 것
**1.3 command-line interface(CLI), 특히 Cloud SDK를 설치하고 설정**

**Section 2 : 클라우드 솔루션을 기획하고 설정**
**2.1 비용 계산기를 사용하여 GCP 제품 사용을 기획하고 측정하는 것**
**2.2 컴퓨트 자원을 기획하고 설정하는 것**
* 주어진 워크로드를 위해 적합한 compute를 선택하는 것(ex. Compute Engine, Kubernetes Engine, App Engine)
* 미리 선점된 VM과 커스텀 머신을 사용하는 것
**2.3 데이터 스토리지 옵션을 기획하고 설정**
* 제품 선택(ex. Cloud SQL, BigQuery, Cloud Spanner, Cloud Bigtable)
* 스토리지 옵션 선택(ex. Reginal, Multiregional, Nearline, Coldline)
**2.4 네트워크 리소스를 기획하고 설정**
* 로드밸런싱 옵션을 구별하는 것
* 이용가능성을 위해 네트워크에서 리소스 위치를 지정하는 것
* Cloud DNS 설정하는 것

**Section 3 : 클라우스 솔루션을 배포하고 구현**
**3.1 Compute Engine 리소스를 배포하고 구현하는 것**
* Cloud Console과 Cloud SDK(gcloud)를 사용하여 compute instance를 실행하는 것(ex. assign disk, availability policy, SSH keys)
* 인스턴스 템플릿을 사용하여 오토스케일 관리 인스턴스 그룹을 생성하는 것
* 인스턴스를 위한 커스텀 SSH 키를 생성하고 업로드하는 것
* Stackdriver 모니터링과 로깅을 위한 VM을 설정하는 것
* compute 할당량 평가와 증설 요청
* 모니터링과 로깅을 위한 Stackdriver 에이전트를 설치하는 것
**3.2 Kubernetes Engine 리소스를 배포하고 구현**
* Kubernetes Enging 클러스터 배포하기
* pod를 사용하여 Kubernetes Engine에 container 어플리케이션을 배포하는 것
* Kubernetes Engine 어플리케이션 모니터링과 로깅 설정하는 것
**3.3 App Engine과 Cloud Functions 리소스를 배포하고 구현**
* App Engine에 어플리케이션 배포하는 것(ex. scaling configuration, versions, traffic splitting)
* Google Cloud 이벤트를 받는 Cloud Functions 배포하는 것(ex. Cloud Pub/Sub 이벤트, Cloud Storage object 변경 알림 이벤트)
**3.4 데이터 솔루션을 배포하고 구현**
* 데이터 시스템 제품 초기화(ex. Cloud SQL, Cloud Datastore, BigQuery, Cloud Spanner, Cloud Pub/Sub, Cloud Bigtable, Cloud Dataproc, Cloud Storage)
* 데이터 로딩(ex. command-line upload, API transfer, import/export, Cloud Storage에서 데이터 로드, Cloud Pub/Sub에서 데이터 스트리밍)
**3.5 네트워킹 리소스를 배포하고 구현**
* 서브넷과 함께 VPC 생성(ex. custom-mode VPC, shared VPC)
* 커스텀 네트워크 설정과 함께 Compute Engine 인스턴스 실행(ex. internal-only IP address, Google private access, static external, private IP address, network tags)
* VPC를 위한 ingress, egress 방화벽 정책 생성(ex. IP subnet, tags, service accounts)
* Cloud VPN을 사용하여 Google VPC와 외부 네트워크간 VPN 생성
* 어플리케이션으로 들어오는 네트워크 트래픽을 분산하기위한 로드밸런서 생성(ex. global HTTP(S) load balancer, global SSL proxy load balancer, global TCP proxy load balancer, regional network load balancer, regional internal load balancer)
**3.6 Cloud Launcher를 사용하여 솔루션 배포**
* Cloud Launcher 카탈로그를 탐색하고 솔루션 정보를 확인
* Cloud Launcher 마켓플레이스 솔루션 배포
**3.7 Deployment Manager를 사용하여 어플리케이션 배포**
* 어플리케이션의 배포를 자동화하는 Deployment Manager 템플릿 배포
* GCP 리소스를 할당하고 어플리케이션을 자동적으로 설정하는 Deployment Manager 템플릿 배포

**Section 4 : 클라우드 솔루션의 성동적인 운영 보증**
**4.1 Compute Engine 리소스 관리**

[맨 위로](#Contents) 