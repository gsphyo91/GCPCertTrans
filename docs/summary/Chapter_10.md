# Chapter 10 Computing with Cloud Functions

## Cloud Functions 소개

* GCP에서 제공하는 Serverless Compute 서비스
* App Engine과의 차이점
  * App Engine은 하나의 어플리케이션으로 구성된 다수의 서비스를 지원
  * Cloud Functions은 다른 서비스와 독립적으로 관리되고 운영하는 개별적인 서비스

### Event, Trigger, and Functions

* Cloud Functions에서 알아야할 용어
  * Events
    * Cloud Storage에 파일이 업로드된 것과 같이 구글 클라우드에서 일어나는 특정 동작
    * 각 이벤트와 관련된 다양한 종류의 동작이 있고, 현재는 5가지 카테고리의 이벤트를 지원
      * Cloud Storage
        * 파일 업로드, 삭제, 보관
      * Cloud Pub/Sub
        * 메시지 Publish
      * HTTP
        * POST, GET, PUT, DELETE, OPTIONS
      * Firebase
        * 데이터베이스 트리거, 인증 트리거
      * Stackdriver Logging
        * Logging 변화에 응답
  * Triggers
    * 이벤트에 응답하는 방법
  * Functions
    * 이벤트에 대한 데이터를 argument로 전달

### Runtime 환경

* Function이 호출될 때마다 다른 호출로부터 분리된 인스턴스에서 실행
* Function의 상태에 대한 정보를 유지해야한다면, Cloud Datastore나 Cloud Storage의 파일과 같은 데이터베이스를 사용해야 함
* [Function 예시](../Chapter_10.md/###-Runtime-환경)

## Cloud Storage에서 이벤트를 수신하는 Cloud Functions

### Cloud Console을 사용하여 Cloud Storage Event를 위한 Cloud Functions 배포

* Cloud Console - Cloud Functions
* 신규 함수 생성 옵션
  * Function name
  * Memory allocated for the function
    * 함수를 사용할 수 있는 메모리의 양
    * 128MB에서 2GB 범위
  * Trigger
    * HTTP, Cloud Pub/Sub, Cloud Storage와 같이 정의된 트리거중 하나
  * Event type
  * Source of the function code
    * 소스코드를 찾을 위치
      * Cloud Storage, Cloud Source Repository 등
  * Runtime
    * 코드를 실행하는데 사용할 런타임
  * Source code
  * Python, Go or Node.js function to execute
    * 이벤트가 발생할 때 실행해야할 코드에서 함수의 이름
* 함수 코드를 포함한 파일 업로드

### gcloud 명령을 사용하여 Cloud Storage 이벤트를 위한 Cloud Function 배포

```bash
# gcloud 명령 업데이트
gcloud components update

# functions 배포
gcloud functions deploy

# `gcp-ace-exam-test-bucket` 버킷에 새로운 파일이 업로드될 때마다 `cloud_storage_function_test`를 실행하는 명령
gcloud functions deploy cloud_storage_function_test --runtime python37 --trigger-resource gcp-ace-exam-test-bucket --trigger-event google.storage.object.finalize

# function 삭제
gcloud functions delete [FUNCTIONS_NAME]
```

* function 배포 명령의 파라미터
  * `runtime`
  * `trigger-resource`
    * 트리거와 관련된 버킷의 이름
  * `trigger-event`
    * 함수 실행을 트리거하는 이벤트의 종류
      * google.storage.object.finalize
        * 파일이 완전히 업로드 되었을 때
      * google.storage.object.delete
      * google.storage.object.archive
      * google.storage.object.metadataUpdate

## Pub/Sub으로부터 이벤트를 수신하는 Cloud Functions

### Cloud Console을 사용하여 Cloud Pub/Sub 이벤트를 위한 Cloud Functions 배포

* Cloud Console - Cloud Functions - 함수 생성
* [Function 예시](../Chapter_10.md/###-Cloud-Console을-사용하여-Cloud-Pub/Sub-이벤트를-위한-Cloud-Function-배포)

[맨 위로](#chapter-10-computing-with-cloud-functions)

[본문 보기](../Chapter_10.md)