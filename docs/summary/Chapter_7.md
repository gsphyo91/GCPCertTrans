# Chapter 7 Computing with Kubernetes

## Kubernetes 소개

* GCP에서 관리하는 Kubernetes 서비스
* 사용자는 Kubernetes 플랫폼을 관리할필요 없이 Kubernetes 클러스터를 생성하고 관리할 수 있음
* Kubernetes의 Container Orchestration
  * VM의 클러스터에서 컨테이너를 실행하고, 컨테이너가 실행될 곳을 결정하고, 컨테이너의 상태를 모니터링하고, VM의 완전한 Lifecycle을 관리
* Kubernetes 클러스터는 인스턴스 그룹과 유사
  * 그룹으로 관리될 수 있는 VM의 집합
  * 차이점
    * 인스턴스 그룹은 모든 VM에서 같은 이미지를 실행하지만, Kubernets는 아님
    * 인스턴스 그룹은 컨테이너의 배포를 지원하는 메커니즘이 없음
* 컨테이너
  * 게스트 OS를 복제하지 않고도 VM처럼 어플리케이션이나 워크로드를 분산하고 확장
  * 아주 빠르게 기동 및 중지될 수 있음
  * 아주 적은 리소스를 사용
* Kubernetes는 인스턴그 그룹에 비해서 유지보수와 관련하여 더 많은 융통성을 갖음

### Kubernetes 클러스터 아키텍처

* Kubernetes 클러스터는 Master node와 하나 이상의 Worker node로 구성
  * 마스터 노드
    * 클러스터를 제어하고, 고가용성과 장애대응을 위한 replicated와 분산
    * Kubernetes에서 제공되는 서비스를 관리
      * Kubernetes API, Controllers, Schedulers
    * 클러스터와의 모든 상호작용은 Kubernetes API를 사용하여 마스터를 통해 동작
    * 노드에서 동작을 수행하는 명령어 실행
  * 워커 노드
    * 어플리케이션을 실행하도록 설정된 컨테이너를 실행하는 VM
    * 마스터 노드에 의해 주로 제어되지만, 일부 명령어로 수동제어 가능
    * `kubelet` 에이전트를 통해 마스터 노드와 통신
* `kubectl` 명령어를 사용해 클러스터와 상호작용
* 클러스터를 생성할 때, 머신타입을 지정할 수 있음
* 클러스터의 메모리, CPU 일부는 k8s에서 선점

### Kubernetes Object

* Pods
* Services
* Volumes
* Namespaces

#### Pods

* 클러스터에서 실행 중인 프로세스의 단일 인스턴스
* 하나 이상의 컨테이너를 포함
* 보통 하나의 컨테이너를 싱행하지만, 다수의 컨테이너도 실행 가능
  * 2개 이상의 컨테이너가 반드시 리소스를 공유해야할 때 사용
* 유니크한 IP 주소와 Port의 집합을 얻음
* pod 내 다수의 컨테이너는 서로다른 포트에 연결되고, localhost로 통신할 수 있음
* pod는 컨테이너가 마치 독립된 VM에서 실행 중인 것처럼 공용 스토리지, 하나의 IP주소, port 집합을 공유하는 것처럼 작동
  * 구성을 변경하지 않고도 동일한 어플리케이션의 여러 인스턴스나 다른 어플리케이션의 다른 인스턴스를 동일한 노드나 다른 노드에 배포할 수 있음
* pod는 일반적으로 그룹으로 생성
* Replicas는 pod의 복사본
* pod는 Auto scailing을 지원
* pod가 unhealthy 상태이면 종료됨
* pod는 Compute Engine의 관리형 인스턴스그룹과 유사
  * 차이점
    * pod는 컨테이너에서 어플리케이션을 실행하기 위한 것이고, 클러스터의 다양한 노드에 위치할 수 있음
    * 인스턴스 그룹은 각 노드에 동일한 어플리케이션 코드를 실행
    * 인스턴스 그룹은 CLI나 콘솔을 통해서 스스로 관리하지만, pod는 Controller에서 관리함

#### Services

* 특정 IP 주소를 갖는 pod가 종료되고, 다른 pod가 생성되면 IP주소가 변경될 수 있음
* service는 API Endpoint에 안정적인 IP 주소를 제공하여 특정 어플리케이션을 실행하는 pod를 찾을 수 있도록 하는 객체
* Service는 pod가 변경될 때 업데이트 됨

