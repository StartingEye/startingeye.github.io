---
layout: post
title: "[TIL] Jekyll Chirpy 블로그 구축 및 환경 설정"
date: 2026-03-16 19:00:00 +0900
categories: [TIL, Blog]
tags: [github-pages, jekyll, vscode, troubleshooting]
---

## 📅 오늘 학습한 내용
GitHub Pages와 Jekyll(Chirpy 테마)을 이용해 개인 기술 블로그를 구축하고, 효율적인 관리를 위한 VS Code 환경 설정법을 학습했습니다.

---

## 1. 블로그 초기 설정 (Configuration)
`_config.yml` 파일을 수정하여 블로그의 정체성을 설정했습니다.

- **기본 정보**: 블로그 제목(`title`), 부제(`tagline`), 설명(`description`)을 정보보안 전공에 맞춰 수정.
- **URL 설정**: `url: "https://startingeye.github.io"` (마지막에 `/`가 들어가지 않도록 주의!)
- **개인화**: `author` 정보와 SNS 링크(GitHub, Email) 업데이트.
- **언어 및 시간**: `lang: ko`, `timezone: Asia/Seoul`로 변경하여 한국 사용자 환경에 최적화.

## 2. 블로그 제작 및 운영 절차
블로그를 수정하고 배포하는 전체 프로세스는 다음과 같습니다.

1.  **로컬 서버 실행**: `bundle exec jekyll serve`로 변경 사항 실시간 모니터링.
2.  **포스트 작성**: `_posts` 폴더에 `YYYY-MM-DD-제목.md` 규칙에 맞춰 작성.
3.  **데이터 무결성 검사**: `HTML-Proofer`를 통한 깨진 링크 및 인코딩 오류 사전 차단.
4.  **배포**: `git add .` -> `git commit` -> `git push` 단계로 서버 전송.


## ⚠️ 발생했던 주요 오류 및 해결 (Troubleshooting)

### 인코딩 오류 (invalid byte sequence in UTF-8)
- **원인**: 이미지 파일이 `_posts` 폴더 안에 위치하여 Jekyll이 텍스트로 읽으려다 발생.
- **해결**: 이미지를 `assets/img/posts/` 폴더로 이동시키고 절대 경로로 링크 수정.

### Front Matter 렌더링 오류
- **원인**: `index.html` 파일의 최상단 `---` 위에 공백이 있어 파일 형식을 오인함.
- **해결**: 파일의 첫 번째 줄부터 `---`가 시작되도록 수정하여 정상 출력 확인.

---

## 💡 느낀 점
이 작업을 하면서 많은 오류를 경험 했고 그 오류를 해결해 나가면서 반복해 보니 처음 할 때 보다는 
많이 설정 및 설치가 수월해 진것 같다.중간 중간 설정을 잘못하여 처음부터 지웠다가 다시 설치하면서
많이 배울수 있었던거 같다.

## 💡 배운 내용
git을 활용해 markdown을 사용하여 블로그에 이렇게 기록을 남길 수 있고 jekyll 서버 구동 및
github 레퍼지토리에 업로드 하는 방법 까지 많은걸 배울 수 있었던거 같다

## 앞으로 계획
markdown과 git을 이용하여 내가 공부하는 내용을 정리하여서 업로드 해보고자 한다
TIL및 워게임, 보안지식 등 각종 다른 플랫폼에서 공부한걸 정리하면서 내것으로 만들어 볼려고 한다

