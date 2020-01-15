# Chapter 6 Managing Virtual Machine

## 단일 VM 인스턴스 관리

* 단일 인스턴스
  * 인스턴스 그룹이나 다른 유형의 클러스터가 아닌 자체로 생성된 인스턴스

### Console에서 단일 VM 인스턴스 관리

* 클라우드 엔지니어가 친숙해야하는 기본 VM 관리 업무는 인스턴스 생성, 중지, 삭제, VM 조회, GPU 추가, 스냅샷 이미지 작업

#### 인스턴스 기동, 중지, 삭제

* Cloud Console - Compute Engine - VM Instance
* 인스턴스가 중지되면 비용이 청구되지 않음

#### VM 인벤토리 확인

* Cloud Console의 VM Instancs에서 VM 리스트 확인
* 필터를 이용하여 인스턴스 조회
* 다양한 필터 조건을 설정하는 경우
  * OR 연산자를 명시적으로 입력하지 않으면, 모두 AND 연산

#### 인스턴스에 GPU 할당

* GPU는 가상화와 머신러닝같은 계산 집중 어플리케이션을 위해 사용
* GPU 라이브러리가 설치되어 있거나 설치될 인스턴스를 시작해야 함
  * 인스턴스가 GPU를 사용할 수 있는 zone에서 동작되는지 반드시 확인
* Machine Type의 Customize를 클릭하여 GPU 파라미터 설정
* GPU 사용의 몇 가지 제한
  * CPU는 선택된 GPU와 반드시 호환되어야 함
  * GPU는 공유 메모리 머신에 추가될 수 없음
* VM에 GPU를 추가할 경우
  * 유지보수 중에 종료되도록 인스턴스를 설정해야 함
  * VM 설정페이지의 Availability Policies 영역에서 설정

#### 스냅샷 작업

* Snapshot은 Persistent Disk의 데이터 복사본
  * 데이터를 복구하기 위해 디스크에 데이터를 저장
  * 동일한 데이터로 다양한 persistent disk를 생성하는 편리한 방법
* Snapshot 생성
  * GCP는 Persistent disk에 데이터의 전체 복사본을 생성
  * 디스크에서 snapshot을  생성하면 GCP는 이전 스냅샷에서 변경된 데이터만 복사
* 디스크에 쓰기 전에 메모리에 버퍼 데이터를 갖는 데이터베이스나 다른 어플리케이션이 동작 중이면, 스냅샷을 생성하기 전에 디스크 버퍼를 비워야 함
* 스냅샷을 작업하기 위해 사용자는 Compute Storage Admin role을 할당 받아야 함

#### 이미지 작업

* Snapshot은 디스크에 이용할 수 있는 데이터를 만다는데 사용되고, Image는 VM을 생성하는데 사용
* Cloud Console - Compute Engine - Image에서 생성
* Image 생성
  * 이름, 설명, Label을 지정
  * Family 속성을 통해 그룹 이미지 생성
  * source위해 옵션을 선택
  * 선택한 Source의 종류를 선택
* Image 관리
  * 커스텀 이미지만 Delete나 Deprecate를 선택할 수 있음
  * GCP 제공 이미지는 할 수 없음
  * Delete는 이미지를 제거
  * Deprecated는 이미지를 더이상 지원하지 않는다는 것을 의미
  * Google의 deprecated 이미지는 사용할 수 있지만, 보안 결함이나 다른 업데이트를 위해 패치되지 않을 수 있음
* Image를 생성한 후, image 리스트에서 Create instance 옵션을 선택해 인스턴스를 생성할 수 있음

### Cloud Shell과 CLI로 단일 VM 인스턴스 관리

#### 인스턴스 기동

* 인스턴스를 기동하기 위해 `gcloud` 명령어를 사용
* `--async`
  * `start` 동작에 대한 정보를 표시
  * `--zone` zone을 선택

```bash
gcloud compute instances start [INSTANCE_NAMES]

//async, zone
gcloud compute instances start instance-1 --zone us-central1-c --async
```

#### 인스턴스 중지

```bash
gcloud compute instances stop [INSTANCE_NAMES]

//--async
gcloud compute instances stop [INSTANCE_NAMES] --async

//--zone
gcloud compute instances stop [INSTANCE_NAMES] --async --zone [ZONE]
```

#### 인스턴스 삭제

* `--delete-disks`와 `--keep-disks`
  * VM에 있는 디스크를 삭제하거나 저장
  * `all`
    * 모든 디스크를 유지
  * `boot`
    * 루트 파일 시스템의 파티션 지정
  * `data`
    * 부팅되지 않는 디스크를 지정

