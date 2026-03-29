---
layout: post
title: "[Linux] 사용자 및 그룹 관리 핵심 요약 (adduser, usermod, groupadd)"
date: 2026-03-29
categories: [Linux, 실습]
tags: [리눅스, 사용자관리, 그룹관리, adduser, usermod]
---

# 1. 리눅스 계정의 이해
리눅스는 멀티유저(Multi-user) 운영체제로, 접속하는 모든 사용자 주체는 각자의 계정으로 관리됩니다. 모든 계정은 고유한 숫자 식별자인 UID(User ID)를 부여받으며, 시스템은 UID를 기준으로 권한 검사를 수행합니다.

* **root 사용자**: UID 0, 시스템의 모든 권한을 가진 관리자
* **시스템 사용자**: UID 1~999, 서비스 실행을 위한 계정 (www-data, sshd 등)
* **일반 사용자**: UID 1000 이상, 개인 업무용 계정으로 우리가 주로 발급받는 계정입니다.

# 2. 계정 생성 및 관리
## 2.1 계정 생성: `adduser` vs `useradd`
계정을 만드는 명령어는 크게 2가지가 있습니다. 가급적 기본 설정이 자동으로 적용되는 `adduser` 사용을 권장합니다.
- **`adduser`**: 사용자와 상호작용하며 비밀번호 설정, 홈 디렉터리 생성 및 기본 뼈대를 알아서 잡아주는 **고수준 명령어** (초보자 권장)
- **`useradd`**: 모든 옵션을 명령줄에서 직접 지정해야 하는 **저수준 명령어**로 세부적인 설정이 필요하거나 스트립트 자동화에 주로 사용합니다.

```bash
sudo adduser [새_계정명]
# 예: sudo adduser new_user
```

**[💻 adduser 명령어 실행 과정]**
![](/assets/img/posts/2026-03-29-linux-user-group-management/adduser-prompt.png)

## 2.2 계정 정보 확인 및 수정 (`usermod`)
생성된 계정들은 시스템의 주요 관리 파일인 `/etc/passwd` 파일에서 속성을 확인할 수 있습니다.
```bash
grep "^[계정명]::" /etc/passwd
```
> 출력 예: `new_user:x:1001:1001:,,,:/home/new_user:/bin/bash`
> 위처럼 `x`로 표시된 비밀번호는 평문이 아닌 해시값으로 안전하게 `/etc/shadow` 파일에 저장되며 오직 root 권한으로만 확인할 수 있습니다.

**[💻 /etc/passwd 파일 구조 확인]**
![](/assets/img/posts/2026-03-29-linux-user-group-management/etc-passwd.png)

계정의 코멘트(GECOS) 등을 추가/수정하려면 `usermod` 를 활용합니다.
```bash
sudo usermod -c "[추가 설명]" [계정명]
```

## 2.3 계정 삭제 (`deluser`)
계정을 삭제할 때 보통 홈 디렉터리도 함께 지우는 경우가 많기 때문에 `--remove-home` 옵션을 추가해줍니다.
```bash
sudo deluser --remove-home [계정명]
```

# 3. 그룹 (Group) 관리
그룹을 이용하면 여러 명의 사용자에게 통일된 권한을 일괄 부여하기 편리합니다.
모든 사용자는 기본 그룹 1개와 여러 개의 보조 그룹에 소속될 수 있습니다.

**[💻 /etc/group 파일 구조 확인 (그룹 및 구성원)]**
![](/assets/img/posts/2026-03-29-linux-user-group-management/etc-group.png)

## 3.1 그룹 생성
```bash
sudo addgroup [그룹명]   # (또는 sudo groupadd [그룹명])
```

## 3.2 사용자를 보조 그룹에 추가
**💡 [주의]** 지정한 계정을 특정 보조 그룹에 추가할 때는 `usermod -aG`를 씁니다. 만약 `-a` (append) 옵션 없이 `-G` 만 쓰면, 기존에 속해 있던 다른 보조 그룹에서 모두 탈퇴되고 지정한 보조 그룹에만 남게 되므로 큰 장애를 유발할 수 있습니다.
```bash
sudo usermod -aG [그룹명] [계정명]
# 예: sudo usermod -aG developers new_user
```

## 3.3 소속 그룹 확인
특정 계정이 어떤 그룹에 속해있는지 확인하려면 `groups` 명령어를 사용합니다. 시스템 전체의 그룹 목록은 `/etc/group` 파일에 관리됩니다.
```bash
groups [계정명]
```

# 4. 주요 구성 파일 요약

| 파일 경로 | 관리 정보 | 권한 (퍼미션) | 접근 허용 수준 |
| :--- | :--- | :--- | :--- |
| `/etc/passwd` | 사용자 기본 정보 (UID/GID 포함) | 644 (`-rw-r--r--`) | 누구나 읽기 가능 |
| `/etc/shadow` | 문자열 해시 암호화된 사용자 비밀번호 | 640 (`-rw-r-----`) | root 만 읽기 가능 |
| `/etc/group` | 그룹별 GID와 멤버 소속 정보 | 644 (`-rw-r--r--`) | 누구나 읽기 가능 |
| `/etc/gshadow` | 그룹 비밀번호 해시 | 640 (`-rw-r-----`) | root 만 읽기 가능 |
