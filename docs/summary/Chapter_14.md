# Chapter 14 Cloud 네트워크: VPC(Virtual Private Cloud)와 VPN(Virtual Private Networks)

## Subnet으로 VPC 생성

* VPC는 프로젝트의 리소스와 연결되는 물리 네트워크의 소프트웨어 버전
* GCP는 프로젝트를 생성할 때 VPC를 자동으로 생성
  * 추가 VPC를 생성하고, 생성된 VPC를 수정할 수 있음
* VPC는 글로벌 리소스
  * region이나 zone에 연결되어있지 않음
  * Compute Engine과 Kubernetes Engine의 트래픽은 방화벽에 의해서 제한되지 않는다는 가정하에 서로 통신할 수 있음
* VPC는 *subnets*이라고 불리는 regional 리소스인 subnet을 포함
  * 서브넷은 연결된 IP주소 범가 있음
    * 개인 private IP를 제공
    * 리소스는 이 주소를 사용하여 서로서로 및 구글 API 및 서비스와 통신
* 프로젝트와 연관된 VPC이외에도, Organization 내에 공유 VPC를 생성할 수 있음
  * 공유 VPC는 공통 프로젝트에 호스팅
  * 권한을 갖는 다른 프로젝트의 사용자가 공유 VPC에 리소스를 생성할 수 있음
  * Organization에 정의되지 않아도 내부 프로젝트간 연결을 위해 VPC Peering 사용

### Cloud Console로 VPC 생성

* Cloud Console - VPC - Create VPC
* Auto mode 네트워크
  * 각 서브넷을 위한 PI 주소 범위를 선택
* Custom 탭
  * region과 IP 주소 범위를 지정
  * IP 범위는 Classless Inter-Domain Routing(CIDR) 표기법으로 지정
  * Private Google Access 지정
    * VM에 외부 IP 주소를 할당하지 않아도 구글 서비스에 접근
  * Flow Logs
    * 네트워크 트래픽의 로깅
* 방화벽 규칙
* 동적 라우팅 설정
  * Regional 라우팅
    * 구글 Cloud Router가 해당 지역 내 경로를 학습
  * Global 라우팅
    * VPC의 모든 Subnet의 경로를 학습
* DNS 서버 정책 설정
  * GCP에 의해서 제공되는 DNS 이름 확인을 활성화
  * 이름 확인 순서를 변경하는 DNS 정책 설정

### gcloud로 VPC 생성

```bash
# auto mode 네트워크 생성
gcloud compute networks create [VPC_NAME] --subnet-mode=auto

# custom mode 네트워크 생성
gcloud compute networks create [VPC_NAME] --subnet-mode=custom

# 서브넷 생성
gcloud compute networks subnet crate [SUBNET_NAME] --network=[VPC_NAME] --region=[REGION] --range=[IP/CIDR] --enable-private-ip-google-access --enable-flow-logs
```

### gcloud를 사용하여 공유 VPC 생성

* 공유 VPPC를 생성하는 명령을 실행하기 전, 조직 수준 또는 폴더 수준에서 조직 구성원에게 Shared VPC Admin role을 할당해야 함
  * `roles/compute.xpnAdmin`

```bash
# Shared VPC Admin role를 조직에 할당
gcloud organizations add-iam-policy-binding [ORG_ID] --member='user:[EMAIL_ADDRESS]' --role="roles/compute.xpnAdmin"

# [ORG_ID] 확인
gcloud organizations list

# Shared VPC Admin role을 폴더에 할당
gcloud resource-manager folders add-iam-policy-binding [FOLDER_ID] --member='user:[EMAIL_ADDRESS]' --role="roles/compute.xpnAdmin"

# [FOLDER_ID] 확인
gcloud resource-manager folders list --organization=[ORG_ID]

# Shared VPC 명령
gcloud compute shared-vpc enable [HOST_PROJECT_ID]

# 공유 VPC를 프로젝트에 연결
gcloud compute shared-vpc associated-projects add [SERVICE_PROJECT_ID] --host-project [HOST_PROJECT_ID]

# VPC peering 명령
# 네트워크 A에 peering 지정
gcloud compute networks peerings create peer-ace-exam-1 \
    --network ace-exam-network-A \
    --peer-project ace-exam-project-B \
    --peer-network ace-exam-network-B \
    --auto-create-routes

# 네트워크 B에 peering 지정
gcloud compute networks peerings create peer-ace-exam-1 \
    --network ace-exam-network-B \
    --peer-project ace-exam-project-A \
    --peer-network ace-exam-network-A \
    --auto-create-routes
```

## Custom 네트워크로 Compute Engine 배포

* Console - Compute Engine - Create Instance - Networking 탭 - Add Network Interface
* static IP 주소를 지정하거나 Primary Internal IP 설정을 사용하여 임시 주소를 선택할 수 있음
* Command Line
  * `gcloud compute instance create [INSTANCE_NAME] --subnet [SUBNET_NAME] --zone [ZONE_NAME]`

## VPC를 위한 방화벽 규칙 생성

* 방화벽 규칙은 네트워크 수준에서 정의되고, VM의 네트워크 트래픽 흐름을 제어하는데 사용
* port의 여러 트래픽을 허용하거나 거부
* incoming(ingress), outgoing(egress) 중, 한 방향으로 트래픽을 적용
* 방화벽을 stateful이므로, 트래픽이 한 방향으로 허용
  * 연결이 맺어지면, 다른 방향도 허용

### 방화벽 규칙의 구조

