---
layout: post
title: "[Web Pentesting] 웹 모의해킹 프로젝트 2주차. 타겟 정찰(Reconnaissance) 및 데이터 추출 트러블슈팅"
date: 2026-03-28 13:00:00 +0900
categories: [Security, Pentesting]
tags: [ffuf, nmap, reconnaissance, dvwa, hacking]
---

## 1. 개요

> ⚠️ **Disclaimer:** 본 포스팅의 침투 테스트 실습은 외부망과 분리된 로컬 환경에서, 사전 인가된 타겟(DVWA)만을 대상으로 진행되었습니다. 권한이 없는 시스템에 대한 무단 스캐닝 및 공격 시도는 관련 법령에 의해 엄격히 처벌받을 수 있습니다.

웹 침투 테스트의 첫 단추는 **정찰(Reconnaissance)**입니다. 타겟 시스템이 어떤 OS를 사용하는지, 어떤 서비스가 구동 중인지, 그리고 숨겨진 디렉터리는 무엇인지 파악하여 '공격 표면(Attack Surface)'을 가시화하는 과정입니다. 

이번 포스팅에서는 `Nmap`과 `ffuf`를 활용하여 DVWA 환경의 지도를 그리는 실습과 그 과정에서의 트러블슈팅 경험을 공유합니다.

### 📌 실습 환경 요약 (Environment)
* **Attacker (공격자):** Kali Linux (`10.0.2.15`)
* **Target (타겟):** Ubuntu / DVWA (`10.0.2.5`)
* **Used Tools:** Nmap, ffuf, Python3, curl, Host PC(분석 진행)

---

## 2. 타겟 시스템 정보 수집 (Nmap)
우선 타겟 IP(`10.0.2.5`)를 대상으로 서비스 버전, OS 정보, 오픈 포트를 정밀 스캔했습니다.

```bash
# 모든 포트 스캔, 서비스 버전 탐지, 기본 스크립트 실행 및 OS 확인
sudo nmap -sV -sC -O -p- 10.0.2.5
```

## 🔍 스캔 및 분석 결과

![](/assets/img/posts/2026-03-28-webproject-week2/2026-03-28-13-25-59.png)

정찰(Reconnaissance) 단계를 통해 파악한 타겟 시스템의 세부 정보는 다음과 같습니다.

* **OS:** Ubuntu (Linux kernel 4.15 - 5.19)
* **Web Server:** Apache httpd 2.4.58
* **Open Ports:** `80/tcp (HTTP)` 활성화 확인

---

## 3. 웹 디렉터리 퍼징 (ffuf) 및 트러블슈팅

웹 서버 내 숨겨진 경로를 찾기 위해 `ffuf`를 사용했습니다. 도구 사용 과정에서 겪은 주요 시행착오와 해결 방안을 기록합니다.

### ⚠️ 이슈 1: 예약어 인식 오류
> **문제:** `FUZZ` 키워드를 소문자로 입력하거나 위치를 잘못 지정하여 스캔이 진행되지 않음.

* **원인:** `ffuf`는 대소문자를 구분하며, 치환될 위치를 나타내는 예약어로 대문자 **`FUZZ`**를 사용해야 합니다.
* **해결:** 명령어 내 퍼징 위치에 대문자 `FUZZ`를 정확히 배치하여 해결했습니다.

### ⚠️ 이슈 2: 타겟 경로(Context Root) 설정 오류
> **문제:** 루트(`http://10.0.2.5/`) 스캔 시 유의미한 결과 부재.

* **원인:** 실제 타겟 애플리케이션(DVWA)이 루트가 아닌 `/DVWA/` 하위 경로에 존재함을 간과했습니다. 
* **해결:** 베이스 URL 경로를 수정하여 정상적으로 내부 디렉터리 구조를 확보했습니다.

---

### ✅ 최종 성공 명령어

반복적인 수정 끝에 최적화된 퍼징 명령어입니다.

```bash
ffuf -u http://10.0.2.5/DVWA/FUZZ \
     -w /usr/share/wordlists/dirb/common.txt \
     -e .php -mc 200,301 -o final_recon.json
```

