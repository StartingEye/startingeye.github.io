---
title: "2026-1학기 중간고사 준비 - setup-script 실습 풀이 및 명령어 설명"
date: 2026-04-19 20:00:00 +0900
categories: [리눅스, 실습]
tags: [리눅스, Ubuntu, 중간고사, bash, 명령어]
---

안녕하세요! 
[2026-1학기 중간고사 - setup-script.sh 실습 환경 20문제]

---

### 문제 1: 현재 경로 확인
![문제 1 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/1번.png)
- **정답 명령어:** `pwd`
- **명령어 설명:** `pwd`는 **Print Working Directory**의 약자로, 현재 작업 중인 디렉터리의 절대 경로를 터미널 화면에 출력해 주는 기본 명령어입니다.

---

### 문제 2: 특정 디렉터리로 이동 후 내용 확인
![문제 2 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/2번.png)
- **정답 명령어:** `cd ~/security` 다음 줄에 `ls`
- **명령어 설명:** `cd` 명령어는 Change Directory의 약자로, `~` (홈 디렉터리) 하위의 `security` 폴더로 위치를 변경합니다. 이동 후 `ls` 명령어를 사용하여 현재 폴더 안의 파일 및 디렉터리 목록을 확인합니다.

---

### 문제 3: 숨김 파일 포함 모든 파일 확인
![문제 3 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/3번.png)
- **정답 명령어:** `ls -la ~/security/samples`
- **명령어 설명:** `ls`에 `-a` 옵션을 통해 이름이 `.`(점)으로 시작하는 숨김 파일(`.htaccess` 등)을 포함해 모든 파일을 보여주도록 하고, `-l` 옵션으로 파일의 상세 정보 리스트(권한, 소유자, 크기 등)를 함께 출력합니다. 

---

### 문제 4: 파일 내용 출력
![문제 4 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/4번.png)
- **정답 명령어:** `cat ~/security/config/sshd_config`
- **명령어 설명:** `cat` 명령어는 지정한 텍스트 파일의 내용을 처음부터 끝까지 그대로 터미널에 출력해 주는 역할을 하므로 파일 내용을 빠르게 열람할 때 사용됩니다.

---

### 문제 5: 파일 상세 정보 및 권한 파악
![문제 5 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/5번.png)
- **정답 명령어:** `ls -l ~/security/samples/suspicious_script.sh`
- **명령어 설명:** `-l` 옵션이 표시하는 속성에서 첫 번째 부분은 파일/디렉터리 여부 및 권한 정보입니다. `-rw-r--r--`처럼 표현되며, 여기서 실행 권한을 뜻하는 `x`가 빠져 있으므로 모두에게 실행 권한이 없음을 알 수 있습니다.

---

### 문제 6: 소유자 단위 권한 부여
![문제 6 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/6번.png)
- **정답 명령어:** `chmod u+x ~/security/samples/suspicious_script.sh` 이후 `ls -l`
- **명령어 설명:** `chmod`(Change Mode) 명령어로 특정한 권한을 수정할 수 있습니다. `u+x`는 **User(소유자)**에게 **eXecute(실행)** 권한을 **+(추가)**하라는 의미입니다. 그 결과 권한 리스트가 `-rwxr--r--`로 업데이트됩니다.

---

### 문제 7: 지정 문자열 포함된 라인 출력
![문제 7 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/7번.png)
- **정답 명령어:** `grep "Failed" ~/security/logs/auth.log`
- **명령어 설명:** `grep` 명령어를 사용하여 파일 내에서 특정 문자열 패턴(여기서는 `Failed`)이 포함된 라인을 검색하여 텍스트 그대로 찾아줍니다.

---

### 문제 8: 지정 문자열 포함된 라인의 개수 출력
![문제 8 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/8번.png)
- **정답 명령어:** `grep -c "Failed" ~/security/logs/auth.log`
- **명령어 설명:** `grep`의 `-c` (count) 옵션을 사용하면 문자열을 포함하는 문장을 모두 보여주지 않고 일치하는 행의 총 **숫자 하나**만 요약해서 출력해 줍니다.

---

### 문제 9: 404가 포함된 줄의 라인수 파이프로 구하기
![문제 9 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/9번.png)
- **정답 명령어:** `grep "404" ~/security/logs/access.log | wc -l`
- **명령어 설명:** 먼저 `grep`으로 조건에 맞는 라인을 필터링한 후 `|`(파이프)를 통해 라인의 수를 세어주는 `wc -l` (Word Count - Lines) 명령어의 입력으로 넘깁니다. 이는 `grep -c`와 논리적으로 동일한 결과를 가져옵니다.

---

### 문제 10: 특정 필드만 출력하기
![문제 10 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/10번.png)
- **정답 명령어:** `awk '{print $1}' ~/security/logs/access.log`
- **명령어 설명:** `awk`는 텍스트를 공백 또는 탭 기준으로 필드를 잘라주는 매우 강력한 도구입니다. `{print $1}` 옵션을 주면 각 줄에서 첫 번째 열(`$1`)에 해당하는 IP 주소 부분만 성공적으로 추출합니다.

