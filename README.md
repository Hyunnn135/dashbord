# masterboard

현태(`hyunnn135`)의 1인 개발 프로젝트 마스터보드 — 4개 프로젝트(월급연구소·맥스아웃·Nudge·부동산연구소)의 진행 상황·태스크·devlog 를 한 곳에서 관리하는 정적 HTML 대시보드.

> 컨텍스트 / 데이터 스키마 / 카테고리 색상 등 자세한 내용은 [`PROJECT_CONTEXT.md`](PROJECT_CONTEXT.md) 참고.

---

## 빠른 시작

```bash
cd ~/Desktop/Projects/masterboard
# 데이터는 정적 — 빌드 없음, 그냥 브라우저로 열기
open index.html
```

특정 프로젝트 상세 보드:
- `project.html?slug=salarykorea`
- `project.html?slug=maxout`
- `project.html?slug=nudge`
- `project.html?slug=realestate`

---

## 파일 구조

| 파일 | 역할 |
|-----|------|
| `index.html` | 홈 — 4개 프로젝트 카드 + 타임라인 + Recent devlog |
| `project.html` | 단일 템플릿 — `?slug=...` 쿼리로 4개 프로젝트 커버 |
| `blog-upload-board.html` | 네이버 블로그 업로드 워크플로 (salarykorea 전용) |
| `devlog.html` | devlog 인덱스 뷰 |
| `project-data.{js,json}` | 단일 진실 소스 (JS = 브라우저용, JSON = 사람/외부용, byte-exact 동일) |
| `update-devlog-index.py` | devlog 자동 인덱싱 스크립트 |
| `devlog/index.{json,js}` | devlog 인덱스 자동 생성 산출물 |
| `devlog/*.md` | 날짜·프로젝트별 작업 일지 |

---

## devlog 인덱스 빌드 프로세스

### 1. 새 devlog 추가

파일명 규칙: `devlog/YYYY-MM-DD-{프로젝트}[-N].md`

- 프로젝트: `salarykorea` / `maxout` / `nudge` / `masterboard` / `realestate`
- 같은 날 같은 프로젝트 2회차 이상: `-2`, `-3`, ... 접미사

예시:
```
devlog/2026-04-22-nudge.md
devlog/2026-04-15-nudge-3.md   ← 3차 세션
```

### 2. 첫 줄 헤더

```markdown
# [프로젝트명] YYYY-MM-DD — 한 줄 요약
```

또는

```markdown
# Nudge 2026-04-22 — Complication 정합화 마무리
```

> 스크립트의 `clean_title()` 이 두 형식 모두 처리.

### 3. 인덱스 재생성

```bash
cd ~/Desktop/Projects/masterboard
python3 update-devlog-index.py
```

산출물:
- `devlog/index.json` — 사람/외부 도구용 (날짜 내림차순, 같은 날엔 세션 번호 내림차순)
- `devlog/index.js` — 브라우저용 (`window.DEVLOG_INDEX = [...]`, `<script src="devlog/index.js">` 로 로드)

### 4. 자동 반영되는 곳

- `index.html` Recent devlog 섹션 (최신 N개)
- `project.html` 프로젝트별 devlog 섹션 (slug 필터 후 최대 15개)

### 5. Idempotent 보장

같은 인풋에 대해 스크립트 재실행 시 산출물은 **byte-exact 동일**. 변경이 있을 때만 git status 에 잡힘.

---

## 데이터 추가 워크플로

### 새 태스크 추가
1. `project-data.js` 의 `tasks[]` 에 항목 추가 (필드: `id` / `project` / `title` / `status` / `priority` / `phase` / `tags` / `createdAt` / `completedAt` / `notes`)
2. `project-data.json` 도 같이 갱신 (`.js` 와 byte-exact 동일하게)
3. `index.html` 새로고침 → 해당 프로젝트 카드 진행도 자동 반영

### 태스크 상태 변경 (예: now → done)
1. `status` 필드를 `done` 으로 변경
2. `completedAt` 에 완료 날짜 기록
3. 두 파일 동시 갱신

### 새 프로젝트 추가
1. `project-data.js` 의 `projects[]` 에 한 행 추가 (`slug` / `name` / `icon` / `themeColor` / `oneLiner` / `phases` / `currentPhase` / `linkedDocs`)
2. 해당 슬러그로 `project.html?slug=새프로젝트` 자동 렌더링됨
3. 태스크는 `project` 필드에 그 슬러그 적어서 추가

---

## 데이터 스키마 v2

Phase 1 (2026-04-18) 에 도입, Phase 4 (2026-05-17) 에서 레거시 필드 정리 완료. 자세한 필드 정의는 [`PROJECT_CONTEXT.md`](PROJECT_CONTEXT.md) 의 "데이터 스키마" 섹션.

---

## 관련 문서

- [`PROJECT_CONTEXT.md`](PROJECT_CONTEXT.md) — 데이터 스키마 / 카테고리 색상 / 동기화 규칙
- `~/Desktop/Projects/WORKFLOW_RULES.md` — 모든 프로젝트 공통 마스터 규칙 (0순위 필독)
- `devlog/` — 작업 일지 아카이브
