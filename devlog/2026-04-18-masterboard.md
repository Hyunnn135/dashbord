# [masterboard] 2026-04-18 — 스키마 v2 리팩토링 + 홈 리디자인 (Phase 1~2/4)

> 작업 시간: 오후 세션
> 분류: #리팩토링 #스키마 #UI리디자인

## 📌 오늘 한 일

**마스터보드 전체 리디자인 계획 수립 및 착수.** 보드 6개로 분산되어 있고 완료 태스크 ~40개가 리스트에 섞여 있는 "난잡함"을 해소하기 위해, 4-Phase 마이그레이션 계획을 세우고 Phase 1~2를 이번 세션에 완료.

**리디자인 계획서 작성.** 다른 1인 개발자 대시보드 사례(Derek Sivers의 `/now` 페이지, Notion 개인 대시보드의 Now/Next/Later 상태 모델, GitHub Projects for Solo Devs의 "하나의 단일 진실 소스 + 여러 뷰" 철학)를 참고하여, masterboard 를 **작업 관리 도구**로 축 이동. 포트폴리오 역할은 devlog 페이지에 흡수. 계획서는 `projescts/masterboard-redesign-plan.md`.

**Phase 1: project-data.json 스키마 v2 로 개편.**
- `tasks` 객체에 `project` / `status(now|next|later|done|archived)` / `priority(high|mid|low)` / `phase` / `tags` / `createdAt` / `completedAt` / `notes` 필드 도입
- `done` 불리언 폐기 → `status` 열거형으로 통합
- 기존 필드(`text`, `done`, `tag`, `tagColor`)는 Phase 4까지 **레거시로 유지** → 구 HTML 렌더 안 깨짐
- `projects` 배열 신규 — 4개 프로젝트(salarykorea, maxout, nudge, realestate) 메타 (`slug`, `name`, `icon`, `themeColor`, `oneLiner`, `phases`, `currentPhase`, `linkedDocs`)
- 82개 task 전수 수동 라벨링 — `migrate_schema.py` 스크립트로 결정론적 변환
- 최종 분포: now 4 · next 5 · later 32 · done 41
- 백업: `project-data.backup.json`, `project-data.backup.js`

**Phase 2: index.html 전면 재작성.**
- 기존 634줄 → 330줄 (−48%)
- 구조: Top nav → **Today** (status=="now" 태스크, 우선순위 정렬) → **Projects** (4개 카드, 진행률 + now/next 카운트) → **Recent devlog** (최근 3개)
- 제거: 긴급 액션 배너(Today 섹션에 흡수), 드래그 정렬(data-driven 전환으로 불필요), 하드코딩된 타임라인(프로젝트 카드의 currentPhase로 대체)
- CSS 변수(`--card-color`, `--card-bg`) + JS `hexToRgba()` 로 프로젝트 themeColor 를 런타임에 적용 → 데이터만 바꾸면 색상 자동 반영
- 기존 index는 `index.old.html` 로 백업

## 🤔 결정 사항

- **레거시 필드 유지 전략** — Phase 1에서 기존 HTML을 한꺼번에 깨지 않고, 구 보드(`project-masterboard.html` 등 4개)가 Phase 4까지 살아있도록 `text/done/tag/tagColor` 를 중복 저장. 데이터 크기 15~20% 증가 감수.
- **devlog 인덱싱은 Phase 3+ 로 이월** — 현재 `index.html`의 "Recent devlog" 는 최근 3개 하드코딩. masterboard/devlog/ 폴더를 자동 스캔하는 `devlog-index.json` 생성 로직은 별도 작업으로 분리. devlog 추가 시마다 index.html 한 줄 수정 필요 (당분간 감수).
- **realestate 프로젝트 `status: "paused"`** — 도메인 미구매 상태를 반영. 프로젝트 카드가 62% opacity 로 표시되어 시각적으로도 구분됨.
- **Now/Next 분류 기준 확정** — now 는 "세션 열자마자 착수할 것"(salarykorea 긴급액션 3개 + nudge 동기화 버그 1개), next 는 "now 직후 이어갈 것"(AdFit 등록, 색인 확인, 계산기 롤아웃, maxout Xcode 셋업, nudge 실기기 검증). 나머지는 모두 later 로 일괄 묶어 첫 화면에서 숨김.

## 🐛 문제와 해결 과정

- `index.html` JSON 파싱 검증 중 bash heredoc 의 `!` 이스케이프 문제 발생. Python 으로 대체 파싱 검증 → 82 tasks, 4 projects 정상 로드 확인.
- 계획 수립 시 검토: realestate 의 task 4개가 `t23~t26` 으로 salarykorea 프로젝트에 섞여 있었음. 변환 시 `project: "realestate"` 로 재배정. 기존 HTML은 tag 필드 기반이라 영향 없음.

## 🔍 검증 결과 (배포 시)

- 로컬↔원격 일치 여부: _(push 후 확인)_
- 실제 작동 확인: _(index.html 열어 Today 4개, Projects 4개 카드, devlog 3개 렌더링 확인 필요)_

## 📅 다음에 할 일

- **Phase 3: 프로젝트 상세 템플릿** — `project.html` 단일 파일 + `?slug=xxx` 쿼리 분기. Now/Next/Later/Done/Archived 아코디언. salarykorea 전용 "블로그 업로드" 위젯(65개 포스트 진척) 흡수.
- **Phase 4: 구 보드 제거** — `project-masterboard.html`, `maxout-board.html`, `nudge-board.html`, `blog-upload-board.html` 삭제 + 레거시 필드(`text/done/tag/tagColor`) 제거 + `PROJECT_CONTEXT.md` 갱신.

## 💭 느낀 점

- 보드 개수를 늘리는 방향이 아니라 **뷰를 늘리는 방향**으로 설계를 되돌린 게 핵심 전환점. 프로젝트가 추가될 때마다 보드 파일을 새로 만들던 습관이 "난잡함"의 진짜 원인이었음.
- Now/Next/Later 모델은 단순해 보이지만, 실제 라벨링을 해보니 "이건 진짜 now 인가, 아니면 later 인가?"를 강제로 판단하게 됨 → **결정 압박 자체가 정리 효과**를 냄. 82개 중 now 는 4개뿐.
- 레거시 필드를 유지하는 이중 스키마 전략 덕에 "Phase 1 끝 → HTML 안 깨짐" 이 보장되어 push 를 Phase 별로 끊을 수 있게 됨. 큰 변경을 작은 step 으로 쪼개는 데 유용.
