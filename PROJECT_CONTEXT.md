# masterboard — 프로젝트 컨텍스트

> ⚠️ **0순위 필독:** `~/Desktop/Projects/WORKFLOW_RULES.md` (모든 프로젝트 공통 마스터 규칙)
> 이 파일은 masterboard 프로젝트의 핵심 컨텍스트입니다.
> **최종 업데이트: 2026-05-17 (Phase 4 완료 — 구 보드 제거 + 레거시 필드 정리)**

---

## 프로젝트 개요

- **역할:** 모든 프로젝트(salarykorea, maxout, nudge, realestate)의 메인 허브 / 포트폴리오
- **위치:** `~/Desktop/Projects/masterboard/`
- **기술:** HTML + CSS + Vanilla JS (단일 페이지, 빌드 없음)
- **데이터 소스:** `project-data.json` (단일 진실 소스, `.js`와 항상 동기화)
- **devlog 인덱스:** `devlog/index.json` (사람/외부용) + `devlog/index.js` (브라우저용, `window.DEVLOG_INDEX`)

---

## 핵심 파일 구조

| 파일 | 역할 |
|-----|------|
| `index.html` | 메인 허브 — 4개 프로젝트 카드, 타임라인, Recent devlog (window.DEVLOG_INDEX 동적 렌더) |
| `project.html` | **단일 프로젝트 상세 템플릿** — `?slug=salarykorea\|maxout\|nudge\|realestate` 쿼리 분기로 4개 프로젝트 커버 |
| `blog-upload-board.html` | 네이버 블로그 업로드 워크플로 (salarykorea 전용) |
| `devlog.html` | devlog 인덱스 뷰 |
| `project-data.js` | 브라우저 로드용 (`window.PROJECT_DATA`) |
| `project-data.json` | 사람/외부 도구용 (JS 와 byte-exact 동일 데이터) |
| `update-devlog-index.py` | `devlog/*.md` 스캔 → `index.json` + `index.js` 자동 생성 (idempotent) |

> Phase 4 (2026-05-17) 에서 삭제된 옛 보드: `project-masterboard.html`, `maxout-board.html`, `nudge-board.html`, `index.old.html`, `project-data.backup.js`, `project-data.backup.json` — 모두 `project.html` 단일 템플릿으로 대체됨

---

## 데이터 스키마 (v2 — Phase 1 도입, Phase 4 정합화)

### projects[] 필드
- `slug` — 식별자 (`salarykorea` / `maxout` / `nudge` / `realestate`)
- `name`, `icon`, `themeColor`, `status` (`active` / `paused`)
- `oneLiner` — 한 줄 설명
- `phases[]`, `currentPhase` — 단계 진행도
- `linkedDocs` — `{context, progress}` (각 프로젝트 폴더 내 문서 경로)

### tasks[] 필드
- `id` — 고유 식별자 (예: `n10g`, `r1`, `w5`)
- `project` — 슬러그 참조
- `title`, `status` (`now` / `next` / `later` / `done` / `archived`), `priority` (`high` / `mid` / `low`)
- `phase` — 해당 프로젝트의 phase 이름 또는 null
- `tags[]` — 카테고리 태그
- `createdAt`, `completedAt` — 날짜 (yyyy-mm-dd)
- `notes` — 자유 메모

> ❌ **제거된 레거시 필드 (Phase 4 2026-05-17):** `text` (= title 중복), `done` (= status 중복), `tag` / `tagColor` (= tags + project.themeColor 중복). 84개 태스크 × 4필드 제거 → 데이터 25% 감축.

---

## 데이터 동기화 규칙

- `project-data.js`와 `project-data.json` 은 **항상 동일한 내용** 유지 (Python 스크립트로 한쪽에서 생성하면 다른 쪽도 같이 갱신)
- 작업 추가/완료/상태 변경 시 두 파일 모두 업데이트
- 각 프로젝트의 `PROJECT_CONTEXT.md` / `PROGRESS.md` 와도 동기화 (양방향)

---

## devlog 인덱스 빌드 프로세스

1. 새 devlog 추가: `~/Desktop/Projects/masterboard/devlog/YYYY-MM-DD-{프로젝트}[-N].md`
2. `cd ~/Desktop/Projects/masterboard && python3 update-devlog-index.py`
3. `devlog/index.json` + `devlog/index.js` 자동 생성 (날짜 내림차순 정렬)
4. `index.html` 의 Recent devlog 섹션 + `project.html` 의 프로젝트별 devlog 섹션이 자동 반영

> 자세한 빌드 프로세스 + 파일명 규칙 + idempotent 보장은 [`README.md`](README.md) 참고

---

## 프로젝트별 카테고리 색상 (themeColor)

| 프로젝트 | icon | themeColor |
|---------|------|-----------|
| 월급연구소 | 💰 | `#3b82f6` (파랑) |
| 맥스아웃 | 💪 | `#fb923c` (주황) |
| Nudge | 🎯 | `#0d9488` (민트) |
| 부동산연구소 | 🏠 | `#64748b` (회색) |

---

## 진행 중인 태그 (tags 필드)

salarykorea: 사이트 / 블로그 / SEO / 도구 / 수익화 / 전략 / 측정 / 긴급액션  
maxout: Phase1~Phase4 / UI / 카운팅 / IAP  
nudge: Phase1~Phase4 / 위젯 / 컴플리케이션 / 동기화 / 진단 / 드리프트정합화 / 버그  
realestate: MVP / 도메인

---

## Phase 4 완료 (2026-05-17) 의 의미

masterboard 자체 리팩토링이 마무리됨. 이후로는 **마스터보드는 다른 프로젝트들의 진행도를 비추는 거울 역할** 만 하고 자체 구조 변경은 없음. 새 프로젝트 추가 시 = `project-data.js` 의 `projects[]` 에 한 행 + `tasks[]` 에 항목 추가만 하면 `project.html?slug=새프로젝트` 가 자동 렌더링.

---

> **읽기 순서:** WORKFLOW_RULES.md → 이 파일 → README.md (devlog 인덱스 빌드 프로세스)
