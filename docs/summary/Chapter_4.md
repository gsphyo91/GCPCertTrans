# Chapter 4 Introduction to Computing in Google Cloud

## Compute Engine

* GCP에서 동작하는 VM을 제공하는 서비스
* 일반적으로 동작 중인 VM을 `Instance`라고 언급
* Compute Engine을 사용할 때, 하나 이상의 instance를 생성하고 관리

### VM images

* Instance는 OS, 라이브러리, 코드를 포함한 이미지를 실행
  * 구글에서 제공하는 퍼블릭 이미지를 실행하도록 선택할 수 있음
  * 구글에서 제공하는 이미지 외에도 오픈소스 프로젝트나 3rd party 벤터에서 제공하는 이미지도 존재
  * 필요로하는 이미지가 없다면, Boot disk에서 커스텀 이미지를 생성하거나 다른 이미지로 시작할 수 있음
* Cloud Console - Compute Engine - VM Instance - Create Instance
* Snapshot 메류를 통해서 image를 생성하고 다른 VM의 이미지로 사용할 수 있음
* 커스텀 이미지는 실행하는 VM의 각 인스턴스에 OS를 설정하고, 추가 소프트웨어를 설치해야 하는 경우 유용
  * Instance의 Boot disk에서 커스텀 이미지를 생성할 수 있음
* 로컬이나 데이터센터의 커스텀 이미지를 구글이 제공하는 가상 디스크 import 도구로 불러올 수 있음
* 커스텀 이미지는 GCP와 반드시 호환되어야 함

### VM은 프로젝트에 포함

* 인스턴스를 생성할 대, 인스턴스를 포함하는 프로젝트를 지정
* 프로젝트는 공통 정책으로 관련 리소스를 관리

### Zone 및 Region에서 가상머신 실행

* zone
  * 리소스 같은 데이터센터이지만, 하나 이상의 밀접하게 연결된 데이터센터로 구성될 수 있음
  * region 내에 위치
* region
  * 지리적인 위치
  * region 내 zone은 저지연, 높은 대역폭의 네트워크로 구성
* VM을 생성할 때 region과 zone을 지정
* VM이 실행되는 장소를 선택할 때 고려해야 할 요소
  * region간 비용이 다를 수 있음
  * Data Locality Regulation (데이터 지역적 규제)
    * ex) EU 시민의 데이터는 EU에 있어야 함
  * High Availability
    * 여러 zone이나 regiondp 인스턴스를 실행 중일 때, zone이나 region 중 하나에 접근할 수 없게되면, 다른 zone과 region에 있는 인스턴스로 서비스를 제공할 수 있음
  * Latency
    * 인스턴스와 데이터가 지리적으로 어플리케이션 사용자와 가깝게 유지되면 letency를 줄이는데 도움
  * Need for specific hardware platform
    * region마다 다를 수 있음

### 사용자들은 VM을 생성하는 권한이 필요

* 프로젝트에서 Compute Engine 리소스를 생성하기 위해 사용자는 프로젝트나 특정 리소스의 팀 멤버여야 하고, 특정 업무를 수행하는 적절한 권한을 가져야 함
  * Individual User
  * A Google Group
  * A G Suite Domain
  * A Service Account
* 프로젝트에 사용자나 사용자 그룹이 추가되면, role을 부여하여 권한을 할당할 수 있음
* Predefined role은 사용자가 업무를 수행하는데 필요한 권한을 그룹화하기 때문에 유용함
  * Compute Engine Admin
    * 인스턴스 전체를 제어하는 역할을 갖는 사용자
  * Compute Engine Network Admin
    * 대부분의 네트워킹 리소스를 생성, 수정, 삭제
    * 방화벽 정책과 SSL 인증으로 read-only 접근을 제공하는 역할
    * 인스턴스를 생성하거나 대체하는 권한을 사용자에게 부여하지 않음
  * Compute Engine Security Admin
    * SSL 인증과 방화벽 정책을 생성, 수정, 삭제할 수 있는 역할
  * Compute Engine Viewer
    * 리소스를 확인할 수 있지만, 리소스의 데이터를 읽을 수 없는 역할
* 권한이 프로젝트 단위로 사용자에게 부여되면, 프로젝트 내의 모든 리소스에 적용

### Preemptible VM

* 짧게 기동되는 Compute Instance
* 일반적인 Compute Instance와 동일한 설정으로 제공하고, 24시간까지 유지
* Fault-tolerant가 있고, 인스턴스 중단(경고 30초)를 견딜 수 있다면 Preemptible VM을 통해 비용을 크게 줄일 수 있음
* 노드가 작업 중간에 중단되면, 플랫폼을 실패를 감지하고 서버의 다른 노드로 워크로드를 이동시킴

#### Preemtible VM의 제한

* Preemtible VM의 특징
  * 언제든지 종료될 수 있음
    * 10분 내로 종료된다면, 비용이 발생하지 않음
  * 24시간 내에 종료됨
  * 항상 이용하지 못할 수 있음
    * 이용 가능한지는 zone과 region에 따라 다름
  * 일반 VM으로 이관할 수 없음
  * 자동적으로 재시작하도록 설정할 수 없음
  * Service Level Agreement(SLA)를 보장하지 않음

