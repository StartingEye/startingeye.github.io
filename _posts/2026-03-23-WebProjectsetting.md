---
layout: post
title: "[Project] 웹 모의해킹 01. 환경 구축(Kali Linux & DVWA) 및 정밀 설정"
date: 2026-03-22 23:59:00 +0900
categories: [Project, Security]
tags: [InformationSecurity, KaliLinux, DVWA, Lab-Setup, BurpSuite]
---

## 0. 시작하며: "모의해킹 입문"
안녕하세요, **startingeye**입니다. 정보보안학과 2학년으로서, "처음으로 모의해킹 환경을 구축해보며"첫 번째 실습인 **'모의해킹 환경 구축'** 을 진행했습니다.

본 포스팅은 공격자 OS인 Kali Linux 설치와 실습용 가상 서버(DVWA)의 세부 설정, 그리고 분석 도구인 Burp Suite의 정밀 세팅 과정을 기록한 리포트입니다.

---

## 1. 공격자 OS: Kali Linux 설치 및 네트워크 검증
가상화 도구인 VirtualBox를 활용하여 독립된 공격 환경을 구축하고 통신 상태를 확인했습니다.

* **Platform**: VirtualBox 7.x
* **OS Image**: Kali Linux 2026.x (OVA 이미지 활용)
* **Network**: **NAT Network** 설정 (가상 머신 간 독립적 통신 및 호스트 OS 보호)
* **Resource**: RAM 4GB / CPU 2 Core 할당

* ip addr 명령어를 사용하여 두 서버의 각자 주소는
  - ubuntu: 10.0.2.5
  - kali: 10.0.2.15
  - 서로 통신이 가능하며 ip addr 명령어로 주소가 나타남
![](/assets/img/posts/2026-03-23-WebProjectsetting/2026-03-25-01-17-46.png)
---

## 2. 실습용 가상 서버: DVWA 세팅 및 DB 초기화
취약점 분석을 위한 타겟 서버인 DVWA(Damn Vulnerable Web App)를 구축하고, 서비스가 정상 동작할 수 있도록 내부 설정을 완료했습니다.

### 2.1 서비스 활성화 및 상태 확인
```bash
# Apache 및 MariaDB 서비스 시작
sudo systemctl start apache2 mariadb
```

![](/assets/img/posts/2026-03-23-WebProjectsetting/2026-03-25-01-30-35.png)

```bash
# 서비스 상태 검증 (Active: running 상태 확인 필수)
sudo systemctl status apache2 mariadb | grep "Active:"
```

### 2.2 데이터베이스 초기화 (Setup)
브라우저에서 http://127.0.0.1/DVWA/setup.php에 접속하여 서버 연동을 마무리합니다.

![](/assets/img/posts/2026-03-23-WebProjectsetting/2026-03-25-01-49-48.png)

* **DB Reset**: 페이지 하단의 [Create / Reset Database] 버튼을 클릭하여 스키마를 생성합니다. 이 과정이 완료되어야만 데이터베이스 테이블이 생성되어 로그인이 가능해집니다.
* **Security Level**: 기초적인 취약점 원리 파악과 필터링 우회 로직 학습을 위해 'Low' 레벨로 설정했습니다.

---

## 3. 분석 도구 세팅: Burp Suite Proxy 및 인증서 검증
웹 패킷을 가로채고 분석하기 위한 Burp Suite의 프록시 환경과 HTTPS 복호화 설정을 완료했습니다.

* **Proxy Listen**: 127.0.0.1:8080 포트가 정상적으로 Listen 중인지 확인했습니다.
* **CA 인증서 설치**:
    * 브라우저에서 http://burp 접속 후 cacert.der 다운로드.
    * 브라우저(Firefox) 설정의 '인증서 보기' 메뉴를 통해 인증서 가져오기 실행.
    * "이 CA를 신뢰하여 웹사이트 식별" 항목에 반드시 체크하여 신뢰 관계를 형성합니다.
* ![](/assets/img/posts/2026-03-23-WebProjectsetting/2026-03-26-10-31-23.png)
*캡션*
* **검증**: HTTPS 사이트 접속 시 Burp Suite의 Intercept 탭에서 암호화된 패킷이 평문으로 정상 노출되는지 최종 확인했습니다.

---

## 4. 1주차 최종 검증 완료 (Checklist)

- [x] Kali Linux 부팅 및 NAT Network 내부 통신 정상 여부 확인.
- [x] Apache 및 MariaDB 서비스의 Active 상태 및 포트 점유 확인.
- [x] DVWA DB Reset 성공 및 로그인 세션 정상 작동 확인.
- [x] Burp Suite 인증서 등록을 통한 HTTPS 패킷 인터셉트 성공 확인.
- [x] 예기치 못한 설정 오류에 대비한 '초기 환경 스냅샷' 저장 완료.

> **Legal Disclaimer**: 본 실습은 본인이 구축한 가상 환경 내에서만 수행되었습니다. 비인가 시스템에 대한 공격 시도는 법적 처벌을 받을 수 있습니다.

