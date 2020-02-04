# Chapter 9 Computing with App Engine

## App Engine Component

* App Engine 컴포넌트 4가지
  * Application
  * Service
  * Version
  * Instance
* 프로젝트는 하나의 App Engine 어플리케이션을 갖을 수 있음
* App Engine과 연관된 모든 리소스는 어플리케이션이 생성될 때 지정되는 region에 생성
* 어플리케이션은 최소 하나의 Service를 갖음
* App Engine은 어플리케이션의 버전관리를 지원
* Service는 다양한 Version은 갖을 수 있음
  * version이 실행될 때, 어플리케이션의 인스턴스를 생성

## App Engine 어프리케이션 배포

### Cloud Shell과 SDK를 사용하여 어플리케이션 배포

```bash
# App Engine Python 라이브러리 설치 및 업데이트
gcloud components install app-engine-python

# YAML 파일 배포
gcloud app deploy app.yaml

# v1과 v2 버전의 서비스를 중지
gcloud app versions stop v1 v2
```

* deploy 명령은 yaml 파일이 있는 디렉토리에서 반드시 실행해야 함
* `gcloud app deploy` 명령의 optional 파라미터
  * `--version`, 커스텀 버전 ID를 지정
  * `--project`, 어플리케이션에서 사용할 projectID를 지정
  * `--no-promote`, 트래픽 라우팅없이 어플리케이션 배포

## App Engine 어플리케이션 확장

* Dynamic 인스턴스
  * * App Engine은 부하를 기반으로 필요한만큼 인스턴스를 자동적으로 추가하거나 삭제
  * 인스턴스 부하가 낮을 때, 중지하여 비용을 최적화
  * Auto Scaling
    * YAML 파일에 `automatic_scaling`을 포함하고, 아래 key-value 쌍을추가
      * target_cpu_utilization
        * 최대 CPU 사용량 지정
      * target_throughput_utilization
        * 동시 요청의 최대 수를 지정
        * 0.5에서 0.95 사이로 지정
      * max_concurrent_requests
        * 인스턴스가 수용할 수 있는 최대 동시 요청
        * 기본값은 10이고, max는 80
      * max_instances
      * min_instances
        * 어플리케이션을 실행할 수 있는 인스턴스의 최대/최소 수
      * max_pending_latency
      * min_pending_latency
        * 요청이 queue에서 처리되기 기다리는 최대/최소 시간
    * 기본적인 스케일링을 지원하는 basic scaling
      * idle_timeout
      * max_instances
* Resident 인스턴스
  * 항상 실행되도록 인스턴스를 설정
  * Manual scaling

### App Engine 버전간 트래픽 분산

* App Engine에서 트래픽을 분산하는 3가지 방법
  * IP주소 기반
    * 고정성을 제공하여 최소 IP주소가 변경되지 않는 한 클라이언트는 항상 동일한 분할로 라우팅
    * App Engine은 Hash를 생성
    * 클라이언트의 IP가 변경되는 경우 문제가 발생할 수 있음
  * HTTP 쿠키 기반
    * 사용자를 버전에 지정하려고할 때 유용
    * GOOGAPPUID라는 이름의 HTTP Request 헤더에 0에서 999사이의 hash 값이 포함
    * 사용자의 IP가 변경되어도 앱의 동일한 버전으로 접근
    * GOOGAPPUID가 없으면 트래픽은 랜덤하게 라우팅
  * 랜덤 선택 기반
    * 워크로드를 고르게 분산하려고할 때 유용

```bash
# 트래픽 분산 명령
gcloud app services set-traffic [SERVICE_NAME] --splits v1=.4,v2=.6
```

* `gcloud app services set-traffic` 명령의 파라미터
  * `--migrate`
    * App Engine이 이전 버전에서 신규 버전으로 트래픽을 이관해야한다는 것
  * `--split-by`
    * IP나 쿠키 중 하나를 사용하여 트래픽을 분산하는 방법을 지정
    * `ip`, `cookie`, `random`

[맨 위로](#chapter-9-computing-with-app-engine)

[본문 보기](../Chapter_9.md)