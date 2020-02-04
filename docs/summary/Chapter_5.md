# Chapter 5 Computing with Compute Engine Virtual Machine

## Console로 VM 생성하고 설정

* Cloud Console
* 프로젝트 생성
* Compute Engine
* VM Instance

### VM 주요 설정 정보

* VM 생성에 필요한 정보
  * VM 이름
  * VM을 실행할 Region, Zone
  * Machine Type
    * VM의 CPU 수와 메모리의 양
  * Boot Disk
    * VM이 실행할 OS
  * Identity and API access
    * VM을 위한 service account를 지정하고, API 접근 범위를 설정
  * Firewall
    * HTTP나 HTTPS 트래픽의 접근

### 추가 설정 정보

### Management Tab

* VM을 설명 입력
* Key-Value 쌍의 Label을 생성
  * 서버의 수가 늘어날 때 중요
* deletion protection
  * 인스턴스를 삭제하기 전에 추가 확인을 강제로 적용
* Startup script
  * 인스턴스가 기동될 때 실행하는 스크립트
  * bash나 python 스크립트 입력 가능
* Metadata
  * 인스턴스와 연관된 Key-Value를 지정
    * Key는 metadata 서버에 저장
* Availability Policy
  * Preemptibility
    * 설정하면, 구글은 30초간 경고를 보내고 서버를 중지할 수 있음
    * 일반 VM보다 비용이 적음
  * Automatic restart
    * 서버의 하드웨어 장애, 유지보수 등의 이슈 발생 시 중지해야하는지 여부
  * Host maintenance
    * 유지보수 이벤트가 발생햇을 때, VM을 다른 물리버서로 이동시켜야하는 지
* Security
  * Shielded VM과 Secure Shell(SSH) Key를 사용할 지 지정
  * Shielded VM
    * 추가 보안 메커니즘을 갖도록 구성
    * Secure Boot
      * 오직 인증된 OS 소프트웨어만 VM에서 실행
      * 소프트웨어의 디지털 서명을 확인하고, 확인에 실패하면 부트 프로세스 중지
    * Virtual Trusted Platform Module(vTPM)
      * Trusted Platform Module의 가상화 버전
      * TPM은 Key와 인증처럼 보안 리소스를 보호하도록 설계된 특별한 컴퓨터 칩
    * Intergrity Monitoring
      * 유명한 부트 측정 기준을 사용하여 최근 부트 측정을 비교
      * 확인이 실패하면, 측정 기준과 현재 측정간 차이를 보여줌
  * SSH Key
    * 사용자에게 프로젝트 전반적으로 VM에 접근하는 데 사용
* Disk
  * 인스턴스가 삭제될 때, Boot Disk를 삭제할지 지정
  * Boot Disk를 위한 암호화 키를 어떻게 관리하고 싶은지 선택
    * 기본적으로 구글이 키를 관리
  * 새로운 디스크를 추가하거나 기존 디스크를 추가하는 옵션
  * 새로운 디스크를 추가할 때 제공해야하는 정보
    * Disk 이름
    * Disk type
      * Standard
      * SSD Persistent Disk
    * Source Image
    * 기가바이트 급 사이즈
    * 암호화 키가 관리되는 방법
* Networking
  * VM의 IP를 포함한 네트워크 인터페이스 정보를 확인
  * 다수의 네트워크를 갖고있다면
    * 다른 네트워크에 다른 네트워크 인터페이스를 추가할 수 있음
  * 네트워크간 일부 트래픽을 제어하는 프록시나 서버를 통작하는 경우 유용
  * 네트워크 태그 설정
* Sole Tenancy
  * 다른 VM과 함께 서버에서 실행되어야 한다면 서버에 Label을 지정

## Cloud SDK로 VM 생성과 설정

### Cloud SDK 설치

* 구글 클라우드 리소스와 상호작용하기 위한 3가지 옵션
  * CLI
  * RESTful Interface
  * Cloud Shell
* Cloud SDK는 Linux, Windows, Mac에 설치될 수 있음

### Cloud SDK에서 VM 생성

* `gcloud` 명령어로 할 수 잇는 클라우드 관리 업무
  * Compute Engine
  * Cloud SQL Instance
  * Kubernetes Engine
  * Cloud Dataproc
  * Cloud DNS
  * Cloud Deployment
* 인스턴스 생성을 위한 파라미터
  * `--boot-disk-size`
    * Boot Disk의 크기
    * 10GB에서 2TB 사이
  * `--boot-disk-type`
    * Boot Disk의 타입
    * `gcloud compute disk-types list`에서 확인
  * `--labels`
    * Key=value 포맷의 리스트
  * `--machine-type`
    * 사용하는 머신 타입
      * 지정하지 않으면 n1-standard-1을 사용
    * `gcloud compute machine-type list`에서 확인
  * `--preemptible`
    * VM을 Preemptible로 지정

``` bash
# Compute Engine 인스턴스 생성
# zone을 설정하지 않으면 default 프로젝트 정보를 사용
gcloud compute instances create [option] [instance name] [zone]

# 생성된 VM 리스트 확인
gcloud comptue instances list

# Example
gcloud compute instances create --machine-type=n1-standard-8 --preemptible intance-n1
```

### Cloud Shell에서 VM 생성

* Cloud Console에서 Cloud Shell 클릭하여 Cloud SDK와 동일한 동작 수행

## 기본 VM 관리

* Cloud Console - Compute Engine - VM Instance

### VM에 네트워크 접근

* Cloud Console의 VM 리스트에서 SSH 버튼 클릭

### VM 모니터링

* VM 인스턴스의 상세 페이지의 모니터링 페이지에서 CPU, Disk, 네트워크 로드를 모니터링할 수 있음
  
### VM 비용

* VM 비용에 대해서 기억해야할 요소
  * VM은 1초 단위로 계산
  * 비용은 machine type을 기반으로 함
    * 많은 CPU와 메모리를 사용하면 더 많은 비용이 발생
  * 지속적인 사용에 대한 할인 제공
  * VM은 최고 1분 단위로 청구
  * Preemptible VM은 VM의 비용을 80%까지 절약

## VM 기획, 배포, 관리를 위한 가이드라인

* 적은 수의 VM에서 작업할 때 적용되는 가이드라인
  * 요구사항을 충족하는 적은 수의 CPU의 적은 양의 메모리의 machine type을 선택
  * VM 관리를 위해 Console을 사용
  * 기동될 때 수행해야하는 여러 작업은 Startup Script 사용
  * Machine Image에 많은 수정이 있다면 시스템에 이미지를 저장하고 새 인스턴스와 함께 사용
  * 예상치 못한 장애가 발생해도 괜찮다면, Preemptible VM 사용
  * SSH나 RDP를 사용하여 VM에 접근하고 OS레벨의 작업을 수행
  * VM레벨 작업을 수행하기 위해 Cloud Console, Cloud Shell, Cloud SDK를 사용

[맨 위로](#chapter-5-computing-with-compute-engine-virtual-machine)

[본문 보기](../Chapter_5.md)