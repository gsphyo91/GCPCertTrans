# Chapter 11 클라우드에서 Storage 기획

## 스토리지 시스템 유형

* 스토리시 솔루션을 선택할 때의 주요 고민은 데이터에 접근하는 시간
* GCP에서 제공하는 스토리지 서비스
  * 캐싱을 위한 관리형 Redis 캐시 서비스
  * VM 사용을 위한 Persistent Disk Storage
  * 리소스 전반적으로 파일에 공유된 접근을 위한 Object Storage
  * 장기간, 비주기적 접근 요구사항을 위한 Archival Storage

### Cache

* 데이터에 수 밀리초 접근을 제공하도록 설계된 in-memory 데이터 저장소
* 캐시는 이용할 수 있는 데이터의 양이 제한
* 캐시를 호스팅한 머신이 중지되면 캐시의 컨텐츠는 삭제됨

#### MemoryStore

* GCP의 관리형 Redis 서비스
* Redis와 프로토콜 호환이 되기 때문에, Redis 작업을 위해 작성된 툴과 어플리케이션은 MemoryStore와 사용할 수 있음
* MemoryStore를 사용할 때
  * Redis를 실행할 인스턴스를 생성
    * 인스턴스는 1GB에서 300GB 메모리로 구성
    * HA를 구성할 수 있음

#### Configuring MemoryStore

* Memorystore 캐시는 Compute Engine, App Engine, Kubernetes Engine에서 실행하는 어플리케이션과 사용될 수 있음
* Console - Memorystore - Redis instance 생성 옵션 선택
* Memorystore 설정 파라미터
  * Instance ID
  * Display Name
  * Redis Version
  * Instance Tier
    * Basic
      * Replica를 포함하지 않지만 비용이 적음
    * Standard
      * HA를 위해 다른 zone에 Replica를 갖음
  * Region, Zone
  * Size
  * Network
    * 다른 네트워크를 지정하지 않으면 기본 네트워크에서 접근
  * Labels
  * IP주소가 지정될 IP 범위

### Persistent Storage

* 내구성있는 block storage
* Persistent Disk는 GCE와 GKE의 VM에 연결될 수 있음
* VM을 호스팅한 물리서버에 직접 연결되지 않고, 네트워크로 접근
* Persistent Disk의 데이터는 VM이 중지된 후에도 계속 존재
* VM과 독립적으로 존재

#### Persistent Disk의 기능

* SSD와 HDD 설정을 사용할 수 있음
  * SSD
    * 높은 대역폭이 중요할 때 사용
    * 랜덤 액세스와 순차적 액세스 패턴을 위한 일관된 성능을 제공
  * HDD
    * 지연시간이 더 길지만 비용이 적음
    * 대량의 데이터를 저장하고, 지연시간이 덜 민감한 배치를 수행할때 적합
* Multireader 스토리지를 지원하여 다양한 VM에 마운트될 수 있음
* 디스크의 스냅샷을 생성할 수 있음
  * 디스크의 복사본은 다른 VM에서 사용하기 위해 분배
  * 스냅샷으로 생성된 디스크가 VM에 마운트되면, Read/Write 동작을 모두 지원
* VM에 마운트되어있는 동안 사이즈를 증가할 수 있음
  * SSD와 HDD는 모두 64TB까지 증가할 수 있음
* 디스크의 데이터가 자동적으로 암호화
* 디스크가 zonal인지 regional인지 고려해야 함
  * zonal
    * 하나의 zone에 다양한 물리 드라이버 전체적으로 데이터를 저장
    * zone을 이용할 수 없게되면, 디스크에 접근할 수 없음
  * regional
    * region 내 2개의 zone에 걸쳐서 데이터 블록을 복사
    * zonal보다 비용이 비쌈

#### Persistent Disk 설정

* Console - Compute Engine - Disk
* Persistent Disk 생성 파라미터
  * Disk Name
  * Description
  * Type
    * Standard
    * SSD Persistent Disk
  * HA를 위한 Region내 Replica 생성 옵션
  * Region, Zone
  * Label
  * Source Type
    * 공백
    * 이미지
    * 스냅샷
  * Encryption
    * GCP에서 암호화 키를 관리
    * GCP의 Cloud Key Management Service를 사용하여 키 관리
      * Key Management Service에 생성될 Key 이름 지정
      * GCP의 Key Repository에 키를 저장
    * Customer-Supplied Key
      * 다른 키 관리 시스템을 사용하여 키를 생성하고 관리

### Object Storage

