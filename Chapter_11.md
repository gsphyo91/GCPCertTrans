# Chapter 11 클라우드에서 Storage 기획

**이 챕터는 구글 Associate Cloud Engineer 인증 시험 과목 중, 아래 내용을 다룬다.**
* 2.3 데이터 스토리지 옵션 기획 및 설정

클라우드 엔지니어로서, GCP에서 제공되는 다양한 스토리지 옵션을 이해해야 한다. "쿼리를 위한 SQL에 접근하는 것 vs 데이터베이스에 페타바이트 급의 데이터 스트리밍을 저장하고 쿼리하는 기능"과 같이 상대적인 절충안을 알면서 주어진 케이스를 위한 적당한 옵션을 선택해야 한다.

이 책에서 대부분 다른 챕터와 다르게, 이 챕터는 GCP에서 특정 업무를 수행하는 것보다 스토리지 개념에 더 초점을 맞춘다. 여기 자료는 최고의 스토리지 솔루션을 선택하는 것에 대한 질문에 답변하는데 도움을 줄 것이다. 챕터 12는 데이터 솔루션을 배포하고 구현하는데 상세한 정보를 제공할 것이다.

스토리지 옵션 중 선택을 위해서, 스토리지 솔루션이 어떻게 다른지 이해해야 도움이 된다.
* Time to access data
* Data model
* Other features, such as consistency, availability, and support for transactions

이 챕터는 다양한 유형의 요구사항을 위해 스토리지 옵션을 선택하기 위한 가이드라인을 포함한다.

## 스토리지 시스템의 유형

스토리지 솔루션을 선택할 때 주요 고민은 데이터에 접근해야하는 시간이다. 극단적으로 CPU 칩의 L1 캐시에 있는 데이터는 0.5 나노초(ns)에 접근할 수 있다. 반대로 일부 서비스는 데이터 파일을 가져오는데 몇 시간이 요구될 수 있다. 대부분 스토리지 요구사항은 이러한 극단 사이에 있다.

**나노초, 밀리초, 마이크로초**
> 일부 스토리시 시스템은 전자 현미경에서 일어날 것 처럼 우리롸 친숙하지 않은 속도로 동작한다. 1초는 메모리나 디스크의 데이터에 접근하는데 걸리는 시간보다 극단적으로 길다. 3가지 측정 단위로 접근 시간이나 "latency"를 측정한다.
> * 나노 초(㎱), 10^-9초
> * 마이크로 초(㎲), 10^-6초
> * 밀리 초(㎳), 10^-3초 
>
> 10^-3은 과학적 표기법이고 0.001초를 의미한다. 유사하게, 10^-6은 0.000001과 같다. 10^-9는 0.000000001 초와 같다.
>
> 또 다른 고민은 Persistence이다. 특정 시스템에 저장된 데이터의 내구성은 어드정도인가? 캐시는 데이터에 접근하기 위한 가장 적은 지연시간을 제공하지만, 휘발성 데이터 유형은 오직 메모리에 전원이 공급될때만 존재한다. 서버가 중지되면 데이터는 사라진다. 디스크 드라이브는 내구성 등급이 더 높지만, 실패할 수 있다. 중복성이 도움이 된다. 데이터의 복사본을 만들고, 다른 rack, 다른 zone, 다른 region에 다른 서버에 저장함으로써, 하드웨어 장애로 인해 데이터 손실 위험을 줄인다.

GCP는 몇 가지 스토리지 서비스가 있다.
* 캐싱을 위한 관리형 Redis 캐시 서비스
* VM 사용을 위한 Persistenct disk storage
* 리소스 전반적으로 파일에 공유된 접근을 위한 Object storage
* 장기간, 비주기적 접근 요구사항을 위한 Archival stoarge

### Cache

캐시는 어플리케이션이 데이터에 수 밀리초 접근을 제공하도록 설계된 인메모리 데이터 저장소이다. 다른 스토리지 시스템보다 주요 장점은 저지연이다. 캐시는 이용할 수 있는 데이터의 양이 제한된다. 그리고 캐시를 호스팅한 머신이 중지되면 캐시의 컨텐츠는 삭제된다. 이 것은 상당한 제한조건이다. 하지만 일부 경우에, 데이터 접근 속도의 장점이 단점을 능가한다.

