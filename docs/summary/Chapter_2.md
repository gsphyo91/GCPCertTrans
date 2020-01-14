# Chapter 2 Google Cloud Computing Services

## GCP의 Computing Component

* GCP에서 제공하는 서비스는 다음과 같은 카테고리로 그룹화
  * Computing Resource
  * Storage Resource
  * Databases
  * Networking Services
  * Identity Management and Security
  * Development tools
  * Management tools
  * Specialized Services

### Computing Resource

* 퍼블릭 클라우드 사용자는 스스로 VM을 생성하고 관리할 수 있음
* Infrastructure as a service(IaaS)
  * 운영체제, 설치할 패키지, 백업시기, 다른 유지보수 작업을 선택할 수 있음
  * Compute Engine
* Platform as a Service(PaaS)
  * 서버, 네트워크, 스토리지 시스템을 관리할 필요없이 어플리케이션을 실행하는 런타임 환경 제공
  * App Engine, Cloud Functions
* Container 관리를 위한 Kubernetes Engine 제공

#### Compute Engine

* VM을 생성하고, VM에 Persistent Disk를 추가하고, Cloud Storage와 같은 GCP의 다른 서비스를 사용할 수 있는 서비스
* VM
  * 물리서버의 추상화
  * `하이퍼바이저`라고 불리는 low-level 서비스에서 동작
    * GCP의 VM은 KVM 하이퍼바이저를 사용
  * 하이퍼바이저는 리눅스나 윈도우 서버같은 OS 위에서 동작
  * Guest OS라고 불리는 다양한 OS를 실행하는 동시에 다른 Guest OS로부터 독림시킬 수 있음
* VM을 생성할 때 미리 정의된 사이즈를 선택할 수 있지만, 커스텀으로 구성할 수 있음
  * 운영체제
  * Persistent Storage 사이즈
  * GPU 추가
  * Preemptible VM 생성
    * 일반적인 VM보다 80%정도 비용을 절약할 수 있음
    * 구글에 의해서 언제든지 종료될 수 있음

#### Kubernetes Engine

* 사용자가 서버의 Cluster에서 컨테이너화된 어플리케이션을 쉽게 실행할 수 있도록 설계
* Container
  * 하이퍼바이저가 필요하지 않음
  * Container Manager 위에서 실행되는 Guest OS가 없음
  * Container는 Host OS 기능을 사용하는 동시에 OS와 Container Manager는 동작 중인 Container간 독립을 보장
* GCP의 Kubernetes Engine은 기본적인 리소스를 할당
* Kubernetes는 Cluster에 있는 서버의 상태를 모니터링하고, 서버의 에러와 같은 문제를 자동적으로 해결함
* 어플리케이션의 부하가 증가하면 추가적인 리소스를 할당하는 Auto Scailing을 지원

#### App Engine

* GCP의 Compute PaaS 제품
* 개발자와 관리자는 VM 구성이나 Kubernetes 클러스터 설정에 대해 관려할 필요가 없음
  * 개발자는 어플리케이션을 만들고, Serverless 환경에 코드를 배포
* App Engine은 기본적인 Computing과 네트워크 인프라를 관리
* App Engine의 2가지 유형
  1. Standard
     * 특정 언어별 샌드박스에서 어플리케이션을 실행
     * 어플리케이션 코드와 함께 설치되어야하는 소프트웨어와 운영체제 패키지가 필요하지 않음
  2. Flesxible
     * App Engine 환경에서 Docker Container를 실행
     * Library나 3rd Party 소프트웨어가 필요한 경우 잘 동작

#### Cloud Functions

* 이벤트 중심 프로세싱에 적합한 Computing 옵션
  * Cloud Storage에 파일이 업로드 되거나 메시지 큐에 메시지가 쓰여지는 것
* 실행되는 코드는 짧게 동작해야 하고, 오래 동작하는 코드를 위해 설계되지 않음
* 3rd Party API나 자연어 번역과 같은 GCP 서비스를 호출하는데 자주 사용
* 부하에 따라서 자동적으로 확장됨

## GCP의 Storage Component

### Storage Resource

#### Cloud Storage