### Custom Machine Types

* Compute Engine은 기본 유형, High-memory machine, high-CPU machine, shared core type, memory-optimized machines으로 그룹화된 25가지 이상의 predefined 머신 유형이 있음
  * ex) n1-standad-1은 1개의 vCPU와 3.75GB의 메모리
* 커스텀 머신 유형은 1~64 vCPU와 vCPU당 6.5GB의 메모리까지 가질 수 있음
* 할당된 vCPU 수와 메모리를 기반으로 비용이 책정

### Compute Engine 가상머신 사용 사례

* Compute Engine으로 할 수 있는 것
  * 인스턴스에서 실행할 특정 이미지를 선택
  * 소프트웨어 패키지나 커스텀 라이브러리 선택
  * 인스턴스에 대한 사용 권한을 가진 사용자가 세부적으로 제어
  * 인스턴스를 위해 SSL 인증과 방화벽 정책에서 제어
* 구글 Compute Engine은 최소한의 관리만 제공
  * 구글은 퍼블릭 이미지와 VM 설정 세트를 제공
  * 관리자로서 사용할 이미지, CPU 수, 할당 메모리, Persistent Disk 설정 방법, 네트워크 설정 방법은 선택해야 함
* GCP에서 리소스에 대해 더 많은 제어를 할수록, 리소스를 구성하고 관리해야하는 책임이 더 커짐

## App Engine

* 어플리케이션을 실행하기 위한 관리형 플랫폼을 제공하는 PaaS Compute 서비스
* 어플리케이션을 실행하는 VM이 아닌 어플리케이션에 초점을 두어야 함
* 기본적인 리소스 요구사항을 지정하고, 구글은 코드를 실행하는데 필요한 리소스를 관리
* VM 인스턴스처럼 어플리케이션은 프로젝트 내에 생성

### App Engine 어플리케이션의 구조

* 어플리케이션은 공통적인 구조를 갖고, 서비스로 구성
  * 서비스는 제품이 사이트에서 판매되었을 때 창고를 업데이트하는 것처럼 특정 기능을 제공
  * 서비스는 버전이 있고, 각 버전은 App Engine에 의해서 관리되는 인스턴스에서 실행
* 어플리케이션을 제공하는데 사용되는 인스턴스의 수는 설정과 현재 로드에따라 달라짐
  * 로드가 증가하면, 구글은 필요한만큼 인스턴스를 추가
  * 로드가 줄어들면, 인스턴스를 종료하여 사용하지 않는 인스턴스의 비용을 절감
  * 이러한 종류의 인스턴스는 Dynamic 인스턴스와 함께 사용
* App Engine은 Resident 인스턴스를 제공
  * 지속적으로 실행되고, 수작업으로 인스턴스를 추가하거나 제거

### App Engine Standard와 Flexible 환경

* Standard 환경은 Language Runtimes을 제공
* Flexible 환경은 더 일반화된 컨테이너 실행 플랫폼

#### App Engine Standard 환경

* 기본적인 App Engine 환경으로 사전에 정의된 언어별 런타임을 구성
  * Python, Java, php, Node.JS, Go

#### App Engine Flexible 환경

* App Engine처럼 PaaS의 장점을 워하는 개발자들에게 더 많은 옵션과 제어를 제공
  * App Engine Standard 환경과 같은 언어와 커스텀에 제약이 없음
* Flexible 환경은 Docker 컨테이너를 사용
* Kubernetes Engine과 비슷하지만, App Engine Flexible은 완전 관리형 PaaS를 제공
* App Engine 서버의 상태는 구글에 의해서 모니터링되고, 어떠한 개입없이 필요에 따라 수정

### App Engine 사용 사례

* App Engine 제품은 기본적인 OS나 스토리지 시스템을 설정하거나 제어할 필요가 거의 없을 때 Computing 플랫폼을 위한 좋은 선택

#### App Engine Standard 환경을 사용할 시기

* 지원되는 언어 중 하나로 작성된 어플리케이션을 위해 설계
* 언어별 런타임을 제공

#### App Engine Flexible 환경을 사용할 시기

* 서비스가 분리될 수 있고, 서비스가 컨테이너화될 수 있는 어플리케이션에 적합
* 기동되는 동안 추가적인 소프트웨어를 설치하거나 명령어를 실행할 필요가 있다면, Dockerfile을 지정
  * 명령어 추가를 위해 `RUN` 명령어 사용 ex) `RUN apt-get update`
* Standard 환경을 로드가 없는 경우 실행 중이지 않은 인스턴스를 중지
* Flexible 환경은 중지하지 않고, 항상 최소 하나의 컨테이너가 실행
  * 로드가 없어도 해당 시간만큼 비용이 발생

## Kubernetes Engine

