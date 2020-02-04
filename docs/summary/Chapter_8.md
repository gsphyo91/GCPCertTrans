# Chapter 8 Kubernetes 클러스터 관리

## Kubernetes 클러스터 상태 확인

* Cloud Console이나 `gcloud` 명령어 중 하나를 사용하여 Kubernetes 상태 확인

### Cloud Console을 사용하여 Kubernetes 클러스터의 상태 확인

* k8s 클러스터 리스트에서 클러스터를 클릭하면 상세정보가 표시
* 클러스터 이름 아래의 3가지 옵션
  * Details
    * Add-ons은 클러스터의 optional 추가 기능의 상태를 표시
    * Permission은 클러스터에 활성화된 GCP Service API가 표시
    * Node-pool은 k8s 클럿터에 실행 중인 개별 인스턴스 그룹
    * node에서 실행 중인 node 이미지, 머신 타입, 전체 vCPU 수, 디스크 타입, preemptibel인지 아닌지 확인
  * Storage
    * 클러스터에서 사용되는 Persistent Volume, Storage class 표시
  * Nodes
    * 클러스터에서 실행 중인 node나 VM 리스트를 확인
    * node에서 실행 중인 pod의 리스트 확인
    * pod 상세 정보
      * CPU, 메모리, 디스크 통계 확인 화면
      * pod 생성일, label, 로그링크, 상태를 포함
      * pod의 상태
        * Pending
          * 이미지를 다운로드 중
        * Succeeded
          * 성공적으로 종료되었다는 것
        * Failed
          * 최소 하나의 컨테이너가 장애인 경우
        * Unknown
          * 마스터가 node에 도달할 수 없어 상태를 결정할 수 없는 경우
      * 컨테이너 리스트
        * 컨테이너 상태, 기동시간, 실행 중인 명령어, 마운트된 volume 확인

### Cloud SDK와 Cloud Shell을 사용하여 Kubernetes 클러스터 상태 확인

```bash
# 클러스터 이름과 기본정보 조회
gcloud container cluster list

# 클러스터 상세정보 확인
gcloud container cluster describe --zone [ZONE] [CLUSTER_NAME]

# 클러스터의 node 리스트 조회
kubectl get nodes

# 클러스터의 pod 리스트 조회
kubectl get pods

# node와 pod의 자세한 정보
kubectl describe nodes
kubectl describe pods
```

## Nodes 추가, 수정, 삭제

### Cloud Console로 Nodes 추가, 수정, 삭제

* Kubernetes Engine 페이지를 열고, 클러스터의 이름을 클릭하여 Edit 클릭
* Node Pools 섹션의 아래에 클러스터에 대한 정보 조회

### Cloud SDK와 Cloud Shel로 추가, 수정, 삭제

* nodes를 추가하거나 수정하는 명령은 `gcloud container cluster resize`

```bash
# 클러스터의 수를 3에서 5로 증가하는 경우
gcloud container clusters resize [CLUSTER_NAME] --node-pool [NODE_POOL_NAME] --size 5 --region=[REGION]

# 클러스터 수정, 오토 스케일링 적용
gcloud container cluster update [CLUSTER_NAME] --enable-autoscaling --min-nodes 1 --max-nodes 5 --zone [ZONE] --node-pool [NODE_POOL_NAME]
```

## Pods 추가, 수정, 삭제

* pod를 직접 조작하지 않는 것이 좋음
* pod의 수를 변경하고 싶다면, deployment 설정을 변경

### Cloud Console로 Pods 추가, 수정, 삭제

* deployment의 replicas는 어플리케이션을 실행할 pod의 수
* Console - Kubernetes Engine - Workloads - 수정하고자하는 deployment 클릭
* 위쪽 메뉴에서 Action 클릭
  * Autoscale, Expose, Rolling Update, Scale 옵션
  * Scale을 선택하면 replicas 수를 변경할 수 있음
  * Autoscale을 지정하여 replicas를 지정하거나 삭제할 수 있음
  * Expose에서 service를 노출하는 것을 설정
  * Rolling Update를 하기위한 파라미터 지정
    * pod를 업데이트하려고 판단하기 전 대기하는 최소 시간
    * 허용된 목표 크기를 초과하는 pod의 최대 수
    * 사용할 수 없는 pod의 최대 수

### Cloud SDK와 Cloud Shell로 pod를 추가, 수정, 삭제

* deployment 작업을 통해 수행

```bash
# deployment 조회
kubectl get deployments

# repplicas 수 수정 (5개)
kubectl scale deployment [DEPLOYMENT_NAME] --replicas 5

# deployment 삭제
kubectl delete deployment [DEPLOYMENT_NAME]
```

## Service 추가, 수정, 삭제

### Cloud Console에서 Service 추가, 수정, 삭제

* Service는 deployment를 통해 추가
* Console의 Workload 페이지 위쪽의 Deploy 옵션을 클릭하면 deployment 양식 표시
* deployment의 이름을 클릭하면 service의 리스트를 포함한 deployment의 상세정보 확인

### Cloud SDK와 Cloud Shell로 Service 추가, 수정, 삭제

```bash
# Service 조회
kubectl get services

# service 추가
kubectl run [SERVICE_NAME] --image=[CONTAINER_REGISTRY_URL] --port [PORT]

# 외부에서 접속하기위한 포트 노출
kubectl expose deployment [SERVICE_NAME] --type="LoadBalancer"

# Service 삭제
kubectl delete service [SERVICE_NAME]
```

## 이미지 Repository와 이미지 상세정보 확인

* Container Repository는 컨테이너 이미지를 저장하기 위한 GCP 서비스
* Registry를 생성하고 이미지를 저장하면, Console이나 SDK/Shell에서 컨텐츠와 이미지 확인 가능

### Cloud Console로 이미지 Repository와 이미지 상세정보 확인

* Cloud Console - Container Registry
* 이미지 이름을 클릭하면 이미지의 상세정보 확인
  * 이미지 타입, 사이즈, 생성시간이 포함

### Cloud SDK와 Cloud Shell로 이미지 Repository와 이미지 상세정보 확인

```bash
# 이미지 조회
gcloud container images list

# container Repository의 리스트 조회
gcloud container images list --repository [GCR_URL]

# 이미지 상세정보 확인
gcloud containrer images describe [GCR_URL]
```

[맨 위로](#chapter-8-kubernetes-%ed%81%b4%eb%9f%ac%ec%8a%a4%ed%84%b0-%ea%b4%80%eb%a6%ac)

[본문 보기](../Chapter_8.md)