* GCP의 Object Storage 시스템
* Object는 File이나 Binary Object일 수 있음
* File 시스템에서의 Directory와 유사한 Bucket에 구성됨
* Cloud Storage가 File 시스템이 아니라는 것을 기억해야 함
* VM이나 Persistent Disk의 파일 시스템에서 접근할 수 있음
* 저장된 Object는 URL에 의해서 유니크한 주소를 갖고 있음
* IAM을 통해서 Bucket을 읽고 쓰는 권한을 설정할 수 있음
* Cloud Storage는 단일 데이터를 다루는 Object를 저장하는데 유용
  * ex) Image
  * 갑작스럽게 Object를 쓰거나 조회하고, 서버와 독립적으로 저장되어야 한다면 유용
* Cloud Storage의 Class
  1. Regional Storage
     * 구글 클라우드 하나의 `Region`에 Object 사본을 저장
     * 같은 Region에서 동작하고, Object로 접근하는데 Latency가 낮아야하는 어플리케이션에 적합
  2. Multi-Regional
     * 여러 Region에 Object의 복사본을 저장하는 것을 제공
     * 높은 유용성, 지속성, 낮은 latency를 위해 중요
  3. Nearline
     * 데이터는 가끔 접근하지만, 장기간 유지되어야할 필요가 있을 때
     * Regional이나 Multi-Regional보다 비용이 적음
  4. Coldline
     * 높은 지속성과 빈번하지 않은 접근을 위해 설계된 저비용 저장소
     * 1년에 한 번도 접근을 하지 않는 데이터를 위해 적합
* Lifecycle 관리 정책
  * 사전에 정의한 정책을 기반으로 자동적으로 Object를 관리
  * ex) Bucket에서 60일이 지난 Object를 Nearline Storage로 이동
* Region
  * 여러 `zone`이나 배포 영역을 갖을 수 있는 지리적인 영역

#### Persistent Disk

* Compute Engine이나 Kubernetes Engine에 추가되는 Storage 서비스
* SSD나 HDD 위에 Block Storage를 제공
  * SSD는 성능이 중요한 저지연의 어플리케이션에서 사용
  * SSD는 HDD보다 가격이 비쌈
  * 더 많은 시간을 허용할 수 있다면, HDD를 사용
* 장점
  * 성능 저하없이 다양한 Client가 disk를 읽을 수 있음
  * 여러 인스턴스가 단일 데이터의 사본을 읽을 수 있음
  * VM 재기동 없이 필요 사이즈를 조정할 수 있음
* Persistent Disk는 SSD나 HDD를 사용하여 64TB까지 증가 가능

#### Cloud Filestore

* Compute Engine과 Kubernetes Engine과 함께 사용할 수 있는 공유파일시스템
* 높은 수의 초당 I/O 동작뿐만 아니라 다양한 Storage 용량을 제공
* 네트워크 파일 시스템(NFS) 프로토콜로 구현되어서 VM에 쉽게 마운트

### Database

* GCP 사용자는 서비스를 선택하기 전에 어플리케이션의 요구사항에 대해서 이해해야함

#### Cloud SQL

* 데이터베이스 백업이나 패치를 할 필요 없이 VM에 MySQL이나 PostgreSQL을 설치할 수 있는 GCP 서비스
* replication 관리를 포함하고, 가용성이 높은 데이터베이스를 위해 자동으로 failover를 제공
* 관계형 데이터베이스는 비교적 일관된 데이터 구조의 어플리케이션에 적합

#### Cloud Bigtable

* 10억 개의 행과 1000개의 열까지 관리할 수 있는 petabyte급 어플리케이션을 위해 설계
* `wide-column data model`로 알려진 NoSQL 기반
* 저지연으로 읽고 쓰는 동작이 필요한 어플리케이션에 적합
* 초당 100만 개의 동작을 지원하도록 설계
* Hadoop에서 데이터 접근을 위한 API인 Hbase API를 지원

#### Cloud Spanner

* NoSQL 데이터베이스처럼 수평적인 확장 기능과 함께 높은 일관성과 트랜잭션과 같은 관계형 데이터베이스의 핵심 장점을 조합한 구글의 글로벌 분산 관계형 데이터베이스
* 99.999%의 SLA를 갖춘 고가용성 데이터베이스로 확장이 가능
* ID 기반의 접근제어와 함께 유휴상태나 전송 중에서 암호화를 제공
* ANSI 2011 표준 SQL을 지원

#### Cloud Datastore

* NoSQL Document 데이터베이스
  * document 컨셉이나 key-value 쌍의 collection을 기본 구성 블록으로 사용
    * document는 유연한 스키마를 허용
  * 포함될 수도 있는 key들은 document 데이터베이스를 사용하기 전에 정의할 필요가 없음