#### MemoryStore

GCP는 관리형 Redis 서비스인 Memorystore를 제공한다. Redis는 오픈소스 캐시로 넓게 사용된다. Memorystore는 Redis와 프로토콜 호환이 되기 때문에, Redis 작업을 위해 작성된 툴과 어플리케이션은 MemoryStore와 사용할 수 있다.

캐시는 데이터를 조회할 때 지연시간이 길면 안 되는 어플리케이션과 주로 사용된다. 예를 들어, 하드디스크 드라이브로부터 읽어야하는 어플리케이션은 인메모리에서 데이터를 읽는 경우보다 80배 더 오래 기다려야한다. 어플리케이션 개발자는 데이터베이스에서 조회된 다음 데이터가 필요할 때 디스크 대신 캐시에서 조회되는 데이터를 저장하는데 캐시를 사용할 수 있다.

MemoryStore를 사용할 때, Redis를 실행할 인스턴스를 생성한다. 인스턴스는 1GB에서 300GB 메모리로 구성된다. 또한 high availability(HA)를 구성할 수 있다. 이 경우 MemoryStore가 failover replicas를 생성한다.

#### Configuring MemoryStore

Memorystore 캐시는 Compute Engine, App Engine, Kubernetes Engine에서 실행하는 어플리케이션과 사용될 수 있다. 그림 11.1은 Memorystore를 구성하는데 사용되는 파라미터를 보여준다. 메인 콘솔 메뉴에서 Memorystore를 선택한 다음 Redis instance 생성 옵션을 선택하여 아래 양식을 확인할 수 있다.

![11.1](./img/ch11/11.1.png)

**그림 11.1** Memorystore 캐시를 위한 설정 파라미터

Memorystore에서 Redis 캐시를 설정하기 위해, instance ID, display name, Redis Version을 지정해야 한다. 현재 Redis 3.2만 지원된다. Standard instance tier를 선택하여 high availability를 위해 다른 zone에 replica를 갖도록 선택할 수 있다. Basic instance tier는 replica를 포함하지 않지만 비용이 적다.

캐시 전용 메모리 양에 따라서 region과 zone을 지정해야 한다. 캐시는 1GB에서 300GB의 사이즈가 될 수 있따. Redis 인스턴스는 다른 네트워크를 지정하지 않으면 기본 네트워크에서 접근할 수 있다.(GCP의 더 많은 네트워크 정보를 위해 챕터 14, 15를 참고). Memorystore를 위한 고급 옵션은 labels을 지정하고 IP 주소가 지정될 IP 범위를 정의할 수 있다.

### Persistent Storage

GCP에서 persistent disks는 내구성있는 block storage를 제공한다. Persistent disk는 GCE와 GKE의 VM에 연결될 수 있다. persistent disk가 block storage 디바이스이기 때문에, 이 디바이스에 파일 시스템을 생성할 수 있다. Persistent disk는 VM을 호스팅한 물리 서버에 직접 연결되지 않지만, 네트워크로 접근할 수 있다. VM은 SSD에 로컬로 연결될 수 있지만, 이 장치의 데이터는 VM이 종료되었을 때 잃는다. persistent disk의 데이터는 VM이 중지된 후에도 계속 존재한다. persistent disk는 VM과 독립적으로 존재한다. 로컬로 연결된 SSD는 그렇지 않다.

#### Persistent disk의 기능

