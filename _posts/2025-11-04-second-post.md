---

title: "TCP/IP 모델과 OSI 7계층 — 핵심 개념과 프로토콜의 동작(단계별 정리)"
categories: [networking, tcpip]
tags: [TCP, IP, OSI, 네트워크, 프로토콜]
--------------------------------

---

## 개요 및 역사(간단히)

* **시작**: 1960년대 후반 미국 국방성 고등연구계획국(ARPA)에서 네트워크 연구 시작.
* **TCP/IP 사양**: 1982년에 사양 결정. 1983년 ARPANET의 표준 프로토콜로 채택되며 인터넷으로 발전.
* **의미**: ARPANET에서 사용하던 통신 규약을 인터넷의 표준으로 채택한 것이 오늘날의 TCP/IP 기반 인터넷.
* **현황**: 오랫동안 사용되며 실무에서 표준으로 자리 잡아 안정적.

---

## 모델 비교 요약

### OSI 7계층

1. 물리(Physical)
2. 데이터 링크(Data Link)
3. 네트워크(Network)
4. 전송(Transport)
5. 세션(Session)
6. 표현(Presentation)
7. 응용(Application)

* **목적**: 국제표준화를 위한 이론적 모델. 교육/설계에 유용.

### TCP/IP 4계층 (실무 관점)

1. 네트워크 액세스(Network Access) — 물리 + 데이터 링크 대응
2. 인터넷(Internet) — 네트워크 계층
3. 전송(Transport)
4. 응용(Application)

* **특징**: 실제 인터넷에서 구현된 모델로 실무와 실습에 더 가깝다.

---

## 각 계층별 핵심 역할(요약)

### 1계층: 네트워크 액세스 계층 (Network Access)

* 물리적 전송 (케이블, 무선), MAC 주소 기반의 로컬 통신, 프레이밍
* 관련 프로토콜/기술: Ethernet, ARP, PPP, 스위칭(L2)

### 2계층: 인터넷 계층 (Internet)

* 호스트 간 IP 패킷 전달, 라우팅
* 관련 프로토콜: IP(IPv4/IPv6), ICMP, ARP

### 3계층: 전송 계층 (Transport)

* 종단 간(end-to-end) 전송, 신뢰성/흐름 제어
* 관련 프로토콜: TCP(신뢰성), UDP(비신뢰성)

### 4계층: 응용 계층 (Application)

* 사용자 애플리케이션과 직접 연관되는 프로토콜
* 관련 프로토콜: HTTP, FTP, DNS, DHCP, SSH 등

---

## IP (Internet Protocol)

* 역할: 데이터를 **패킷 단위**로 목적지 IP 주소로 전달.
* 성격: Best-effort 전달(순서 보장 X, 재전송·오류 보정 미지원).
* 주소 체계: IPv4(32-bit, 약 43억 주소) — 클래스 기반에서 CIDR로 이동.

### CIDR 요약

* 클래스(A/B/C)에 의존하지 않고, 가변 길이 전치부(prefix)를 사용하여 주소 할당과 라우팅을 최적화.
* 표기 예: 192.0.2.0/24 (전치부 길이 24)

---

## TCP (Transmission Control Protocol)

* 역할: 데이터 전달의 **신뢰성 보장**, 순서 보장, 흐름 및 혼잡 제어
* 특징: 연결형(연결 수립 후 데이터 전송), 바이트 스트림 단위

### TCP의 핵심 동작 (단계별)

#### 1) 연결 수립 — **3-way handshake**

1. **SYN** (클라이언트 → 서버): 클라이언트가 초기 시퀀스 번호 `ISN_c`를 담아 SYN 패킷 전송(접속 요청).
2. **SYN-ACK** (서버 → 클라이언트): 서버는 자신의 `ISN_s`와 함께 수신한 `ISN_c+1`에 대한 ACK를 보내 응답.
3. **ACK** (클라이언트 → 서버): 클라이언트가 서버의 `ISN_s+1`에 대한 ACK를 보내 연결 완성.

* 이 과정을 통해 양측은 초기 시퀀스 번호를 교환하고 연결 상태를 초기화.

#### 2) 데이터 전송 및 신뢰성 보장

* **시퀀스 번호**로 바이트 순서 추적.
* **ACK**로 수신 확인(누적 ACK 혹은 선택적 ACK(SACK) 확장).
* 손실 패킷 감지: 재전송 타이머(RTO) 만료 또는 중복 ACK(예: 3 duplicate ACK) 감지 => 재전송.
* **흐름 제어**: 수신 윈도우(RCV.WND)를 통해 송신 속도 조절.
* **혼잡 제어**: slow start, congestion avoidance, fast retransmit, fast recovery 알고리즘.

#### 3) 연결 종료 — **4-way close** (일반적)