* 어플리케이션 설계 당시에 알려지지 않은 속성들을 수용하는데 유용
* Compute Engine, Kubernetes Engine, App Engine에서 동작하는 어플리케이션에서 REST API를 통해 접근
* 로드에 따라서 자동적으로 확장
* 성능을 유지하는데 필요한 데이터를 `shard`나 파티셔닝
* replication, backup과 같이 다른 데이터베이스처럼 관리형 서비스
* 고가용성과 구조화 데이터를 요구하는 어플리케이션이 적합
* 데이터를 읽을 때 항상 높은 일관성을 필요로하지 않음

#### Cloud Memorystore

* in-memory caches 서비스
* 메모리에서 자주 사용되는 데이터를 캐시하기 위한 Redis 서비스
* ms 미만의 데이터 접근을 제공하도록 설계
* 사용자가 cache의 사이즈를 지정할 수 있음
* 고가용성, 패치, 자동 failover를 보장

#### Cloud Filestore

* 확장성이 뛰어난 웹과 모바일 어플리케이션을 위해 백엔드로 설계된 NoSQL 데이터베이스 서비스
* 차별화 기능
  * 오프라인 지원
  * 동기화
  * 모바일, IoT 디바이스 데이터를 관리하기 위한 기능
  * 백엔드 데이터 저장소를 제공하는 클라리언트 라이브러리

## GCP의 Networking Component

### Networking Service

#### Virtual Private Cloud(VPC)

* 퍼블릭 클라우드 인프라를 사용할지라도, VPC를 생성하여 클라우드 리소스를 논리적으로 독립
* 퍼블릭 인터넷에 의존하지 않고 전세계로 확장할 수 있음
* VPC에서 어떠한 서버로 전송되는 트래픽은 구글의 글로벌 네트워크를 통해 라우팅
* 자신의 백엔드 서버에 공인 IP를 생성하지 않고도 머신러닝이나 IoT 서비스 같은 구글 서비스에 접근할 수 있음
* Internet Protocal Security(IPSec)을 사용하여 On-Prem의 Virtual Private Network(VPN)에 연결될 수 있음

#### Cloud Load Balancing

* 클라우드 인프라 전반적으로 워크로드를 분산하는 글로벌 로드밸런싱 제공
* Single Multicast IP 주소 사용
  * Region 내 및 여러 region 간 워크로드를 분산
  * 장애가 발생하거나 성능이 저하된 서버에 적응
  * 워크로드의 변화에 따라 Compute resource를 자동적으로 확장
* internal 로드 밸런싱 제공
  * IP 주소가 인터넷에 노출될 필요가 없음
* HTTP, HTTPS, TCP/SSL, UDP 트래픽 로드밸런싱

#### Cloud Armor

* 글로벌 HTTP(S) 로드밸런싱 서비스를 기반한 구글 네트워크 보안 서비스
* Cloud Armor의 기능
  * IP 주소 기반으로 접근을 허용하거나 차단
  * 사이트간 스크립팅 공격에 대응하기 위해 사전에 규칙 정의
  * SQL injection 공격에 대응
  * L3와 L7의 규칙을 정의
  * 인입되는 트래픽의 지리적 위치를 기반으로 접근을 허용하거나 차단

#### Cloud CDN

* Content Delivery Network(CDN)
* 전 세계 Endpoint set으로 컨텐츠를 캐시하여 사용자 요청에 대해 저지연으로 응답
* 구글은 현재 90개 이상의 CDN endpoint을 보유
* 많은 양의 정적 컨텐츠와 글로벌 고객의 분야를 위해 특히 중요

#### Cloud Interconnect

* 기존 네트워크를 구글 네트워크에 연결하기 위한 GCP 서비스
  1. Interconnect
     * VPC에 있는 디바이스에 연결하기 위해 Private Internet 표준(RFC1918)의 주소 할당을 사용
     * On-Prem 데이터센터와 구글 인프라 중 하나와 연결을 유지
     * 구글 인프라와 직접적인 interconnect를 할 수 없다면 Partner Interconnect를 사용
       * 3rd party 네트워크 제공업체에 의존하여 구글 인프라와 연결
  2. Peering
* interconnect나 peering이 필요하지 않은 조직을 위해 구글은 공인 인터넷을 사용한 VPN 서비스 제공