#### ReplicaSet

* deployment에 의해서 사용되고, 동일한 수의 pod가 실행 중이라는 것을 보장하는 controller

#### Deployment

* Deployment는 동일한 pod의 세트
* 동일한 pod template를 사용하여 생성되고, 동일한 어플리케이션을 실행함
* pod template는 pod를 실행하는 방법에 대한 정의
  * pod specification이라고 부름
* k8s는 template에 정의된 상태롤 pod를 유지
* specification에는 deployment에 포함되어야하는 pod의 최소 수가 있음
  * 최소 수에 미치지 못하면, ReplicaSet을 호출하여 추가적인 pod가 deployment에 추가됨

#### StatefulSet

* Deployment는 Stateless 어플리케이션에 적합
  * stateless 어플리케이션은 상태를 추적할 필요가 없는 어플리케이션
* statefulSet은 deployment와 같지만, pod에 유니크한 ID를 지정
  * 이를 통해, k8s는 어떤 클라이언트가 어떤 pod를 사용하는지 추적할 수 있음
* StatefulSet은 어플리케이션이 유니크한 네트워크 ID나 안정적인 Persistent 스토리지가 필요할 때 사용

#### Job

* 워크로드에 대한 추상화
* Job은 pod를 생성하고, 어플리케이션이 워크로드를 완료할 때까지 pod를 실행

## Kubernetes 클러스터 배포

* Cloud Console이나 Cloud Shell 또는 Cloud SDK가 설치된 로컬 환경에서 배포

### Cloud Console에서 Kubernetes 클러스터 배포

* Kubernetes Engine을 사용하기 위해, Kubernetes Engine API를 활성화
* 처음 사용하면 Credential을 만들어야 함
  * overview 페이지 위쪽에 Create Credential 버튼 클릭하여 생성
  * 사용할 API를 지정한 다음 credentials을 생성
* Create Cluster를 클릭하면 몇몇 템플릿을 선택할 수 있음
  * 템플릿은 vCPU 수, 메모리, GPU 사용에따라 다름
  * 템플릿에서 제공되는 파라미터를 수정할 수 있음
* 클러스터 리스트에서 클러스터를 수정, 삭제하고, 클러스터에 연결할 수 있음
* Kubernetes Engine 섹션에서 Workload 페이지에서 현재 실행 중인 워크로드를 확인할 수 있음

### Cloud Shell관 Cloud SDK를 사용하여 Kubernetes 클러스터 배포

* Kubernetes Engine을 작업하기 위해 `gcloud` 커맨드를 사용

```bash
gcloud container
```

* `glcoud` 파라미터
  * Projcet
  * zone
  * machine type
  * image type, disk type
  * disk size
  * number of nodes

```bash
// 기본 명령어
gcloud container clusters create [CLUSTER_NAME] --num-nodes=3 --region=[REGION]
```

## 어플리케이션 pod 배포

* Kubernetes Engine의 Cluster 페이지에서 Create Deployment 선택하여 양식 지정
  * Container image
  * Environment variables
  * Initial command
  * Application Name
  * Labels
  * Namespace
  * Cluster to Deploy to
* Deployment를 지정하면, 그에 맞는 YAML 파일이 표시

* CLI로 작업

```bash
//Docker 이미지 생성
kubectl run [NAME] --image=[IMAGE_NAME] --port=8080

//replicas 수 확장
kubectl scale deployment [DEPLOYMENT_NAME] --replicas=5
```

## Kubernetes 모니터링

* Stackdriver 사용
* Create Cluster 양식에서 Advanced Options에 Stackdriver 모니터링, 로깅 활성화 메뉴 선택
* Stackdriver 설정
  * Workspace 생성
    * workspace는 모니터링을 위한 리소스
    * 모니터링하는 프로젝트를 100개까지 지원
  * 인스턴스 이름을 클릭하여 상세정보 페이지 조회
    * CPU 사용량, 디스크I/O, 네트워크 트래픽 확인
    * CPU 사용량 초과와 같은 조건을 설정하여 알림을 받을 수 있는 alerting 정책
    * 알림이 동작되는 경우, 이메일/webhooks/SMS/Slack 등으로 알림을 받을 수 있음

[맨 위로](#chapter-7-computing-with-kubernetes)

[본문 보기](../Chapter_7.md)