1. 한쪽(클라이언트)이 **FIN**을 보내어 종료 의사 표명.
2. 상대는 **ACK**로 수신 확인.
3. 상대도 자신의 데이터를 모두 보낸 후 **FIN**을 보냄.
4. 최초 측이 **ACK**로 응답하면 완전 종료(양방향 종료).

* **TIME_WAIT** 상태: 종료 직후 일정 시간 동안(일반적으로 2 * MSL) 포트 재사용을 방지.

---

## UDP (User Datagram Protocol)

* 비연결형, 헤더가 간단하여 오버헤드가 작음.
* 순서·재전송·혼잡 제어 없음 — 실시간 스트리밍, DNS 쿼리, 게임 등에 적합.

---

## 중요 프로토콜의 단계별 동작

### ARP(Address Resolution Protocol)

* 목적: 같은 로컬 네트워크 상에서 **IP 주소 → MAC 주소** 매핑.

* 흐름:

  1. 호스트 A가 B의 MAC을 모를 때 브로드캐스트로 `ARP Request`(Who has IP_B?) 전송.
  2. IP_B를 가진 호스트 B가 `ARP Reply`로 자신의 MAC을 A에게 전송(유니캐스트).
  3. A는 ARP 캐시에 MAC을 저장.

* ARP 스푸핑 공격 주의: 네트워크 보안 실습에서 다루기 좋음.

### ICMP (Internet Control Message Protocol) — 예: ping

* 목적: 진단 및 오류 메시지(예: Echo Request/Reply, Destination Unreachable)
* ping 동작:

  1. 송신지에서 `Echo Request` 전송.
  2. 대상이 `Echo Reply`로 응답하면 왕복시간(RTT) 측정 가능.

### DHCP (Dynamic Host Configuration Protocol) — DORA 과정

* 목적: IP 주소 및 네트워크 설정 자동 할당
* 단계(DORA):

  1. **Discover**: 클라이언트 브로드캐스트로 DHCP 서버 탐색.
  2. **Offer**: 서버가 사용 가능한 IP와 설정을 제안.
  3. **Request**: 클라이언트가 특정 Offer를 요청.
  4. **Ack**: 서버가 IP 임대 승인 및 설정 전송.

### DNS (Domain Name System) — 해석 과정

* 목적: 도메인 이름 ↔ IP 주소 변환
* 동작(재귀 vs 반복):

  * **재귀적 쿼리**: 로컬 리졸버가 대신 최종 답을 찾아 클라이언트에 전달.
  * **반복적 쿼리**: 각 네임서버는 다음 네임서버의 정보(위임)를 응답하고 클라이언트가 직접 다음 서버에 질의.

### HTTP (기본 요청/응답 흐름)

1. TCP 연결(또는 TLS 핸드셰이크) 수립.
2. 클라이언트가 `HTTP Request` 전송(GET/POST 등).
3. 서버가 `HTTP Response`(상태코드, 헤더, 바디) 전송.
4. 연결: `Connection: keep-alive` 이거나 `Connection: close`에 따라 재사용/종료.

---

## 라우팅과 포워딩

* **포워딩**: 라우터가 도착한 패킷의 목적지 IP를 보고 다음 홉으로 전달하는 동작(라우팅 테이블 기반).
* **라우팅**: 라우터들 간에 경로 정보를 교환하여 라우팅 테이블을 구성하는 과정.

  * 프로토콜 예: OSPF(링크 상태), RIP(거리 벡터), BGP(인터도메인 경로 교환)

---

## IP 단편화(Fragmentation)와 재조립

* MTU(최대전송단위)를 초과하는 패킷은 라우터나 송신자에 의해 작은 조각으로 분할.
* IPv4는 라우터가 단편화 수행 가능(IPv6는 기본적으로 송신자가 단편 처리).
* 각 조각은 식별자(Identification), 오프셋(Offset), MF(More Fragments) 플래그를 사용하여 재조립.
* 문제점: 단편화는 성능 저하와 보안(예: 조각 재조합 악용) 이슈를 야기.

---

## NAT(Network Address Translation)

* 목적: 사설 IP를 공인 IP로 변환하여 인터넷 접속을 가능하게 함(주소 부족 문제 완화).
* 방식: SNAT, DNAT, PAT(포트 주소 변환) 등.
* 영향: 종단 간 연결(end-to-end)이 깨질 수 있고, 일부 프로토콜(예: FTP, SIP)에 특별 처리 필요.

---

## 보안

* IP 계층/라우팅 공격: IP 스푸핑, 라우팅 프로토콜 공격
* 데이터 링크: ARP 스푸핑
* 전송 계층: TCP 세션 하이재킹, SYN flood(서비스 거부 공격)
* 대응 기법: 방화벽, IPS/IDS, TCP SYN 쿠키, TLS(전송 계층 보안), 네트워크 분할 및 암호화

---