#### Cloud DNS

* GCP에서 제공하는 Domain Name Service
* 자동적으로 확장되도록 설계되어서 기본적인 인프라 확장에 대한 걱정 없이 수 천, 수 만개의 주소를 보유
* VM의 커스텀 이름을 생성할 수있는 Private zone을 제공

### Identity Management

* Identity and Access Management(IAM) 서비스는 사용자가 클라우드 리소스의 세부적인 접근제어를 정의
* IAM은 User, role, privileges 개념을 사용
* Identity(ID)
  * 로그인이나 다른 메커니즘을 통해 인증된 후, 인증된 사용자는 리소스에 접근하고, ID에 부여된 권한을 기반으로 동작을 수행
* User 유사한 권한을 필요할 수 있음
  * 관련된 권한을 그룹으로 묶을 수 있음
* roles은 ID에 할당된 권한의 집합

### Development Tools

* Cloud SDK는 GCP 리소스를 관리하기 위한 Command-line Interface
* Cloud SDK는 Java, Python, Node.js, Ruby, Go, .NET, php를 위한 클라이언트 라이브러리를 제공
* Container Registry, Coud Build, Cloud Source Repositories로 컨테이너화 어플리케이션 배포를 지원
* 어플리케이션이 상용으로 배포된 후에도 어플리케이션을 모니터링하고 유지하는데 도움을 주는 추가 관리도구를 제공

## GCP의 추가적인 Component

### Management Tools

* Stackdriver
  * 어플리케이션과 인프라로부터 metrics, log, event data를 수집
  * 데이터를 통합하여 DevOps 엔지니어가 모니터링, 평가, 운영적인 문제를 진단
* Monitoring
  * GCP, AWS 리소스, Nginx와 같은 오픈소스 시스템을 포함한 어플리케이션 계측으로부터 성능 데이터를 수집하여 Stackdriver의 기능을 확장
* Logging
  * Log 데이터를 저장, 분석, 알람을 받을 수 있는 서비스
* Error Reporting
  * 중앙 인터페이스에 표시하기 위해 어플리케이션 crash 정보를 집계
* Trace
  * 어플리케이션의 지연 데이터를 캡처하여 성능에 문제가 있는 영역을 식별하는데 도움을 주는 분산 추적 서비스
* Debugger
  * 개발자가 실행 중인 코드, 명령어 입력의 상태를 점검하고, 콜 스택 변수를 확인
* Profiler
  * 전반적은 CPU와 메모리 활용 정보를 수집하는데 사용
  * 어플리케이션 성능에 미치는 영향을 최소화하는 통계적인 샘플링 사용
  
### Specialized Services

#### Apigee API Platform

* 본인 어플리케이션에 접근하는 API를 GCP 사용자들에게 제공하기 위한 관리 서비스
* 개발자가 API를 배포하고, 모니터링하고, 보안하는 것을 허용
* Open API Specification 기반으로 API 프록시를 생성
* Apigee API 플랫폼은 사용자가 정의할 수 있는 정책을 기반으로 라우팅과 비율을 제한
* API는 OAuth 2.0이나 SAML을 사용하여 인증
* 전송 중이나 전송 중이지 않을 때 모두 암호화

#### Data Analytics

* BigQuery
  * 데이터 웨어하우스를 위한 Petabyte 급 데이터베이스 분석 서비스
* Cloud Dataflow
  * 배치와 스트림 처리 파이프라인을 정의하기 위한 프레임워크
* Cloud Dataproc
  * 관리형 Hadoop과 Spark 서비스
* Cloud Dataprep
  * 분석가가 분석을 위한 데이터를 탐색하고 준비할 수 있는 서비스

#### AI and Machine Learning

* Cloud AutoML
  * 머신러닝 경험없는 개발자가 머신러닝 모델을 개발할 수 있는 도구
* Cloud Machine Learning Engine
  * 확장 가능한 머신러닝 시스템을 상용에 구축하고 배포하기위한 플랫폼
* Cloud Natural Language Processing
  * 사람의 언어를 분석하고, 텍스트로부터 정보를 추출하기위한 도구
* Cloud Vision
  * 메타데이터, 텍스트 추출, 컨텐츠 필터링을 사용하여 이미지에 표시하기위한 이미지 분석 플랫폼

[맨 위로](#chapter-2-google-cloud-computing-services)

[본문 보기](../Chapter_2.md)