* 방화벽 규칙 컴포넌트
  * Direction
    * Ingress나 Egress 중 하나
  * Priority
    * 최우선 규칙이 적용
    * 우선순위는 0에서 65535까지 숫자로 지정
      * 0은 최상위, 65535는 최하위
  * Action
    * 허용이나 거부, 하나만 선택 가능
  * Target
    * 규칙이 적용될 인스턴스
      * 네트워크의 모든 인스턴스
      * 특정 네트워크 태그를 갖는 인스턴스
      * 특정 서비스 계정을 사용하는 인스턴스
  * Source/Destination
    * Source는 ingress 규칙을 적용
      * Source IP 범위, 특정 네트워크 태그를 갖는 인스턴스, 특정 서비스 계정을 사용하는 인스턴스를 지정
    * Destination 파라미터는 오직 IP 범위를 사용
  * Protocol and Port
    * TCP, UDP, ICMP같은 네트워크 프로토콜과 port 번호
    * 지정하지 않으면, 규칙은 모든 프로토콜에 적용
  * Enforcement Status
    * 방화벽 규칙은 enable이나 disabled 중 하나
    * 비활성 규칙은 일치해도 적용되지 않음
* 모든 VPC는 2가지 암시적 규칙이 있음
  1.모든 destinations에 egress 트래픽을 허용
  2.모든 source로부터 들어오는 모든 트래픽을 거부 
  * 두 가지 암시적 규칙은 모두 우선순위 65535
  * 우선순위가 높은 규칙을 생성할 수 있지만, 암시적 규칙을 삭제할 수 없음
* VPC가 자동으로 생성될 때, 디폴트 네트워크는 4가지 네트워크 규칙은 갖고 생성
  1.동일한 네트워크의 모든 VM 인스턴스에서 트래픽이 인입되는 것
  2.SSH를 허용하기 위해 22번 포트로 TCP 트래픽이 인입되는 것
  3.Microsoft Remote Desktop Protocol(RDP)를 허용하기 위해 3389번 포트로 TCP 트래픽이 인입되는 것
  4.네트워크의 모든 source로부터 Internet Control Message Protocol(ICMP)가 인입되는 것
  * 기본 규칙은 모두 우선순위 65534

### Cloud Console을 사용하여 방화벽 규칙 생성

* Console - VPC - Firewall - Create Firewall Rule
  * 방화벽 규칙의 이름과 설명 지정
  * Logging 활성화 설정
  * 규칙을 적용할 VPC의 네트워크를 지정
  * 우선순위
    * 0에서 65535 범위의 숫자
  * 방향
    * ingress이거나 egress
  * 동작
    * 허용하거나 거부
  * 타겟
  * 소스
    * IP 범위, 서브넷, 소스 태그, 서비스 계정
  * 프로토콜 및 포트
    * 모두 허용 및 지정된 프로토콜, 포트

### gcloud를 사용하여 방화벽 규칙 생성

* 커맨드라인으로 방화벽 규칙을 작업하기 위한 명령
  * `gcloud compute firewall-rules`
  * 파라미터
    * `--action`
    * `--allow`
    * `--description`
    * `--destination-ranges`
    * `--direction`
    * `--network`
    * `--priority`
    * `--source-ranges`
    * `--source-service-accounts`
    * `--source-tags`
    * `--target-service-accounts`
    * `--target-tags`

## VPN 생성

* VPN은 구글 네트워크에서 자체 네트워크로 네트워크 트래픽을 안전하게 전송

### Cloud Console로 VPN 생성

* Console - Hybrid Connectivity - Create VPN Connection
  * VPN의 이름과 설명
  * Compute Engine VPN Gateway
    * VPN 연결의 GCP 끝단 설정
  * 네트워크를 포함하는 region, static IP 주소를 지정
  * Tunnels
    * VPN의 다른 네트워크 엔드포인트를 설정
    * 자체 네트워크의 VPN 게이트웨이의 이름, 설명, IP 주소를 지정
    * 사용할 Internet Key Exchange(IKE)의 버전 지정
  * Routing Options
    * Dynamic Routing은 네트워크 경로를 학습하는 BGP 프로토콜 사용
      * Cloud Router 생성
        * 이름, 설명 입력
        * BGP 프로토콜에서 사용되는 private Autonomous System Number(ASN)을 지정
          * ASN은 64512-65534이거나 4000000000–4294967294 범위의 숫자
          * 유니크한 ASN이 필요
    * Route-Based Routing
      * 원격 네트워크의 IP 범위를 입력
    * Policy-Based Routing
      * 원격 IP 범위, VPN을 사용하는 로컬 서브넷, 로컬 IP 범위 입력

### gcloud를 사용하여 VPN 생성

```bash
# target-vpn-gateways 생성 명령
gcloud compute target-vpn-tunnels create [TUNNEL_NAME] --network=[NETWORK_NAME]

# Forwarding rule 생성
gcloud comptue forwarding-rules create [NAME] --TARGET_SPECIFICATION=VPN_GATEWAY

# vpn tunnels 생성 명령
gcloud compute vpn-tunnels create [TUNNEL_NAME] --peer-address=[PEER_ADDRESS] --shared-secret=[SHARED_SECRET] --target-vpn-gateway=[TARGET_VPN_GATEWAY]
```

[맨 위로](#chapter-14-cloud-%eb%84%a4%ed%8a%b8%ec%9b%8c%ed%81%ac-vpcvirtual-private-cloud%ec%99%80-vpnvirtual-private-networks)

[본문 보기](../Chapter_14.md)