* Exabyte 급의 데이터를 저장해야하고, 넓에 공유해야할 때 적합한 Object Storage
* GCP에서는 Cloud Storage

#### Cloud Storage의 기능

* Cloud Storage는 Object 스토리지 시스템
* 시스템에 저장되는 파일은 최소 단위로 취급
  * 파일의 한 섹션만 읽는 것같이 파일의 부분만 조작할 수 없음
* Object를 생성/삭제를 수행할 수 있지만, 파일의 하위 구성요소를 조작할 수 없음
* 동시성과 잠금을 지원하지 않음
  * 다수의 클라이언트가 파일을 작성하면, 마지막 데이터가 저장
* 데이터 구조에 대한 요구없이 대량의 데이터를 저장하기위해 적합
* Bucket
  * Cloud Storage의 논리적 구성 단위
  * 프로젝트 내의 리소스
  * namespace를 글로벌 단위로 공유
    * Bucket의 이름은 globally 유니크해야 함
* Object Storage는 파일 시스템을 제공하지 않음
* Bucket은 객체를 그룹으로 구성
  * 디렉토리와 유사하지만, 디렉토리는 아님
* Cloud Storage에서 제공하는 등급
  * Multi-regional
  * regional
  * nearline
  * coldline

#### Multiregional과 Regional 스토리지

* 버킷을 생성할 때, 버킷을 생성할 Region을 지정
* Multiregional 버킷
  * 99.95%의 SLA
  * 99.99%의 가용성 제공
  * 다수의 Region에 데이터 복사
  * 비용이 높음
* Regional 버킷
  * 99.9%의 SLA
  * 99.99%의 가용성
  * Zone에 걸쳐서 데이터 복사
* Multiregional과 Regional 스토리지는 자주 사용되는 데이터를 위해 사용
  * 한달의 한 번 이상
* 사용자의 위치를 기반으로 regional과 multiregional 중 선택
  * 사용자가 글로벌로 분산되고, 동기화된 데이터에 접근한다면 Multiregional

#### Nearline and Coldline 스토리지

* 자주 접근하지 않는 데이터를 위해 nearline과 coldline 스토리지 클래스가 적합
* Nearline
  * 한 달에 한 번 미만 접근
  * Multiregional 위치
    * 월평균 99.95% 가용성
    * 99.9% SLA
  * Regional 위치
    * 월평균 99.9% 가용성
    * 99.0% SLA
  * 최소 30일의 저장 기간
* Coldline
  * 1년에 한 번 미만 접근
  * Multiregional 위치
    * 월평균 99.95% 가용성
    * 99.9% SLA
  * Regional
    * 월평균 99.9% 가용성
    * 99.0% SLA
  * 최소 90일 이상의 저장

| :: | Regional | Multiregional | Nearline | Coldline |
|---|---|---|---|---|
| Features | 다수의 zone에 복사된 Object Storage | 다수의 region에 복사된 Object Storage | 한 달에 한번미만 접근하는 Object Storage | 1년에 한 번 미만 접근하는 Object Storage |
| Storage 비용 | $0.20/GB/month | $0.26/GB/month | $0.10/GB/month | $0.07/GB/month |
| 액세스 비용 | | | $0.01/GB | $0.05/GB |
| 사용 사례 | 어플리케이션간 공유되는 Object Storage | 공유된 Object를 글로벌 액세스 | 데이터 레이크, 백업의 오래된 데이터 | 문서 보유, 규정 준수 |

### Versioning과 Object LifeCycle 관리

* 버킷은 Object가 변경될 때, Object의 버전을 유지하는데 설정될 수 있음
  * 버킷에 버전관리가 활성화되면, Object의 복사본은 Object가 변경될때마다 보관
  * Object의 최신 버전은 Live 버전
* 자동적으로 Object의 스토리지 등급을 변경하거나 특정기간 뒤에 Object를 삭제하는 lifecycle 관리 정책 제공
  * Lifecycle 정책 Configuration 설정
    * condition이 true면, action을 실행
    * Condition
      * Object가 특정 age에 도달하면, 삭제되거나 더 적은 비용의 스토리지 등급으로 이동
      * 버전이 Live인지 아닌지
      * 특정 날짜 이전에 생성되었는지
      * 특정 스토리지 등급에 있는지
  * Lifecycle 정책이 버킷에 적용되고, 버킷은 Object에 영향
* Lifecycle 관리를 사용하여 Object 스토리지 등급 변경
  * Multiregional과 Regional에서 Nearline이나 Coldline으로 변경 가능
  * Nearline은 오직 Coldline으로만 변경 가능