---

### 문제 11: 파이프를 이용한 정렬 및 집계 처리
![문제 11 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/11번.png)
- **정답 명령어:** `awk '{print $1}' ~/security/logs/access.log | sort | uniq -c | sort -rn`
- **명령어 설명:** 
1. `awk`로 IP를 추출하여 
2. `sort`로 정렬(같은 IP를 묶음)
3. `uniq -c`로 중복 항목을 하나의 줄로 합치면서 발생 횟수를 세고
4. `sort -rn`으로 Reverse(내림차순, `-r`)이자 숫자 기준(`-n`) 정렬로 바꾸어 호출 횟수가 많은 순으로 정렬합니다.

---

### 문제 12: 뒤에서 N번째 항목 조건 출력하기
![문제 12 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/12qjs.png)
- **정답 명령어:** `grep "Failed password" ~/security/logs/auth.log | awk '{print $(NF-3)}'`
- **명령어 설명:** 성공/실패 로그 행의 길이가 달라도 `NF` (Number of Fields) 기능을 활용해 "가장 마지막 필드를 기준으로" 위치에 접근할 수 있습니다. 즉 `$(NF-3)`로 뒤에서 네 번째 열에 위치한 IP 주소를 정확히 뽑아냅니다. (참고: 사진 파일명 12qjs)

---

### 문제 13: 특정 구분자 자르기
![문제 13 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/13번.png)
- **정답 명령어:** `cut -d: -f1 ~/security/system/passwd`
- **명령어 설명:** `cut` 명령어를 사용하여 콜론(`:`) 구분자(`-d:`)를 기준으로 문서의 각 줄을 나누고, 첫 번째 필드(`-f1`)인 사용자 이름만 출력합니다.

---

### 문제 14: 조건에 맞는 파일 검색하기
![문제 14 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/14번.png)
- **정답 명령어:** `find ~/security -name "*.log"`
- **명령어 설명:** `find` 명령어는 지정된 경로에서 특정 조건의 파일을 검색합니다. `-name` 옵션에 `"*.log"` 패턴을 주어 확장자가 `.log`인 파일을 모두 찾습니다.

---

### 문제 15: 디렉터리 디스크 점유율 파악
![문제 15 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/15번.png)
- **정답 명령어:** `du -h --max-depth=1 ~/security`
- **명령어 설명:** `du` 명령어로 디스크 사용량을 확인합니다. `-h`를 사용해 사람이 읽기 쉬운 단위(KB, MB 등)로 표시하며, `--max-depth=1`로 하위 디렉터리 1단계까지만 요약합니다.

---

### 문제 16: 파일 및 디렉터리 압축
![문제 16 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/16번.png)
- **정답 명령어:** `tar -czf ~/security/reports/logs_backup.tar.gz -C ~/security logs`
- **명령어 설명:** `tar` 명령어에 `-c`(생성), `-z`(gzip압축), `-f`(파일명 지정) 옵션을 주어 압축합니다. `-C` 옵션은 대상 디렉터리로 이동 후 압축을 진행하게 합니다.

---

### 문제 17: 명령 출력 리다이렉션으로 텍스트 파일 저장
![문제 17 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/17번.png)
- **정답 명령어:** `grep "404" ~/security/logs/access.log > ~/security/reports/404_report.txt`
- **명령어 설명:** `>` 리다이렉션을 사용하여 명령어의 표준 출력(결과)을 화면이 아닌 지정한 파일에 차례대로 기록합니다.

---

### 문제 18: 환경 변수와 셸 스크립트
![문제 18 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/18번.png)
- **정답 명령어:** Here Document (`cat << 'EOF' > ...`) 방식과 실행 권한 추가 및 동작 파일 확인
- **명령어 설명:** `cat << EOF` 형식의 Here Document 구문 구조를 사용하여 여러 줄의 텍스트를 파일로 한 번에 작성합니다. `${NAME}` 환경 변수를 사용해 문자열에 이름을 치환합니다.

---

### 문제 19: 조건문을 사용한 존재하는 파일인지 체크
![문제 19 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/19번.png)
- **정답 명령어:** `if [ -f ~/security/logs/auth.log ]; then ...` 블록 동작
- **명령어 설명:** Bash 조건문인 `if` 문을 사용하였으며, `[ -f 파일경로 ]` 구문을 통해 해당 파일(일반 파일)이 실제로 존재하는지를 먼저 검사하여 결과에 따라 분기합니다.

---

### 문제 20: 로그 파일을 순회하며 오류 카운트
![문제 20 실습](/assets/img/posts/2026-04-19-linux-midterm-prep/20번.png)
- **정답 명령어:** `for FILE in ~/security/logs/*.log; do ...` 내의 `grep -ic` 등
- **명령어 설명:** `for` 문으로 `.log` 확장자를 가진 파일 목록을 순회합니다. 루프 안에서 `grep -ic` 명령어로 각 로그 파일별 오류(error; 대소문자 무시) 카운트를 세어 반복 처리합니다.

---

이번 포스트가 중간고사 대비에 많은 도움이 되셨기를 바랍니다. 파이팅!
