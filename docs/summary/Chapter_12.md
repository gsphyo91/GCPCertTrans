# Chapter 12 GCP에서 스토리지 배포

## Cloud SQL 배포 및 관리

* Cloud SQL은 관리형 relational 데이터베이스 서비스

### MySQL 인스턴스 생성 및 연결

* Console - SQL - Create Instance - MySQL
* 데이터베이스 연결
  * 생성 후, Cloud Shell에서 `gcloud sql connect` 입력
    * 연결할 인스턴스 이름과 선택적으로 username, password 입력

```bash
// 인스턴스 연결 명령
gcloud sql connect [INSTANCE_NAME]
```

### 데이터베이스 생성, 데이터 로딩 및 쿼리

* MySQL Command Line은 `gcloud`가 아닌 MySQL 명령 사용
* 데이터베이스 생성
  * `CREATE DATABASE`
* 데이터베이스 사용
  * `USE [DATABASE_NAME]`
* 테이블 생성
  * `CREATE TABLE`
* 데이터 삽입
  * `INSERT INTO [TABLE] (Column) VALUES [VALUE]`
* 테이블 쿼리
  * `SELECT [Column] FROM [TABLE]`

### Cloud SQL에서의 MySQL 백업

* Cloud SQL은 on-demand와 automatic backups 모두 적용 가능
* on-demand 백업 생성
  * Console - Cloud SQL Instances - Instance 이름 클릭 - Backup 탭
  * Create Backup
  * Command Line
    * `gcloud sql backups`
    ``` bash
    gcloud sql backups create --async --instance [INSTANCE_NAME]
    ```
* automatic backups
  * Console - Cloud SQL Instance - Instance 이름 클릭 - Edit Instance - Enabled Auto Backups
    * 백업을 생성하는 시기 설정
      * 자동 백업이 발생하는 시간 범위
    * 특정 시점 복구 기능 활성화 가능
  * Command Line
  ```bash
  gcloud sql instances patch [INSTANCE_NAME] -backup-start-time [HH:MM]
  ```

## Datastore 배포 및 관리

### Datastore 데이터베이스에 데이터 추가

* Console - Datastore - Entities 옵션 - Create Entity
  * Entities는 관계형 데이터베이스의 스키마와 유사
  * Kind는 관계형 데이터베이스에서 테이블과 유사
* Entity를 생성한 후, SQL과 유사한 GQL을 사용하여 document 데이터베이스에 쿼리

### Datastore 백업

* 백업파일을 저장하는 Cloud Storage 버킷을 생성해야하고, 백업을 수행하는 사용자에게 적절한 권한을 부여해야 함
  * `datastore.databases.export` 권한이 필요
  * 데이터가 인입되는 경우, `datastore.databases.import` 필요

```bash
// 백업을 위한 버킷 생성
gsutil mb gs://[BUCKET_NAME]/

// Cloud Datastore Import Export Admin role을 갖는 사용자가 백업을 생성
gcloud -namespace='[NAMESPACE]' gs://[BUCKET_NAME]

// 백업 파일을 추가
gcloud datastore export -namespaces='[NAMESPACE]' gs://[BUCKET_NAME]

// 백업 파일을 추가
gcloud datastore import gs://[BUCKET]/[PATH]/[FILE].overall_export_metadata
```

## BigQuery 배포 및 관리

* BigQuery는 fully 관리형 데이터베이스 서버
  * 구글은 백업을 관리하고, 다른 기본적인 관리 업무를 수행

### BigQuery에서 쿼리의 비용 측정

* Console - BigQuery
* Query Editor에 쿼리를 입력하면, 오른쪽 아래에 얼마나 많은 데이터가 스캔될지 측정 값을 제공
  * `bq` 명령에 `--dry-run` 옵션을 함께 사용하면 측정 값을 얻을 수 있음
  ```bash
  bq --location=[LOCATION] query --use_legacy_sql=false --dry-run [SQL_QUERY]
  ```

### BigQuery의 Job 확인

* Jobs는 데이터 로드, 추출, 복사, 쿼리에 사용되는 프로세스
* BigQuery 콘솔 - Job History
  * Green : 성공적으로 완료된 job
  * Red : 실패된 job
* `bq show` 명령을 통해 BigQuery job의 상태를 확인할 수 있음

## Cloud Spanner 배포 및 관리

* Console - Cloud Spanner - Create Instance - Instance Detail - Create Database
* 데이터베이스를 생성할 때, 테이블 구조를 정의하는 SQL Data Definition Language(DDL)을 사용
* Cloud Spanner는 관리형 데이터베이스 서비스
  * 패치, 백업, 기본적인 데이터 관리업무를 수행하지 않아도 됨

