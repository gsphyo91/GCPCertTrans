# Chapter 18 모니터링, 로깅, 비용 예측

## Stackdriver 모니터링

* Stackdriver는 리소스의 성능 지표, 로그, 이벤트 데이터를 수집하기 위한 서비스
* Stackdriver는 GCP, AWS, On-Prem 리소스를 지원하는 하이브리드 환경에서 작동

## 리소스 지표를 기반으로 알림 생성

* 지표는 정기적으로 수집되는 리소스의 측정치
    * 최대값, 최소값, 측정된 아이템의 평균값 같은 집계값을 리턴
* 지표를 모니터링하고 수집하기 위해, 모니터링을 위한 Stackdriver agent를 설치
* 모니터링 agent를 설치하면, 동시에 로깅 agent를 설치
* agent 설치 Command line
    ```bash
    curl -sSO https://dl.google.com/cloudagents/install-monitoring-agent.sh
    sudo bash install-monitoring-agent.sh
    curl -sSO https://dl.google.com/cloudagents/install-logging-agent.sh
    sudo bash install-logging-agent.sh --structured
    ```
* Console - Stackdriver Monitoring - Workspace 생성
    * 모니터링하는 프로젝트 선택
    * 이메일 리포트 생성
* 지표 모니터링 정책 생성 (Create Policy)
    * 정책은 알람이나 경고가 발생할 시기를 결정하는 조건으로 구성
    * Add Condition
        * 조건 파라미터를 지정
        * 필터 지정
        * Group By
            * 시간열이나 정기적으로 생성되는 데이터를 그룹화
            * 버킷에 들어오는 데이터에 적용될 수 있는 min, max, mean 등의 함수를 포함
        * reducer 지정
            * 시계열로 그룹화된 값을 하나의 값으로 생성하기 위한 함수
            * sum, min, max, count 등
        * 조건이 트리거되는 시기 지정
            * 특정 임계치를 초과하는 값이 있거나 측정된 값이 장기간동안 임계치를 초과하는 경우
        * 알림 채널
            * 이메일, Slack, Google Cloud Console(Mobile), PagerDuty, HipChat, Campfire
        * Document
            * 엔지니어가 문제를 이해하고, 이슈를 해결하는 방법에 대한 정보를 제공

### 커스텀 지표 생성

* 커스텀 메트릭은 사정 정의된 메트릭과 비슷하지만, 생성한 측정 항목을 제외
* 커스텀 메트릭 생성 방법
    * 오픈소스 모니터링 라이브러리 OpenCensus
        * Higher-level, Monitoring-focused API 제공
    * Stackdriver의 Monitoring API 사용
        * Lower-level
* 커스텀 메트릭 정의 항목
    * 프로젝트 내에서 유니크한 타입 이름
    * 프로젝트
    * 표시된느 이름, 설명
    * Gauge, Delta, Cumulative Metric같은 메트릭 종류
        * Gauge, 특정 시점에 측정
        * Delta, 간격에 따른 변화를 캡쳐
        * Cumulative, 간격에 걸쳐 누적된 값
    * 메트릭 label
    * 시계열 데이터 포인트에 포함할 모니터링 리소스 Object

## Stackdriver 로깅

* GCP와 AWS에서 생성되는 로그와 이벤트 데이터를 수집, 저장, 필터링, 조회
* 관리형 서비스로, 서버를 구성하거나 배포할 필요가 없다.
* 로깅의 3가지 작업
    * Log Sinks 구성
    * 로그 조회 및 필터링
    * 메시지 상세정보 조회

### Log Sinks 구성

* 로깅은 로그 데이터를 30일동안 유지
    * 장기간 저장해야할 경우, Cloud Storage나 BigQuery와 같은 장기간 스토리지 시스템에 로깅 데이터를 추출하는게 가장 적합
* 스토리지 시스템으로 데이터를 복사하는 프로세스는 Exporting
* 로그데이터를 작성하는 위치는 Sink
* Log Sink 생성
    * Console - Logging - Export - Create Export
    * 파라미터 입력
        * Sink name
        * Sink Service
            * BigQuery
                * Sink Destination은 BigQuery Data Set
                * 로그 이름과 타임스탬프를 기반으로 테이블 생성
                * ex. syslog_20190102
            * Cloud Storage
                * 기존 버킷이나 새로운 버킷에 로그를 추출할 수 있음
                * 파일 셋을 Sink Bucket에 작성
                * 파일은 로그 타입과 날짜의 계층으로 구성
                * ex. `[BUCKET_NAME]/syslog/2019/01/02`
            * Cloud Pub/Sub
                * 토픽을 생성하거나 기존 토픽을 사용하여 선택
                * 데이터는 LogEntry로 알려진 Object 구조에 Base64 인코딩
                * LogEntry는 `type`, `instance_id`, `zone`, `project_id`와 같은 프로퍼티를 포함
            * Custom Destination
                * Sink를 호스팅하는 현재 프로젝트 이외의 프로젝트의 이름을 지정
        * Sink Destination

### 로그 조회와 필터링

* Console - Stackdriver - Logging
* 로그 메시지 필터링
    * Label or Text
    * Resource Type
        * resource, VM instance, Subnetworks, Project, Databases를 포함한 GCP 리소스 타입의 리스트 제공
    * Log Type
    * Log Level
        * Error, Info, Warning, Debug와 같은 로그 메시지 레벨
    * Time Limit

### 메시지 상세 정보 조회

* 각 로그 항목의 삼각형 아이콘을 클릭하여 확장 가능

## Cloud 진단 사용

* 소프트웨어 개발자가 어플리케이션의 성능과 작동에 대한 정보를 수집할 수 있는 진단 툴 제공
* Cloud Trace, Cloud Debug

### Cloud Trace Overview

* 어플리케이션으로부터 데이터 지연 시간을 수집하기위한 분산 추적 시스템
* Cloud Trace 콘솔에서 프로젝트에서 실행 중인 어플리케이션에서 생성되는 trace를 나열할 수 있음
* Cloud Trace는 개발자와 DevOps 엔지니어가 병목현상을 발생하는 코드 영역을 식별하는데 도움을 주는 분산 추적 어플리케이션

### Cloud Debug Overview

* Cloud Debug는 실행 중인 프로그램의 상태를 점검하기 위한 어플리케이션 디버거
* 개발자가 로그 상태를 입력하거나 어플리케이션의 상태를 스냅샷으로 만들 수 있음
* Cloud Debug는 프로그램이 실행되는 동안 상태의 스냅샷을 생성하는데 사용
    * 소스코드 변경없이 로그 메시지를 입력할 수 있음

### GCP 상태 조회

* Google Cloud Status 대시보드에서 GCP 상태를 확인
* Console - Google Cloud Platform Status

## Pricing Calculator 사용

* 사용자가 선택한 서비스의 리소스 설정과 관련된 비용을 이해하는데 도와주는 도구
* 리소스 구성, 리소스 사용기간, 스토리지의 경우 저장할 데이터의 양을 지정
* VM의 비용을 측정하기 위한 파라미터
    * instance 수
    * machine type
    * os
    * 일, 주별 평균 사용량
    * persistent disks
    * load balancing
    * Cloud TPUs
* Pricing Calculator는 다양한 서비스의 가격을 측정할 수 있고, 모든 서비스를 위한 전체 측정치를 생성

[맨 위로](#chapter-18-%eb%aa%a8%eb%8b%88%ed%84%b0%eb%a7%81-%eb%a1%9c%ea%b9%85-%eb%b9%84%ec%9a%a9-%ec%98%88%ec%b8%a1)

[본문 보기](../Chapter_18.md)