#### Cloud Storage 설정

* Console - Storage
* 버킷 생성 파라미터
  * Name
  * Storage Class
  * Label
  * Encryption Key
* Lifecycle 정책 정의
  * Console - Storage - Browse - Lifecycle

### Storage 솔루션을 기획할 때 Storage Type

* Storage 솔루션을 기획할 때, 고려할 요소는 데이터에 접근하는데 요구되는 시간
* Memorystore
  * 가장 빠른 접근 시간 제공
  * 사용할 수 있는 메모리 양 제한
  * 휘발성
  * 마지막 특정 시점으로 복구될 수 있도록 정기적으로 캐시의 내용을 Persistent 스토리지에 저장
* Persistent Storage
  * VM에 연결된 디스크와 같이 Block 스토리지 디바이스
  * SSD와 HDD 드라이브 제공
  * SSD는 빠르지만, 비쌈
  * HDD는 대량의 데이터를 저장하지만, 느림
* Object Storage
  * 장기간 대량의 데이터를 저장하는데 사용
  * Regional, Multiregional
  * Lifecycle 관리와 버전관리 지원

## 스토리지 데이터 모델

* GCP에서 사용할 수 있는 데이터 모델
  * Object
  * Relational
  * NoSQL

### Object: Cloud Storage

* Object Storage 데이터 모델은 최소 단위의 Object로 파일을 취급
* 대용량 데이터를 저장하고, Object 내 데이터에 세부 접근이 필요하지 않을 때 사용
* 저장되어야하지만, 장기간 분석되지 않는 데이터를 보관하는데 적합

### Relational: Cloud SQL, Cloud Spanner, BigQuery

* Relational 데이터베이스
  * 빈번한 쿼리와 데이터 업데이트를 지원
  * 사용자에게 일관된 데이터 조회가 중요할 때 사용
  * 데이터베이스 트랜잭션을 지원
    * 트랜잭션은 성공이나 실패를 보장하는 일련의 작업
  * 데이터가 구조화되고, 관계형 데이터베이스로 모델링 되었을 때 사용
* Cloud SQL
  * MySQL과 PostgreSQL 데이터베이스를 제공
  * 클러스터에 수평적 확장이 필요하지 않을 때 사용
  * 실행 중인 서버에 수직적 확장을 제공
* Cloud Spanner
  * 대규모의 관계형 데이터를 갖거나 모든 서버에 일관성과 무결정을 보장하도록 글로벌로 분리해야하는 데이터를 갖을 때 사용
* BigQuery
  * 데이터 웨어하우스와 분석 어플리케이션을 위해 설계
  * 페타바이트급 데이터를 저장하도록 설계
  * 대량의 행과 열 데이터로 작업

#### Cloud SQL 설정

* Console - Cloud SQL

#### Cloud Spanner 설정

* Console - Cloud Spanner
* 설정 파라미터
  * Name
  * ID
  * 노드의 수
  * 노드의 위치
  *   Regional
  *   Multiregional
* Cloud Spanner는 Cloud SQL이나 다른 데이터베이스 옵션보다 많이 비쌈

### BigQuery 설정

* 스토리지와 쿼리, 통계, 머신러닝 분석 툴을 제공하는 관리형 분석 서비스
* 인스턴스를 설정할 필요가 ㅇ벗음
* 데이터를 보유할 Data Set을 생성해야 함
  * Name
  * region
    * 모든 region이 BigQuery를 지원하지 않음

### NoSQL: Datastore, Cloud Firestore, Bigtable

* NoSQL 데이터베이스는 관계형 모델을 사용하지 않고, 고정된 구조나 스키마를 요구하지 않음
* 개발자는 다양한 레코드에 다양한 속성을 저장할 수 있음
* GCP의 3가지 NoSQL 옵션
  * Cloud Datastore
  * Cloud Firestore
  * Cloud Bigtable

#### Datastore 기능

* Datastore는 Document 데이터베이스
  * 스프레드시트나 텍스트파일 같은 문서를 저장하는 의미는 아님
  * document라고 불리는 구조로 구성
  * document는 key-value 쌍의 집합으로 만들어짐
* Datastore에서 Key-Value 쌍을 Entity라고 부름
  * Entity는 공통적인 Properties를 가지만, 모든 Entity가 동일한 Properties를 요구하지 않음
* Datastore는 관리형 데이터베이스
  * 사용자가 서버를 관리하거나 데이터베이스 소프트웨어를 설치할 필요가 없음
  * 자동적으로 데이터를파티셔닝하고, 수요에따라서 스케일업/다운