## Cloud Pub/Sub 배포 및 관리

* Pub/Sub 메시지 큐를 배포하기 위해 필요한 작업
  * 토픽 생성
    * 어플리케이션이 메시지를 보낼 수 있는 구조
    * Pub/Sub은 메시지를 수신하고, 어플리케이션에 의해 읽혀지기 전까지 메시지를 유지
  * 구독 생성
    * 어플리케이션은 구독에 의해서 메시지를 읽음
* Console - Pub/Sub - Create a Topic - Subscription 추가
* 구독 생성
  * 구독 이름
  * 전송 타입
    * Pull
      * 어플리케이션이 토픽을 읽음
    * Push
      * 어플리케이션에 메시지를 작성
      * 메시지를 수신할 엔드포인트의 URL을 지정
* Pub/Sub에 메시지가 publish되면, 메시지를 읽어야하는 어플리케이션은 메시지를 수신
* Pub/Sub은 Acknowledgement Dedline 파라미터를 지정하여 대기
  * 10에서 600초 범위
* 전달할 수 없을 때, 메시지를 유지하는 시간인 보유기간을 지정할 수 있음
  * 보유기간이 지나면 메시지는 토픽에서 삭제
* Command Line

```bash
gcloud pubsub topics create [TOPIC_NAME]
gcloud pubsub subscriptions create [SUBSCRIPTION_NAME] --topic [TOPIC_NAME]
```

## Cloud Bigtable 배포 및 관리

* Bigtable 인스턴스 생성
  * Console - Bigtable - Create Instance
* Command Line
  * 테이블을 생성하기 위해 `cbt` 명령을 설치
    * `cbt`는 테이블 생성, 데이터 삽입, 테이블 쿼리를 위한 서브명령 보유

| Command | Description |
| --- | --- |
| `createtable` | 테이블 생성 |
| `createfamily` | column family 생성 |
| `read` | Reads and displays row |
| `ls` | Lists tables and columns |

```bash
//cbt 명령 설치
gcloud components update
gcloud components install cbt

//환경변수를 .cbt 설정 파일에 설정
echo instance=[INSTANCE_NAME] >> ~/.cbtrc

//테이블 생성
cbt createtable [TABLE_NAME]

//테이블 조회
cbt ls

//column families 생성
cbt createfamily [TABLE_NAME] [COLUMN_FAMILY_NAME]

//row에 column이 있는 셀 값을 설정
cbt set [TABLE_NAME] [ROW_NAME] [COLUMN_FAMILY_NAME]:[COLUMN_NAME]=[VALUE_NAME]

//테이블의 컨텐츠 확인
cbt read [TABLE_NAME]
```

## Cloud Dataproc 배포 및 관리

* Cloud Dataproc은 관리형 Apache Spark와 Apache Hadoop 서비스
  * Spark는 분석과 머신러닝을 지원
  * Hadoop은 배치, 빅데이터 어플리케이션에 적합
* 클러스터 생성
  * Console - Dataproc - Create Cluster
  * 클러스터 이름, region, zone 지정
  * 클러스터 모드
    * single node
      * 개발에 유용
    * standard
      * 오직 하나의 마스터노드를 갖음
    * high availability
      * 3개의 마스터를 사용
  * 머신 설정 정보를 지정
    * CPU, 메모리, 디스크정보 지정
  * 워커노드의 수 지정
  * Preemptible VM 수 지정
* Jobs를 지정
  * Job을 실행할 클러스터와 Spark, PySpark, SparkR, Hive, Spark SQL, Pig, Hadoop 중에서 job의 타입을 지정
  * Main Class나 .jar은 실행할 때 호출되어야하는 함수나 메소드의 이름
    * .jar파일은 실행될 Java 프로그램
* Command Line

```bash
//클러스터 생성
gcloud dataproc clusters create [CLUSTER_NAME] --zone [ZONE]

//job 적용
gcloud dataproc jobs submit spart --cluster [CLUSTER_NAME] --jar [JAR_FILE]
```

## Cloud Storage 관리

* 스토리지 클래스 수동 변경

```bash
//스토리지 클래스 수동 변경
gsutil rewrite -s [STORAGE_CLASS] gs://[PATH_TO_OBJECT]

//버킷 간 Object 이동
gsutil mv gs://[SOURCE_BUCKET_NAME]/[SOURCE_OBJECT_NAME] \ gs://[DESTINATION_BUCKET_NAME]/[DESTINATION_OBJECT_NAME]
```

[맨 위로](#chapter-12-gcp%ec%97%90%ec%84%9c-%ec%8a%a4%ed%86%a0%eb%a6%ac%ec%a7%80-%eb%b0%b0%ed%8f%ac)

[본문 보기](../Chapter_12.md)