**[💻 ffuf 디렉터리 스캔 실행 화면]**
![](/assets/img/posts/2026-03-28-webproject-week2/2026-03-28.png)

## 4. 정찰 결과 분석 (`final_recon.json`)

스캔 결과, 공격자 입장에서 치명적인 취약점으로 이어질 수 있는 유의미한 포인트들을 다수 발견했습니다.

| Path | Status | 의미 및 위험성 |
| :--- | :---: | :--- |
| **`/.git/HEAD`** | **200** | **[High]** Git 메타데이터 노출. 소스 코드 복구 가능성 농후 |
| **`/config`** | 301 | 설정 파일 디렉터리 존재 확인 (접근 제어 미흡 가능성) |
| **`/setup.php`** | 200 | DB 초기화 및 설정 페이지 노출 |
| **`/login.php`** | 200 | 3주차 SQL Injection 공격의 핵심 타겟 |
| **`/php.ini`** | 200 | 서버 환경 설정 정보 노출 |

> **💡 Insight:** 특히 `.git` 디렉터리가 노출된 경우, `git-dumper`와 같은 도구를 사용해 전체 소스코드를 덤프할 수 있습니다. 이는 블랙박스 테스트를 **화이트박스 테스트(White-box Testing)** 수준으로 격상시켜 정밀한 취약점 분석을 가능케 합니다.

---

## 5. 데이터 추출 기법 (Kali to Host)

가상환경(Kali) 내에서 생성된 정찰 결과물(`final_recon.json`)을 분석을 위해 로컬 호스트로 이전해야 했습니다. 이때 복잡한 설정 없이 **Python의 간이 HTTP 서버** 기능을 활용했습니다.

### 💻 Kali Linux (공격자 서버 역할)
```bash
# 특정 디렉토리에서 8000번 포트로 HTTP 서버 구동
python3 -m http.server 8000
```

### 🖥️ Host PC (분석 환경)
이후 호스트 PC의 터미널에서 `curl` 명령어를 사용하여 빠르게 로컬 환경으로 결과물을 가져왔습니다.

```bash
# Host PC에서 curl을 이용해 파일 다운로드
curl -O http://10.0.2.15:8000/final_recon.json
```

해당 과정을 통해 실제 추출해 낸 `final_recon.json` 원본 데이터(Raw Data) 구조의 일부는 다음과 같습니다. 이 데이터에서 HTTP 응답 상태(Status)가 `200`이나 `301`인 유효 경로만을 필터링해 앞선 **[4. 정찰 결과 분석]** 단계를 수행할 수 있었습니다.

```json
{
  "results": [
    {
      "input": {
        "FUZZ": ".git/HEAD"
      },
      "status": 200,
      "length": 23,
      "words": 2,
      "lines": 2,
      "content-type": "application/octet-stream",
      "url": "http://10.0.2.5/DVWA/.git/HEAD"
    },
    {
      "input": {
        "FUZZ": "login.php"
      },
      "status": 200,
      "length": 1543,
      "words": 472,
      "lines": 64,
      "content-type": "text/html",
      "url": "http://10.0.2.5/DVWA/login.php"
    }
  ]
}
```

## 6. 결론 및 향후 계획

2주차 정찰(Reconnaissance) 단계를 통해 DVWA의 내부 구조를 명확히 파악할 수 있었습니다. 특히 보안상 매우 치명적인 **잘못된 설정(Misconfiguration)**으로 인해 `.git` 정보나 설정 페이지가 노출되어 있는 것을 확인한 것이 이번 단계의 핵심 성과입니다.

이러한 정보들은 단순한 수집을 넘어, 다음 단계에서 공격의 정밀도를 높이는 결정적인 자산이 됩니다.

---

### 🚀 Next Phase Preview

드디어 다음 주차부터는 본격적인 **공격(Exploitation)** 단계로 진입합니다! 

* **타겟:** 정찰을 통해 확보한 `login.php`
* **공격 기법:** **SQL Injection (SQLi)**
* **목표:** 1. 관리자 권한으로의 **인증 우회(Authentication Bypass)**
  2. 데이터베이스 내부의 민감 정보 **탈취 및 덤프**

실제 침투가 어떻게 이루어지는지, 다음 포스팅에서 상세히 다루도록 하겠습니다.