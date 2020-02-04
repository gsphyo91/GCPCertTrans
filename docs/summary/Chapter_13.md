# Chapter 13 스토리지에서 데이터 로드

## Cloud Storage에 데이터 로딩 및 이동

* Cloud Storage는 장기간 스토리지, 보관, 파일 전송, 데이터 공유를 포함한 다양한 사례에서 사용됨

### 콘솔을 사용하여 Cloud Storage 데이터 로딩 및 이동

* Console - Cloud Storage
* 버킷 생성
  * 스토리지 클래스
  * region

### 커맨드라인을 사용하여 Cloud Storage로 데이터 로딩 및 이동

* `gsutil` 명령 사용

```bash
# 버킷 생성
gsutil mb gs://[BUCKET_NAME]

# 파일 업로드
gsutil cp [LOCAL_OBJECT_LOCATION] gs://[DESTINATION_BUCKET_NAME]

# 파일 다운로드
gsutil cp gs://[DESTINATION_BUCKET_NAME] [LOCAL_OBJECT_LOCATION]

# 파일 move
gsutil mv gs://[SOURCE_BUCKET_NAME]/[SOURCE_OBJECT_NAME] \ gs://[DESTINATION_BUCKET_NAME]/[DESTINATION_OBJECT_NAME]
```

## 데이터 입력 및 추출

### 데이터 입력 및 추출: Cloud SQL

* Console - Cloud SQL - Instance Detail - Import/Export
* SQL이나 CSV 포맷 선택
  * SQL은 다른 관계형 데이터베이스에 데이터를 입력할 계획이면 유용
  * CSV는 비관계형 데이터베이스에 데이터를 이동할 경우 유용
* Command Line

```bash
# service account가 버킷에 쓸 수 있는지 확인
gcloud sql instances describe [INSTANCE_NAME]

# service account가 버킷에 접근할 수 있도록 버킷의 접근 제어 변경
gsutil acl ch -u [SERVICE_ACCOUNT_ADDRESS]:W gs://[BUCKET_NAME]

# 데이터베이스 export
gcloud sql export [sql/csv] [INSTANCE_NAME] gs://[BUCKET_NAME]/[FILE_NAME] --database=[DATABASE_NAME]

# 데이터베이스 import
gcloud sql import sql [INSTANCE_NAME] gs://[BUCKET_NAME]/[IMPORT_FILE_NAME] --database=[DATABASE_NAME]
```

### 데이터 입력 및 추출: Cloud Datastore

* Datastore에서 데이터 입력 및 추출은 커맨드라인을 통해 수행
* 추출된 엔티티를 그룹화하는 namespace 데이터 구조를 사용
  * 디폴트 namespace는 `default`

```bash
# 데이터 export
gscloud datastore export --namespace="(default)" gs://${BUCKET}

# 데이터 import
gcloud datastore import gs://${BUCKET}/[PATH]/[FILE].overall_export_metadata
```

### 데이터 입력 및 추출: BigQuery

* Console과 커맨드라인을 사용하여 테이블 입력 및 추출
* Console - BigQuery 
  * export
    * Resource 아래에 데이터 셋 - 테이블 description - export
    * 추출 장소
      * Google Cloud Storage
        * 추출 파일을 저장할 버킷 입력
        * 파일 포맷 선택
          * CSV, Avro, JSON
        * 압축타입 선택
          * None
          * CSV를 위한 Gzip
          * deflate
          * Avro를 위한 snappy
      * GCP의 분석도구인 DataStudio
  * import
    * 데이터 셋 선택 - Create Table
      * 원본 테이블
      * 타켓 테이블
      * 데이터 셋 이름
      * 테이블 타입
        * native
        * external
          * 데이터는 원본 위치에 유지되고, 테이블에 대한 메타데이터만 저장
      * 테이블 이름
      * 포맷 지정
        * CSV, JSON, Avro, Parquet, PRC, Cloud Datastore 백업
* 커맨드 라인