```bash
gcloud compute instances delete [INSTANCE_NAME]

gcloud compute instances delete [INSTANCE_NAME] --zone [ZONE] --keep-disks=all

gcloud compute instances delete [INSTANCE_NAME] --zone [ZONE] --delete-disks=data
```

#### VM 인벤토리 확인

* 인벤토리에 있는 VM의 집합 조회
* 인스턴스 이름은 optional

```bash
gcloud compute instances list

//특정 zone의 VM 리스트
gcloud compute instances list --filter="zone:ZONE"

//실행 중인 VM의 리소스 필드를 확인
gcloud compute instances describe
```

#### 스냅샷 작업

```bash
gcloud compute disks snapshot [DISK_NAME] --snapshot-names=[NAME]

//snapshot 리스트 조회
gcloud compute snapshots list

//snapshot에 대한 상세 정보
gcloud compute snapshots describe [SNAPSHOT_NAME]

//디스크의 사이즈와 타입을 지정
gcloud compute disks create [DISK_NAME] --source-snapshot=[SNAPSHOT_NAME] --size=100 --type=pd-standard
```

#### 이미지 작업

```bash
gcloud compute images create [IMAGE_NAME]
```

* 옵션
  * `--source-disk`, `--source-image`, `--source-snapshot`
    * 각각 디스크, 이미지, 스냅샷을 사용하여 이미지 생성
  * `--source-image-family`
    * family 이미지 중 최신 버전을 사용
  * `--source-uri`
    * 웹 주소를 사용하여 이미지를 지정
  * `--description`
  * `--labels`

## 인스턴스 그룹 소개

* 단일 엔티티로 관리되는 VM의 집합
* 인스턴스 그룹에 적용되는 `gcloud`나 콘솔 명령은 인스턴스 그룹의 모든 멤버에 적용
* 인스턴스 그룹의 유형
  * managed
    * Machine type, Boot Disk, Image, zone, label 등 인스턴스의 다른 파라미터를 포함된 템플릿을 사용하여 생성
    * 자동적으로 인스턴스 수 확장
    * 그룹 전반적으로 워크로드를 분산하는 로드밸런싱
    * 인스턴스가 충돌하면 자동적으로 재생성
  * unmanaged
    * 그룹 내 다른 VM 내에서 다른 설정으로 작업해야할때만 사용

### 인스턴스 그룹과 템플릿을 생성하고 제거

```bash
gcloud compute instance-template create [INSTANCE]

//--source-instance
gcloud compute instance-templates create [INSTANCE] --source-instance=[기존 VM]

//인스턴스 템플릿 삭제
gcloud compute instance-template delete [INSTANCE_TEMPLATE_NAME]

//인스턴스 그룹 삭제
gcloud compute instance-groups managed delete-instances [INSTANCE_GROUP_NAME]

//인스턴스 템플릿 조회
gcloud compute instances-templates list

//인스턴스 그룹 조회
gcloud compute instance-groups managed list-instances
```

* 옵션
  * `--source-instance`
    * 인스턴스 템플릿의 source로 기존 VM 지정
    * 디폴트로 n1-standard-1 이미지 사용
* 인스턴스 그룹은 단일 zone이나 region 전반적으로 인스턴스를 포함할 수 있음
  * zonal 관리형 인스턴스 그룹
  * regional 관리형 인스턴스 그룹
    * zone 전반적으로 워크로드가 분산
* Cloud Console의 Instance Group Template
  * 인스턴스 템플릿 삭제

### 인스턴스 그룹 로드밸런싱과 오토 스케일링

* GCP는 로드밸런싱을 제공
* Auto Scailing 구성 가능
  * CPU 사용량, 모니터링 메트릭, 로드밸런싱 조정량, 큐기반 워크로드를 기반으로 Auto Scailing 정책 구성

## VM 관리를 위한 가이드라인

* Label과 Decription 사용
  * 인스턴스의 목적을 확인하는데 도움을 줌
  * 인스턴스의 리스트를 필터링할 때 도움을 줌
* Auto Scailing과 Load Balancing이 적용되는 Managed Instance Group 사용
  * 확장 가능하고, 고가용성 서비스를 배포하는데 핵심
* 계산 집약적인 처리를 위해 GPU 사용
  * GPU는 다른 CPU를 추가하는 것보다 성능적으로 더 큰 이점을 줌
* 디스크의 상태를 저장하거나 복사본을 만들기 위해 스냅샷 사용
  * Cloud Storage에 저장되어 백업 역할
* 장애에도 문제가 없다면 Preemptible VM을 사용
  * 인스턴스의 비용을 80%까지 절약