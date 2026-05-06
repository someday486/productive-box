# productive-box 사용 메뉴얼

## 목차

1. [프로젝트 소개](#1-프로젝트-소개)
   - 1.1 [개요](#11-개요)
   - 1.2 [주요 기능](#12-주요-기능)
   - 1.3 [동작 원리](#13-동작-원리)

2. [사전 준비](#2-사전-준비)
   - 2.1 [GitHub Gist 생성](#21-github-gist-생성)
   - 2.2 [GitHub 토큰 발급](#22-github-토큰-발급)

3. [프로젝트 설정](#3-프로젝트-설정)
   - 3.1 [저장소 포크](#31-저장소-포크)
   - 3.2 [GitHub Actions 활성화](#32-github-actions-활성화)
   - 3.3 [환경 변수 설정](#33-환경-변수-설정)
   - 3.4 [Secret 등록](#34-secret-등록)
   - 3.5 [Gist 프로필 고정](#35-gist-프로필-고정)

4. [코드 구조](#4-코드-구조)
   - 4.1 [디렉토리 구조](#41-디렉토리-구조)
   - 4.2 [주요 파일 설명](#42-주요-파일-설명)
   - 4.3 [src/index.ts](#43-srcindexts)
   - 4.4 [src/githubQuery.ts](#44-srcgithubqueryts)
   - 4.5 [src/queries.ts](#45-srcqueriests)
   - 4.6 [src/generateBarChart.ts](#46-srcgeneratebarchartts)

5. [자동화 워크플로우](#5-자동화-워크플로우)
   - 5.1 [GitHub Actions 스케줄 설정](#51-github-actions-스케줄-설정)
   - 5.2 [워크플로우 동작 흐름](#52-워크플로우-동작-흐름)
   - 5.3 [환경 변수 목록](#53-환경-변수-목록)

6. [GitHub GraphQL API 활용](#6-github-graphql-api-활용)
   - 6.1 [사용자 정보 조회](#61-사용자-정보-조회)
   - 6.2 [기여 저장소 조회](#62-기여-저장소-조회)
   - 6.3 [커밋 날짜 조회](#63-커밋-날짜-조회)

7. [생산성 분석 로직](#7-생산성-분석-로직)
   - 7.1 [시간대 구분 기준](#71-시간대-구분-기준)
   - 7.2 [커밋 집계 방식](#72-커밋-집계-방식)
   - 7.3 [바 차트 생성](#73-바-차트-생성)
   - 7.4 [결과 유형 판정](#74-결과-유형-판정)

8. [Gist 업데이트](#8-gist-업데이트)
   - 8.1 [Octokit REST API 사용](#81-octokit-rest-api-사용)
   - 8.2 [Gist 파일명 동적 변경](#82-gist-파일명-동적-변경)
   - 8.3 [출력 형식 예시](#83-출력-형식-예시)

9. [개발 환경 구성](#9-개발-환경-구성)
   - 9.1 [필수 도구](#91-필수-도구)
   - 9.2 [의존성 설치](#92-의존성-설치)
   - 9.3 [로컬 빌드 방법](#93-로컬-빌드-방법)
   - 9.4 [ESLint 설정](#94-eslint-설정)

10. [타임존 설정 가이드](#10-타임존-설정-가이드)
    - 10.1 [지원 타임존 목록](#101-지원-타임존-목록)
    - 10.2 [타임존 변경 방법](#102-타임존-변경-방법)

11. [문제 해결 (Troubleshooting)](#11-문제-해결-troubleshooting)
    - 11.1 [Actions가 실행되지 않을 때](#111-actions가-실행되지-않을-때)
    - 11.2 [Gist가 업데이트되지 않을 때](#112-gist가-업데이트되지-않을-때)
    - 11.3 [토큰 권한 오류](#113-토큰-권한-오류)
    - 11.4 [커밋 데이터가 없을 때](#114-커밋-데이터가-없을-때)

12. [참고 자료](#12-참고-자료)
    - 12.1 [관련 프로젝트](#121-관련-프로젝트)
    - 12.2 [공식 문서 링크](#122-공식-문서-링크)
    - 12.3 [라이선스](#123-라이선스)

---

## 1. 프로젝트 소개

### 1.1 개요

productive-box는 GitHub 커밋 이력을 분석하여 사용자가 하루 중 어느 시간대에 가장 활발하게 활동하는지 GitHub Gist에 시각화해주는 GitHub Action 프로젝트입니다.

### 1.2 주요 기능

- GitHub 커밋 타임스탬프 수집 및 분석
- 시간대별(아침/낮/저녁/밤) 커밋 분포 시각화
- 결과를 GitHub Gist에 자동 업데이트
- 매일 자정 자동 실행 (GitHub Actions 스케줄)

### 1.3 동작 원리

GitHub GraphQL API로 커밋 시각 데이터를 수집하고, 시간대별로 집계한 뒤 바 차트 형태로 Gist에 기록합니다.

---

## 2. 사전 준비

### 2.1 GitHub Gist 생성

공개(public) Gist를 생성합니다. Gist URL에서 ID 값을 복사해 둡니다.

### 2.2 GitHub 토큰 발급

`gist` 및 `repo` 권한이 포함된 Personal Access Token을 발급합니다.

---

## 3. 프로젝트 설정

### 3.1 저장소 포크

### 3.2 GitHub Actions 활성화

### 3.3 환경 변수 설정

`.github/workflows/schedule.yml`에서 `GIST_ID`와 `TIMEZONE`을 설정합니다.

### 3.4 Secret 등록

저장소 **Settings > Secrets**에서 `GH_TOKEN`을 등록합니다.

### 3.5 Gist 프로필 고정

생성된 Gist를 GitHub 프로필에 고정합니다.

---

## 4. 코드 구조

### 4.1 디렉토리 구조

```
productive-box/
├── src/
│   ├── index.ts           # 메인 실행 파일
│   ├── githubQuery.ts     # GraphQL API 요청 모듈
│   ├── queries.ts         # GraphQL 쿼리 정의
│   └── generateBarChart.ts # 바 차트 생성 모듈
├── dist/
│   └── index.js           # 빌드 결과물
├── .github/
│   └── workflows/
│       └── schedule.yml   # GitHub Actions 워크플로우
├── action.yml             # GitHub Action 메타데이터
└── package.json
```

### 4.2 주요 파일 설명

| 파일 | 역할 |
|------|------|
| `src/index.ts` | 전체 흐름 제어, Gist 업데이트 |
| `src/githubQuery.ts` | GitHub GraphQL API 호출 |
| `src/queries.ts` | GraphQL 쿼리 문자열 정의 |
| `src/generateBarChart.ts` | 퍼센트 기반 바 차트 문자열 생성 |

### 4.3 src/index.ts

메인 진입점으로, 사용자 정보 조회 → 기여 저장소 조회 → 커밋 시각 집계 → Gist 업데이트 순서로 실행됩니다.

### 4.4 src/githubQuery.ts

`GH_TOKEN`을 Bearer 토큰으로 사용해 GitHub GraphQL 엔드포인트(`https://api.github.com/graphql`)에 POST 요청을 보냅니다.

### 4.5 src/queries.ts

3개의 GraphQL 쿼리를 정의합니다: 사용자 정보, 기여 저장소 목록, 커밋 날짜 이력.

### 4.6 src/generateBarChart.ts

퍼센트 값을 받아 막대 문자열을 생성합니다.

---

## 5. 자동화 워크플로우

### 5.1 GitHub Actions 스케줄 설정

매일 자정(UTC 00:00) 또는 master 브랜치 push 시 실행됩니다.

### 5.2 워크플로우 동작 흐름

```
Push / 스케줄 트리거 → update-gist 잡 실행 → productive-box Action 실행 → Gist 업데이트
```

### 5.3 환경 변수 목록

| 변수명 | 설명 | 설정 위치 |
|--------|------|-----------|
| `GH_TOKEN` | GitHub Personal Access Token | Repository Secret |
| `GIST_ID` | 업데이트할 Gist ID | Repository Secret 또는 workflow 직접 입력 |
| `TIMEZONE` | 사용자 타임존 (예: `Asia/Seoul`) | `schedule.yml` |

---

## 6. GitHub GraphQL API 활용

### 6.1 사용자 정보 조회

`viewer` 쿼리로 현재 토큰 소유자의 `login`(username)과 `id`를 조회합니다.

### 6.2 기여 저장소 조회

`repositoriesContributedTo`로 최근 100개의 기여 저장소(포크 제외)를 조회합니다.

### 6.3 커밋 날짜 조회

각 저장소의 master 브랜치에서 해당 사용자의 최근 100개 커밋 날짜를 조회합니다.

---

## 7. 생산성 분석 로직

### 7.1 시간대 구분 기준

| 시간대 | 범위 | 아이콘 |
|--------|------|--------|
| Morning (아침) | 06:00 ~ 11:59 | 🌞 |
| Daytime (낮) | 12:00 ~ 17:59 | 🌆 |
| Evening (저녁) | 18:00 ~ 23:59 | 🌃 |
| Night (밤) | 00:00 ~ 05:59 | 🌙 |

### 7.2 커밋 집계 방식

커밋의 `committedDate`를 지정 타임존 기준으로 변환 후 시(hour) 값으로 시간대를 판별하여 카운트합니다.

### 7.3 바 차트 생성

전체 커밋 수 대비 각 시간대 비율을 퍼센트로 계산하여 21칸짜리 바 차트를 생성합니다.

### 7.4 결과 유형 판정

아침+낮 커밋 합계가 저녁+밤 커밋 합계보다 많으면 **"I'm an early 🐤"**, 그렇지 않으면 **"I'm a night 🦉"** 로 Gist 파일명을 설정합니다.

---

## 8. Gist 업데이트

### 8.1 Octokit REST API 사용

`@octokit/rest` 라이브러리를 통해 Gist를 조회하고 업데이트합니다.

### 8.2 Gist 파일명 동적 변경

분석 결과에 따라 Gist의 파일명이 자동으로 변경됩니다.

### 8.3 출력 형식 예시

```
🌞 Morning      142 commits   ███████████░░░░░░░░░░  52.8%
🌆 Daytime       56 commits   ████░░░░░░░░░░░░░░░░░  20.8%
🌃 Evening       43 commits   ███░░░░░░░░░░░░░░░░░░  16.0%
🌙 Night         28 commits   ██░░░░░░░░░░░░░░░░░░░  10.4%
```

---

## 9. 개발 환경 구성

### 9.1 필수 도구

- Node.js
- Yarn

### 9.2 의존성 설치

```bash
yarn install
```

### 9.3 로컬 빌드 방법

```bash
yarn build
```

`.env` 파일에 `GH_TOKEN`, `GIST_ID`, `TIMEZONE`을 설정한 후 실행합니다.

### 9.4 ESLint 설정

`.eslintrc.js`에 TypeScript 기반 린트 규칙이 정의되어 있습니다.

---

## 10. 타임존 설정 가이드

### 10.1 지원 타임존 목록

IANA 타임존 데이터베이스 형식을 사용합니다.

| 지역 | 타임존 값 |
|------|-----------|
| 한국 | `Asia/Seoul` |
| 일본 | `Asia/Tokyo` |
| 미국 동부 | `America/New_York` |
| 미국 서부 | `America/Los_Angeles` |
| 영국 | `Europe/London` |

### 10.2 타임존 변경 방법

`.github/workflows/schedule.yml`의 `TIMEZONE` 값을 수정합니다.

---

## 11. 문제 해결 (Troubleshooting)

### 11.1 Actions가 실행되지 않을 때

저장소의 Actions 탭에서 워크플로우가 활성화되어 있는지 확인합니다.

### 11.2 Gist가 업데이트되지 않을 때

`GIST_ID`가 올바른지, Gist가 공개(public) 상태인지 확인합니다.

### 11.3 토큰 권한 오류

`GH_TOKEN`에 `gist`와 `repo` 권한이 모두 포함되어 있는지 확인합니다.

### 11.4 커밋 데이터가 없을 때

분석 대상 기간에 커밋이 존재하지 않으면 Gist가 업데이트되지 않습니다.

---

## 12. 참고 자료

### 12.1 관련 프로젝트

- [awesome-pinned-gists](https://github.com/matchai/awesome-pinned-gists) — 핀드 Gist 프로젝트 모음

### 12.2 공식 문서 링크

- [GitHub GraphQL API 문서](https://docs.github.com/en/graphql)
- [Octokit REST.js](https://github.com/octokit/rest.js)
- [GitHub Actions 문서](https://docs.github.com/en/actions)

### 12.3 라이선스

MIT License — 자세한 내용은 `LICENSE` 파일을 참고하세요.
