# Chapter 1 구글 클라우드 플랫폼 Overview

## 클라우드 서비스의 유형

퍼블릭 클라우드를 제공하는 업체는 4가지 카테고리의 서비스를 제공한다.
* Compute resource
* Storage
* Networking
* Specialized Service (머신러닝)

### Compute Resource

#### Virtual Machine

* VM은 컴퓨팅 리소스의 기본적인 단위
* GCP는 다양한 수의 vCPU와 다양한 메모리의 VM을 제공
  * VM의 CPU와 Memory를 커스터마이징 할 수 있음
* VM에 완전한 접근권한을 갖고, 아래 동작을 실행할 수 있음
  * 시스템/스토리지 설정
  * OS 설치
  * 추가 소프트웨어 패키지 설치
* GCP는 하나의 액세스 포인트로 들어오는 트래픽을 백엔드로 분산하는 로드밸런서 서비스를 제공
  * 클러스터 중 하나의 VM에서 에러가 발생하면, 다른 VM으로 분산됨
* 워크로드를 기반으로 클러스터의 VM을 추가하거나 제거하는 "Auto Scailing" 기능 제공

#### Kubernetes Cluster

* Kubernetes Cluster는 Container를 활용
  * Container는 동일한 서버에서 하나의 Container에서 동작하는 프로세스가 다른 Container에서 동작하는 프로세스로부터 독립되는 가벼운 VM
* Cluster에서 실행하고자하는 서버의 수와 서버에서 동작하는 Container의 수를 지정할 수 있음
* Container 수에 최적화된 Auto Scailing 파라미터를 지정할 수 있음
* Cluster에서 Container의 상태를 모니터링하여 장애가 발생한 Container를 감지하고 새로운 Container를 실행

#### Serverless Computing

* VM이나 Kubernetes Cluster를 세팅할 필요없이 작성한 코드를 동작할 수 있음
* GCP에서는 2가지 Serverless Computing 옵션이 있음
  1. App Engine
     * 웹사이트 Back-end나 POS 시스템과 같이 장시간 실행되는 어플리케이션을 위해 사용
  2. Cloud Functions 
     * 파일 업로드와 같은 이벤트에 응답하기 위한 코드를 실행

### Storage

GCP는 몇몇 유형의 Storage 서비스를 제공
* Object Storage
* File Storage
* Block Storage
* Caches

#### Object Storage

* Object Storage는 Object는 Blob 단위의 스토리지를 관리하는 시스템
* Object는 File이지만, 기존 File 시스템에 저장되지 않고, Bucket에 그룹화되어 개별로 주소(URL)이 지정
* 서버의 Disk나 SSD 사이즈에 제한되지 않음
* 가용성과 내구성을 위해서 Object의 복사본이 저장되고, 몇몇 케이스에서는 다른 Region에 저장될 수 있음
* Object Storage는 Serverless
* GCP의 Object Storage인 Cloud Storage는 GCP 서버뿐만 아니라 인터넷에 접속한 다른 디바이스에서 접근할 수 있음
* Object 단위로 접근 제어를 적용할 수 있음

#### File Storage

* File을 위한 계층구조의 Storage 시스템이고, 네트워크 공유 파일 시스템을 제공
* GCP의 Cloud Filestore는 네트워크 파일 시스팀(NFS)를 기반으로 함
* File Storage는 OS가 요구되는 어플리케이션에 적합
* File Storage는 VM이나 어플리케이션과 독립적으로 존재

#### Block Storage

* `block`이라고 불리는 고정된 크기의 데이터 구조를 사용하여 구성
* 일반적으로 VM에 연결된 Ephemeral 및 Persistent Disk에 사용
* Block Storage에 File 시스템을 설치하거나 Block에 직접 접근하는 어플리케이션을 실행할 수 있음
* GCP에서 VM에 연결된 Disk에서 이용할 수 있음
  * Persistent Disk
    * VM에서 연결이 끊어지거나 서버가 중단되어도 저장된 데이터를 유지
    * VM과 독립적인 Block Storage 장치에 저장할 때 사용
  * Ephemeral Disk 
    * VM이 동작하는 동안만 데이터를 저장
    * OS파일, VM이 멈추면 삭제되는 데이터를 저장
  * VM 라이프사이클과 독립적으로 사용하고, OS 레벨에 빠르게 접근해야하는 데이터가 있을 때 좋은 옵션
  * Block Storage의 데이터를 조회하는 것은 Object Storage의 데이터를 조회보다 빠름
  * Object Storage와 Block Storage를 결합할 수 있음
    * ex) Persistent Disk에 복사된 대용량 데이터를 Object Storage에 저장

#### Caches

* 데이터에 빠르게 접근하는 in-memory 데이터 저장소
* Caches의 latency는 1ms 미만으로 설계되었음
* 최소한의 latency로 데이터를 읽어야할 때 유용
* 항상 데이터를 Caches에 저장하지 않는 3가지 이유
  * Memory는 SSD나 HDD보다 가격이 비쌈
  * Caches는 휘발성
  * Caches와 실제 시스템간 동기화되지 않을 수 있음

### Networking

* GCP 내의 디바이스는 Internal 및 External 주소를 갖을 수 있음
  * Internal 주소
    * 오직 GCP 내부 네트워크에서 서비스를 접속
    * GCP 내부 네트워크는 Virtual Private Cloud(VPC)로 정의
  * External 주소
    * 인터넷을 통해 접속
    * Static이나 Ephemeral 주소가 될 수 있음
      * Static 주소는 장시간동안 디바이스에 할당
      * Ephemeral 주소는 VM이 중지되었을 때 오픈
* 방화벽 정책은 Cluster 앞에 있는 LoadBalancer의 인바운드와 아웃바운드 트래픽을 제한
* On-Prem 데이터센터와 VPC간에 네트워크 연결을 위해 `peering`을 사용할 수 있음

### Specialized Services

* 공통적인 특성
  * Serverless
  * 텍스트를 번역하거나 이미지를 분석하는 특정 함수를 제공
  * 서비스에 접근하는 API를 제공
  * 서비스를 사용하는만큼 비용을 청구
* GCP의 Specialized Services
  * 머신러닝 서비스, AutoML
  * 텍스트 분석을 위한 Cloud Natural Language
  * 이미지 분석을 위한 Cloud Vision
  * 시간 단위 데이터의 상관관계를 계산하기위한 Cloud Interface API

## Cloud Computing vs Data Center Computing

### Pay-as-You-Go-for-What-You-Use Model

* 클라우드 컴퓨팅의 단기간 임대 모델과 관련있는 것은 종량제 모델
* 최소 기간 이후에 사용한만큼 비용을 지불
  * 분단위 비용은 서버의 특성에 따라 다름
* 클라우드 엔지니어는 클라우드 제공 업체의 비용 모델을 이해해야 함
  * 모니터링하지 않으면, 상당한 비용의 청구서를 받기 쉬움

### Elastic Resource Allocation

* On-Prem과 퍼블릭 클라우드의 큰 차이는 짧은 동작으로 Compute와 Storage 자원을 추가하고 제거할 수 있는 능력

[맨 위로](#chapter-1-%ea%b5%ac%ea%b8%80-%ed%81%b4%eb%9d%bc%ec%9a%b0%eb%93%9c-%ed%94%8c%eb%9e%ab%ed%8f%bc-overview)

[본문 보기](../Chapter_1.md)