```bash
# 데이터 export
bq extract --destination_format [FORMAT] --compression [COMPRESSION_TYPE] --field_delimiter [DELIMITER] --print_header [BOOLEAN] [PROJECT_ID]:[DATASET].[TABLE] gs://[BUCKET]/[FILENAME]

# 데이터 import
bq load --autodetect --source_format=[FORMAT] [DATASET].[TABLE] [PATH_TO_SOURCE]
```

### 데이터 입력 및 추출: Cloud Spanner

* Console - Cloud Spanner
* Export
  * 인스턴스 이름 클릭 - Export 클릭
    * 타켓 버킷, 추출할 데이터베이스, job을 실행할 region 입력
      * Dataflow를 실행하기 위해 요금이 부과되는지 확인
* Import
  * 인스턴스 detail 페이지에서 import 클릭
  * 원본 버킷, 타켓 데이터베이스, job을 실행할 region 지정

### 데이터 입력 및 추출: Cloug Bigtable

* Cloug Bigtable은 Cloud Console이나 `gcloud`의 Export와 import 옵션이 없음
* 입력과 추출을 위한 java 어플리케이션이나 HBase 인터페이스를 사용

```bash
# Bigtable export
# Java VM을 위해 컴파일된 프로그램인 JAR 파일을 다운로드
curl -f -O http://repo1.maven.org/maven2/com/google/cloud/bigtable/bigtable-beam-import/1.6.0/bigtable-beam-import-1.6.0-shaded.jar

# 추출 프로그램 실행
java -jar bigtable-beam-import-1.6.0-shaded.jar export \
    --runner=dataflow \
    --project=[PROJECT_ID] \
    --bigtableInstanceId=[INSTANCE_ID] \
    --bigtableTableId=[TABLE_ID] \
    --destinationPath=gs://[BUCKET_NAME]/[EXPORT_PATH] \
    --tempLocation=gs://[BUCKET_NAME]/[TEMP_PATH] \
    --maxNumWorkers=[10x_NUMBER_OF_NODES] \
    --zone=[DATAFLOW_JOB_ZONE]

# Bigtable import
java -jar bigtable-beam-import-1.6.0-shaded.jar import \
    --runner=dataflow \
    --project=[PROJECT_ID] \
    --bigtableInstanceId=[INSTANCE_ID] \
    --bigtableTableId=[TABLE_ID] \
    --sourcePattern='gs://[BUCKET_NAME]/[EXPORT_PATH]/part-*' \
    --tempLocation=gs://[BUCKET_NAME]/[TEMP_PATH] \
    --maxNumWorkers=[3x_NUMBER_OF_NODES] \
    --zone=[DATAFLOW_JOB_ZONE]
```

### 데이터 입력 및 추출: Cloud Dataproc

* Cloud Dataproc은 Cloud SQL이나 Bigtable과 같은 데이터베이스가 아니고, 데이터 분석 플랫폼
* 데이터 저장 및 조회보다 데이터 조작, 통계 분석, 머신러닝 등을 위해 설계
* 분석하고자하는 데이터파일을 저장하는데 Cloud Storage와 Persistent Disk를 사용해야 함

```bash
# Dataproc 클러스터 설정 export
gcloud dataproc clusters export [CLUSTER_NAME] --destination=[PATH_TO_EXPORT_FILE]

# import
gcloud dataproc clusters import [SOURCE_FILE]
```

## Pub/Sub에 데이터 스트리밍

* 토픽으로 메시지 생성, 구독으로 메시지 수신

```bash
# 토픽 생성
gcloud pubsub topics create [TOPIC_NAME]

# 구독 생성
gcloud pubsub subscriptions create --topic [TOPIC_NAME] [SUBSCRIPTION_NAME]

# 토픽에 데이터 전송
gcloud pubsub topics publish [TOPIC_NAME] --message [MESSAGE]

# 구독으로 메시지 수신
gcloud pubsub subscriptions pull --auto-ack [SUBSCRIPTION_NAME]
```

[맨 위로](#chapter-13-%ec%8a%a4%ed%86%a0%eb%a6%ac%ec%a7%80%ec%97%90%ec%84%9c-%eb%8d%b0%ec%9d%b4%ed%84%b0-%eb%a1%9c%eb%93%9c)

[본문 보기](../Chapter_13.md)