persistent disk는 SSD와 HDD 설정을 사용할 수 있다. SSD는 높은 대역폭이 중요할 때 사용된다. SSD는 랜덤 액세스와 순차적 액세스 패턴을 위한 일관된 성능을 제공한다. HDD는 지연시간이 더 길지만 비용은 적다. 그래서 HDD는 대량의 데이터를 저장하고 어플리케이션 상호작용보다 데이터 지연시간이 덜 민감한 배치를 수행할 때 좋은 옵션이다. HDD를 지원하는 persistent disk는 기가바이트 0.75 IOPS(초당 입출력 동작, 기가바이스당 1.5 wirte IOPS를 수행할 수 있다. 반면에, 네트워크로 연결된 SSD는 기가바이트당 30 read and write IOPS를 수행할 수 있다. 로컬로 연결된 SSD는 기가바이트당 266에서 453의 read IOPS와 기가바이트당 186에서 240의 write IOPS를 달성할 수 있다.

Persistent disk는 multireader 스토리지를 지원하여 다양한 VM에 마운트될 수 있다. 디스크의 스냅샷은 몇 분만에 생성될 수 있다. 그래서 디스크 데이터의 추가 복사본은 다른 VM에서 사용하기 위해 분배될 수 있다. 스냅샷으로 생성된 디스크가 하나의 VM에 마운트되면, read, write 동작을 모두 지원할 수 있다.

Persistent disk의 사이즈는 VM에 마운트되는 동안 증가될 수 있다. 디스크 사이즈를 재조정하려면, 파일시스템이 접근할 수 있는 추가공간을 만들기 위해 OS 명령을 수행해야할지도 모른다. SSD와 HDD는 모두 64TB까지 가능하다.

Persistent disk는 디스크의 데이터가 자동적으로 암호화된다.

스토리지 옵션을 기획할 때, 디스크가 zonal인지 regional인지 고려해야한다. zonal 디스크는 하나의 존에 다양한 물리 드라이버 전체적으로 데이터를 저장한다. zone이 이용할 수 없게 되면, 디스크에 접근할 수 없다. 그 대안으로, regional persistent disk를 사용할 수 있고, region 내에 2개 존에 걸쳐서 데이터 블록을 복사하지만 zonal 스토리지보다 더 비싸다.

#### Persistent disk 설정

콘솔에서 Compute Engine을 열고 Disk를 선택해서 persistent disk를 생성, 설정할 수 있다. Disk 페이지에서 Create Disk를 클릭하면 그림 11.2와 같은 양식이 나타난다.

![11.2](./img/ch11/11.2.png)

**그림 11.2** persistent disk 생성 양식

디스크의 이름을 제공해야 하지만, description은 옵션이다. 디스크에는 2가지 유형이 있다: standard, SSD persistent disk. high availibility를 위해 region 내에 replica를 생성될 수 있다. region과 zone을 지정해야 한다. Label은 옵션이지만, 각 디스크의 목적을 추적하는데 도움을 주기 위해 추천한다.

Persistent disk는 공백이나 이미지나 스냅샷으로 생성될 수 있다. persistent boot disk를 생성하려면 이미지 옵션을 사용한다. 다른 디스크에 replica를 생성하려면 스냅샷을 사용한다.

데이터를 GCP에 저장할 때, 기본적으로 암호화된다. 디스크를 생성할 때, GCP에서 암호화 키를 관리하도록 선택할 수 있고, 이런 경우 추가 설정이 필요하지 않다. GCP의 Cloud Key Management Service를 사용하여 스스로 키를 관리할수 있고, GCP의 키 repository에 키를 저장할 수 있다. 이를 위해 customer-managed Key 옵션을 선택한다. Cloud Key Management Service에 생성될 키의 이름을 지정해야 한다. 또 다른 키 관리 시스템을 사용하여 키를 생성하고 관리하는 경우, customer-supplied Key를 선택한다. customer-supplied key 옵션을 선택한 경우 양식에서 키를 입력해야 한다.

### Object Storage

캐시는 수 밀리초의 latency로 접근할 수 있는 상대적으로 적은 양의 데이터를 저장하는데 사용된다. Persistent 스토리지 디바이스는 하나의 디스크에 64TB까지 저장할 수 있고, read/write 동작을 위한 100 IOPS까지 제공한다. exabyte 급 대량의 데이터를 저장해야 하고, 넓게 공유해야할 때, object 스토리지는 좋은 옵션이다. GCP의 objet 스토리지는 Cloud Storage 이다. 

#### Cloud Storage의 기능

Cloud Storage는 object 스토리지 시스템이다. 시스템에 저장되는 파일은 최소 단위로 취급된다. 즉, 파일의 한 섹션만 읽는 것같이 파일의 부분만 조작할 수 없다. object를 생성하거나 삭제하는 동작을 수행할 수 있지만 Cloud Storage는 파일의 하위 구성요소를 조작하는 기능을 제공하지 않느다. 예를 들어 파일의 한 색션을 덮어쓰는 Cloud Storage 명령은 없다. 또한, Cloud Storage는 동시성과 잠금을 지원하지 않는다. 다수의 클라이언트가 파일을 작성하면, 파일에 작성된 마지막 데이터가 저장되고 유지된다.

Cloud Storage는 일관된 데이터 구조에 대한 요구없이 대량의 데이터를 저장하기 위해 적합하다. 버킷에 다양한 유형의 데이터를 저장할 수 있다. 버킷은 Cloud Storage의 논리적 구성 단위이다. 버킷은 프로젝트 내의 리소스이다. 버킷은 글로벌 namespace를 공유한다는 것을 기억해야 한다. 그래서 각 버킷의 이름은 globally 유니크해야 한다. "mytestbucket"으로 버킷 이름을 할 수 없을 경우 놀라지 않아도 된다. 특히, 버킷 및 object 이름 지정 규칙을 따르지 않는 경우, 유니크한 파일 이름을 찾는 것은 어렵지 않다.

object storage는 파일 시스템을 제공하는 않는 것을 기억해야 한다. 버킷은 객체를 그룹으로 구성하는 데 도움을 주는 점에서 디렉토리와 유사하다. 하지만, 버킷은 하위 디렉토리같은 기능을 지원하는 디렉토리가 아니다. 구글은 Cloud Storage Fuse라는 오픈소스 프로젝트를 지원한다. 이는 리눅스와 MacOS에서 파일 시스템으로 버킷을 마운트하는 방법을 제공한다. Cloud Storage Fuse를 사용하면, 파일 시스템 명령을 사용하여 버킷에 파일을 다운로드하고 업로드할 수 있다. 하지만, 완전한 파일시스템 기능을 제공하지 않는다. Cloud Storage Fuse는 Cloud Storage와 동일한 제한조건을 갖는다. Cloud Storage Fuse의 목적은 리눅스나 Mac 파일시스템에서 작업할 때, 버킷에 내부, 외부에 데이터를 편하게 이동하게 만들어주는 것이다.

Cloud Storage는 object storage의 4가지 등급을 제공한다: multi-regional, regional, nearline, coldline.

#### Multiregional과 Regional 스토리지

버킷을 생성할 때, 버킷을 생성할 지역을 지정한다. 버킷과 버킷의 컨텐츠는 이 지역에 저장된다. regional 버킷이라고 알려진 single region이나 multiregional 버킷이라고 알려진 multiple regions에 데이터를 저장할 수 있다. Multiregional 버킷은 99.95%의 SLA(서비스 수준 계약)로 월 평균 99.99% 이상의 가용성을 제공한다. 데이터는 다수의 region에 복사된다. Regional 버킷은 월 평균 99.99%의 가용성과 99.9%의 SLA를 갖는다. Regional 버킷은 zone에 걸쳐서 중복된다. 

Multiregional 버킷은 컨텐츠에 접근 가능한 시간을 보장하기 위해 컨텐츠를 다수의 region에 저장되어야할 때 사용된다. 또한, zone 수준의 장애 시, 중복성을 제공한다. 그러나, 이 장점은 비용이 더 높다. 글을 작성하는 시점에, multiregional 스토리지는 미국에서 $0.26/GB/month가 발생한다. 반면에, regional 스토리지는 $0.20/GB/month가 발생한다. (Associate Cloud Enginner 시험에서 특정 비용에 대해서 물어보지 않을 것이다. 하지만 요구사항에 충족하는 저비용 솔류션을 식별하기 위해서 상대적인 비용을 알아야 한다.)