* 서비스마다 다른 VM의 구성이 요구되고, 단일 리소스의 다양한 인스턴스나 클러스터를 관리하고 싶을 때 Kubernetes Engine을 사용
* Kubernetes(k8s)는 가상머신이나 베어메탈 머신의 클러스터 관리를 위해 구글에서 만든 오픈소스 도구
* Container 오케스트레이션 서비스이고, 아래 동작을 수행
  * Container를 위한 Kubernetes 오케스트레이션 소프트웨어를 실행하는 VM 클러스터를 생성
  * 클러스터에 컨테이너화된 어플리케이션을 배포
  * 클러스터를 관리
  * Auto Scailing과 같은 정책을 지정
  * 클러스터 상태를 모니터링
* Kubernetes Engine은 GCP의 관리형 Kubernetes 서비스
  * VM의 그룹을 배포
  * VM에 Kubernetes를 설치
  * Kubernetes 플랫폼 관리

### Kubernetes 기능

* Kubernetes는 다양한 어플리케이션을 실행하는 클러스터를 지원하도록 설계
* Kubernetes의 기능
  * Kubernetes 클러스터에 배포된 Compute Engine VM에 전반적인 로드밸런싱
  * 클러스터 노드(VM)의 Auto Scailing
  * 필요한 클러스터 소프트웨어의 자동 Upgrading
  * 노드 모니터링과 유지보수
  * 로깅
  * 같은 설정을 갖는 노드의 집합인 노드 풀(Node Pool) 지원

### Kubernetes 클러스터 아키텍처

* Kubernetes 클러스터는 Master Node(`master`)와 하나 이상의 Worker Node(`nodes`)를 포함
* Master Node
  * 클러스터를 관리
  * Kubernetes API Server, Resource Controller, Scheduler와 같은 서비스 실행
  * 각 노드에서 실행할 컨테이너와 워크로드를 결정
* Kubernetes 클러스터가 Cloud Console이나 CLI로 생성될 때, 노드의 수도 결정
  * Compute Engine VM이고, Default Type은 n1-standard-1
* `pods`라고 불리는 그룹에 컨테이너를 배포
  * 하나의 pod 내에 컨테이너는 스토리지와 네트워크 리소스를 공유
  * pod 내의 컨테이너는 IP주소와 Port 범위를 공유
  * 서비스를 할당하기 위한 논리적인 단일 유닛

### Kubernetes High Availability

* Kubernets 클러스터의 상태를 유지하는 방법
  * 자원이 부족해진 pod를 중지하는 것
    * 리소스 임계치를 설정하는 `eviction policies`를 지원
      * 리소스가 임계치를 넘을 때, Kubernetes는 pod를 중지
  * 다수의 동일한 pods를 실행하는 것
    * 동일한 pod의 그룹을 `deployment`라고 부름
    * 동일한 pods는 `replicas`라고 부름
* Deployment가 배포될 때, 될 수 있는 3가지 상태
  * Processing
    * deployment가 업무를 수행 중
  * Completed
    * 컨테이너 배포가 완료되었고, 모든 pod는 컨테이너의 최신 버전으로 실행 중
  * Failed
    * Deployment가 복구할수 없는 문제에 맞닥뜨림

### Kubernetes Engine의 사용사례

* Kubernetes Engine은 높은 가용성과 신뢰성이 요구되는 큰 규모의 어플리케이션을 위해 좋은 선택
* pod와 deployment의 개념을 지원

## Cloud Functions

* GCP 환경에서 발생하는 이벤트에 응답하는 단일 목적의 코드를 실행하도록 설계된 서버리스 컴퓨팅 플랫폼
* VM, Container, Cluster를 할당하거나 관리할 필요가 없음

### Cloud Functions 실행 환경

* GCP 보안되고, 독립된 환경에서 코드를 실행하는데 필요한 모든 것을 관리
* Cloud Functions에 대해서 기억해야할 3가지
  * Functions은 보안되고, 독립된 환경에서 실행
  * Compute Resource는 확장을 제어하기 위해서 어떠한 것도 수행하지 않고, Cloud Functions의 많은 인스턴스를 실행하는데 필요한만큼만 확장
  * 하나의 Functions은 다른 모든 것과 독립
    * Cloud Functions의 lifecycle은 서로 의존하지 않음
* Cloud Functions는 동시에 다수의 인스턴스에서 실행될 수 있음
  * 하나의 앱에서 두개의 이미지를 업로드하는 경우, 2가지 다른 인스턴스가 실행됨
  * 두 인스턴스는 독립
* 호출이 분리된 인스턴스에서 실행될 때, Functions는 메모리나 변수를 공유하지 않음
  * Stateless를 충족
* 짧게 실행되고, 이벤트 기반의 처리에 적합
* Cloud Functions은 다음 영역에 적합
  * IoT
    * 센서나 다른 디바이스로부터 전송되는 값에 의존하여 Cloud Functions은 알람을 동작하거나 데이터를 처리
    * IoT 앱 같은 모바일 어플리케이션은 처리를 위해서 데이터를 클라우드로 전송

[맨 위로](#chapter-4-introduction-to-computing-in-google-cloud)

[본문 보기](../Chapter_4.md)