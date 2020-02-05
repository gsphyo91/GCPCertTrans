# Chapter 15 Cloud 네트워크: DNS, 로드밸런싱, IP주소

## Cloud DNS 구성

* Cloud DNS는 도메인 이름 확인을 제공하는 구글 서비스
* 관리되는 zone은 aceexamdns1.com과 같은 DNS 이름 접미사와 관련된 DNS 레코드를 포함
* DNS 레코드는 zone에 대한 특정 정보를 포함
  * 호스트 이름을 IPv4의 IP 주소에 매핑
  * AAAA 레코드는 이름을 IPv6 주소로 매핑
  * CNAME 레코드는 도메인의 alias 이름을 포함하는 정식 이름을 잡음

### Cloud Console을 사용하여 DNS 관리 zone 생성

* console - Network Services - Cloud DNS - Create Zone
  * zone 타입
    * public
      * 인터넷을 통해 접근
      * 모든 source의 쿼리에 응답하는 네임서버를 제공
    * private
      * VM과 로드밸런서같은 GCP 리소스에 네임서비스를 제공
      * zone과 동일한 프로젝트의 리소스에서 시작된 쿼리만 응답
      * zone에 접근할 네트워크 지정
  * zone 이름과 설명
  * DNS 이름
  * DNS Security인 DNSSEC 활성화
    * spoofing(다른 클라이언트처럼 보이는 클라이언트) 예방
    * cache poisoning(DNS 서버를 업데이트하여 잘못된 정보를 보내는 클라이언트)예방
* zone이 생성되었을 때, NS와 SOA 레코드가 추가
  * NS
    * zone 정보를 관리하는 공식 서버의 주소를 갖고 있는 네임서버 레코드
  * SOA
    * zone에 대한 공식 정보를 갖고 있는 Start Of Authority 레코드
  * A와 CNAME 레코드와 같은 다른 레코드를 추가할 수 있음
    * Add Record Set 클릭
    * A나 CNAME을 선택
      * A
        * 도메인 이름을 IP 주소로 매핑하는 서버의 IPv4 주소 입력
        * TTL과 TTL Unit 지정
          * 레코드가 캐시에 얼마나 오래 남아잇을 지 지정
          * DNS resolver가 다시 쿼리하기 전에 데이터를 캐시해야하는 기간
        * Add Item을 클릭하여 다른 IP 주소 추가 가능
      * CNAME
        * 서버의 이름, alias 지정

### gcloud를 사용하여 DNS 관리 zone 생성

```bash
# DNS zone과 레코드를 추가
gcloud dns managed-zones create [ZONE_NAME] --description= --dns-name=[DNS_NAME]

# private zone 생성
gcloud dns managed-zones create [ZONE_NAME] --description= --dns-name=[DNS_NAME] --visibility=private --networks=default

# A 레코드 추가
# Transaction을 시작
gcloud dns record-sets transaction start --zone=[ZONE_NAME]

# Record 정보 추가
gcloud dns record-sets transaction add [IP_ADDRESS] --name=[DNS_NAME] --ttl=[TIME] --type=A --zone=[ZONE_NAME]

# Transaction 완료
gcloud dns record-sets transaction execute --zone=[ZONE_NAME]

# CNAME 레코드 추가
# Transaction을 시작
gcloud dns record-sets transaction start --zone=[ZONE_NAME]

# Record 정보 추가
gcloud dns record-sets transaction add [IP_ADDRESS] --name=[DNS_NAME] --ttl=[TIME] --type=CNAME --zone=[ZONE_NAME]

# Transaction 완료
gcloud dns record-sets transaction execute --zone=[ZONE_NAME]
```

## 로드밸런서 구성

* 로드밸런서는 어플리케이션을 실행하는 서버의 워크로드를 분산

### 로드밸런서의 타입

* 하나의 region이나 다수의 region에 걸쳐서 로드를 분산할 수 있음
* 로드밸런서의 3가지 특징
  * Global vs regional 로드밸런싱
  * External vs internal 로드밸런싱
  * HTTP와 TCP같은 트래픽 타입
