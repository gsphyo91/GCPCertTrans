# Chapter 16 Cloud Launcher와 Deployment Manager로 어플리케이션 배포

## Cloud Launcher를 사용하여 솔루션 배포

* Cloud Launcher는 GCP 환경에 배포될 수 있는 어플리케이션과 데이터 셋의 중앙 저장소
* Cloud Launcher 작업의 2단계 프로세스
    * 솔루션 조회
    * 솔루션 배포

### Cloud Launcher를 탐색하고 솔루션 조회

* Console - Marketplace
* 솔루션 리스트를 확인하기위해 필터로 탐색 및 검색, 특정 카테고리 선택 가능
* 라이센스 타입으로 OS 리스트를 필터할 수 있음
    * Free, Paid, Bring Your Own License(BYOL)
    * Paid OS의 비용은 GCP Billing에 포함

### Cloud Launcher 솔루션 배포

* 어플리케이션 배포 파라미터
    * 솔루션 배포를 위한 파라미터는 어플리케이션마다 다름
    * 이름
    * zone
    * machine type
    * Persistent disk
        * 타입과 사이즈 선택
    * Networking
        * VM을 실행하기 위한 네트워크와 서브넷 지정

## Deployment Manager를 사용하여 어플리케이션 배포

* 자체 솔루션 구성 파일을 생서해서 사용자가 사전 정의된 솔루션을 실행

### Deployment Manager 구성파일

* Deployment Manager 구성 파일은 YAML 문법으로 작성
* resource로 시작하여 3개의 필드를 사용하여 정의된 리소스 엔티티가 있음
    * `name`, 리소스 이름
    * `type`, compute.v1.instance와 같은 리소스의 타입
    * `properties`, 리소스를 위한 구성 파라미터를 지정하는 key-value 쌍

```yaml
# YAML 예시
resource:
- type: compute.v1.instance # 리소스 타입
  name: ace-exam-deployment-vm # 리소스 이름
  properties: 
    machineType: https://www.googleapis.com/compute/v1/projects/[PROJECT_ID]/zones/us-central1-f/machineTypes/f1-micro # Google API 리소스 사양의 URL
    disks:
    - deviceName: boot
      type: PERSISTENT
      boot: true
      autoDelete: true
```

### Deployment Manager 템플릿 파일

* deployment 구성이 복잡해지고 있다면, deployment 템플릿 사용
* 리소스를 정의하고, 리소스를 구성 파일로 가져오는데 사용
* 템플릿은 Python이나 Jinja2로 작성

### Deployment Manager 템플릿 실행

```bash
# deployment 템플리 실행
gcloud deployment-manager deployments create quickstart-deployment --config vm.yaml

# deployment의 상태 설명
gcloud deployment-manager deployments describe quickstart-deployment
```

[맨 위로](#chapter-16-cloud-launcher%ec%99%80-deployment-manager%eb%a1%9c-%ec%96%b4%ed%94%8c%eb%a6%ac%ec%bc%80%ec%9d%b4%ec%85%98-%eb%b0%b0%ed%8f%ac)

[본문 보기](../Chapter_16.md)