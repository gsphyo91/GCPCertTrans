# Chapter 15 Cloud 네트워크: DNS, 로드밸런싱, IP주소

## Cloud DNS 구성

* Cloud DNS는 도메인 이름 확인을 제공하는 구글 서비스
* 관리되는 zone은 aceexamdns1.com과 같은 DNS 이름 접미사와 관련된 DNS 레코드를 포함
* DNS 레코드는 zone에 대한 특정 정보를 포함
  * 호스트 이름을 IPv4의 IP 주소에 매핑
  * AAAA 레코드는 이름을 IPv6 주소로 매핑
  * CNAME 레코드는 도메인의 alias 이름을 포함하는 정식 이름을 잡음

### Cloud Console을 사용하여 DNS 관리 zone 생성

* 