* Global 로드밸런서
  * 어플리케이션이 글로벌로 분산될 때 사용
  * 3가지 글로벌 로드밸런서
    * HTTP(S), HTTP 및 HTTPS 로드를 밸런스
    * SSL Proxy, 보안 소켓 레이어 연결인 SSL/TLS, non-HTTPS 트래픽에 사용
    * TCP Proxy, 로드밸런서의 TCP 세션, 백엔드 서버로 트래픽을 분산
* Regional 로드밸런서
  * 리소스가 하나의 region에 있을 때 사용
  * Internal TCP/UDP
    * Internal VM을 호스팅하는 private 네트워크의 TCP/UDP 트래픽 밸런스
  * Network TCP/UDP
    * IP 프로토콜, 주소, 포트를 기반으로 밸런싱
    * SSL Proxy와 TCP Proxy 로드밸런서에서 지원되지 않는 SSL과 TCP 트래픽에 사용
* External 로드밸런서
  * 인터넷 트래픽을 분산
  * HTTP(S), SSL Proxy, TCP Proxy, Network TCP/UDP
* Internal 로드밸런서
  * GCP 내에서 시작되는 트래픽을 분산
  * Internal TCP/UDP
* 로드밸런서를 선택할 때 트래픽 타입을 고려해야 함
  * HTTP와 HTTPS 트래픽은 external global 로드밸런싱
  * TCP 트래픽은 external global, external regional, internal regional 로드밸런서 사용
  * UDP 트래픽은 external regional, internal regional 로드밸런싱 사용

### Cloud Console을 사용하여 로드밸런서 구성

* Console - Network Services - Load Balancing
  * 로드밸런서 타입 선택
    * HTTP(S), TCP, UDP
  * Public, Private 선택
  * Multi Regions, Single region 선택
  * TCP나 SSL 프로세싱 오프로드 선택
  * 3단계 프로세스 구성
    * 백엔드 구성
      * 이름, region, 네트워크, 백엔드 지정
      * 백엔드는 로드를 분산시킬 VM
      * Health Check 구성
        * 이름, 프로토콜, 포트, health 기준 지정
        * check interval마다 확인하고, timeout 만큼 기다리고, unhealthy threshold 연속 발생하면, unhealthy로 간주
    * 프론트엔드 구성
      * 이름, 서브넷, internal IP, 포트 구성
    * 검토

### gcloud를 사용하여 로드밸런서 구성

```bash
# pool의 모든 VM 트래픽을 로드밸런서로 라우팅
gcloud compute forwarding-rules create [LOAD_BALANCER_NAME] --port=[PORT] --target-pool [POOL_NAME]

# Target pool 생성
gcloud compute target-pools create [POOL_NAME]

# Target pool에 인스턴스 추가
gcloud compute target-pools add-instances [POOL_NAME] --instances [INSTANCE_NAME, INSTANCE_NAME, ...]
```

## IP 주소 관리

### CIDR 블록 확장

* CIDR 블록은 서브넷에서 사용할 수 있는 IP 주소의 범위를 정의
* expand-ip-range는 주소의 수를 증가하는데만 사용
  * 수를 줄이려면 re-create 해야함

```bash
# 사용할 수 있는 주소의 수를 늘리거나 서브넷에서 실행하는 클러스터의 사이즈를 확장하는 경우
gcloud compute networks subnets expand-ip-range [SUBNET_NAME] --prefix-length [LENGTH]
# LENGTH가 16이면 65,536으로 증가
```

### IP 주소 선점

* Static external IP 주소는 console이나 command line에서 선점할 수 있음
* console - VPC - External IP Address - Reserve Static Address
  * 이름, 설명 입력
  * Standard Service tier
    * 일부 데이터 전송을 위해 인터넷 사용
  * Premium tier
    * 구글의 글로벌 네트워크에서 모든 트래픽을 라우팅
  * IPv4, IPv6
  * regional, global
* 선점된 주소는 사용하지 않을 때 VM에 연결되고, 해제될 때까지 연결
* Command line

```bash
# IP 주소 선점(Premium tier)
gcloud compute addresses create [NAME] --region=[REGION] --network-tier=PREMIUM
```

[맨 위로](#chapter-15-cloud-%eb%84%a4%ed%8a%b8%ec%9b%8c%ed%81%ac-dns-%eb%a1%9c%eb%93%9c%eb%b0%b8%eb%9f%b0%ec%8b%b1-ip%ec%a3%bc%ec%86%8c)

[본문 보기](../Chapter_15.md)