* Datastore는 비분석적, 비관계형 스토리지 요구를 위해 사용
  * 다양한 Properties를 갖는 유형에 적합
    * ex) 사용자 프로필
* Datastore는 인덱스와 같이 관계형 데이터베이스와 공통적인 기능을 갖음
  * 주요 차이점
    * 고정된 스키마나 구조를 요구하지 않음
    * 테이블 조인이나 sum과 같은 computing 동작을 지원하지 않음

#### Datastore 설정

* Console - Datastore
* 생성 파라미터
  * Namespace
  * Kind
    * 관계형 데이터베이스의 테이블과 유사
  * Key
    * 자동 생성
    * Custom-defined Key
  * Properties
    * Name
    * Type
      * String, data, time, boolean, array
    * Value

#### Cloud Firestore 기능

* document 데이터 모델을 사용하는 관리형 NoSQL 데이터베이스
* Datastore와 유사
* 모바일앱처럼 분리된 어플리케이션간 데이터를 저장, 동기화, 쿼리하기 위해 설계
* 데이터가 백엔드에서 변경되었을 때, 실시간에 가깝기 자동으로 업데이트

#### Cloud Firestore 설정

* 인스턴스를 설정할 필요가 없는 관리형 데이터베이스 서버
* 데이터 스토리지 시스템은 선택해야 함
  * Datastore를 사용
  * Datastore 모드의 Firestore를 사용
  * Native 모드의 Firestore

#### Bigtable 기능

* Wide-column 데이터베이스
  * 대량의 컬럼을 갖을 수 있는 데이블을 저장
  * 모든 행이 모든 열을 사용해야하는 것은 아님
* 페타바이트급의 데이터베이스로 설계
* 일관성과 밀리초 이하의 latency를 제공하도록 설계
* 클러스터에서 실행되고, 수평적으로 확장

#### Bigtable 설정

* Console - Bigtable
* 생성 파라미터
  * 인스턴스 이름
  * ID
  * Instance Type
    * Production
      * 최소 3개의 노드와 HA를 제공
    * Development
      * replication과 HA 제공없는 저비용 인스턴스
  * Storage Type
    * SSD
    * HDD
  * 클러스터 정보
    * Cluster ID
    * Region, Zone
    * Node의 수

## 스토리지 솔루션 선택: 고려해야할 가이드라인

* Read and Write 패턴
  * 데이터가 구조화되어있다면 Cloud SQL같은 스토리지 솔루션이 가장 적합
  * Read/Write 동작을 지원하는 글로벌 데이터베이스는 Cloud Spanner
  * 지속적으로 높은 속도와 대량의 데이터를 쓴다면 Bigtable
  * 파일을 쓰고 파일의 전체를 다운로드한다면 Cloud Storage
* Consistency
  * 일관성은 데이터베이스에서 데이터를 읽는 사용자가 클러스터 서버와 상관없이 같은 데이터를 얻는 것을 보장
  * 강한 일관성이 필요하다면 Cloud SQL과 Cloud Spanner가적합
    * Datastore는 강한 일관성을 설정할 수 있지만, I/O 동작이 오래 걸림
  * Datastore는 데이터가 구조화되어있지 않는 경우 적합
  * NoSQL 데이터베이스는 최소한의 일관성을 제공
    * 짧은 시간동안 동기화되지 않을 수 잇음
* 트랜잭션 지원
  * 최소단위의 트랜잭션을 지원한다면 트랜잭션을 지원하는 데이터베이스를 사용해야 함
    * 어플리케이션 단에서 트랜잭션을 구현할 수도 있지만, 유지보수가 어려움
  * Cloud SQL, Cloud Spanner, Datastore는 트랜잭션을 지원
* 비용
  * 저장되는 데이터의 양, 조회하거나 스캔하는 데이터의 양, 스토리지 시스템의 단위당 요금에 따라 다름
* Latency
  * Latency는 데이터베이스에서 데이터를 읽는 동작의 시작부터 완료되기까지의 시간
  * Bigtable은 밀리초 미만의 동작을 일관되게 제공
  * Spanner는 latency가 길 수 있지만, 글로벌로 일관되고 확장가능한 데이터베이스

[맨 위로](#chapter-11-%ed%81%b4%eb%9d%bc%ec%9a%b0%eb%93%9c%ec%97%90%ec%84%9c-storage-%ea%b8%b0%ed%9a%8d)

[본문 보기](../Chapter_11.md)