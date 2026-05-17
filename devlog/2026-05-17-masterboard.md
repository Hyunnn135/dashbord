# Masterboard 2026-05-17 — Phase 4 완료 (구 보드 제거 + 레거시 필드 정리 + 문서 갱신)

> 작업 시간: 저녁 세션 (정리 작업)
> 분류: #리팩토링 #스키마정리 #문서화 #Phase4완료
> 프로젝트: masterboard

## 📌 오늘 한 일

**Phase 4 — 마스터보드 자체 리팩토링 마무리.** Phase 3(2026-04-18, `df83b7b`)에서 `project.html` 단일 템플릿이 4개 프로젝트를 모두 커버하게 됐지만, 옛 보드 HTML 들과 그 보드 시절의 데이터 스키마가 동거 상태였음. 오늘 그 부채를 청산:

**A. 옛 보드 파일 6개 삭제** — 모두 `project.html?slug=...` 로 대체됨:
- `project-masterboard.html` (18KB, 328줄)
- `maxout-board.html` (18KB, 340줄)
- `nudge-board.html` (27KB, 468줄)
- `index.old.html` (31KB, 634줄 — 옛 마스터보드 백업)
- `project-data.backup.js` + `project-data.backup.json` (옛 스키마 백업)
- **총 약 130KB 삭제**

`index.html` / `project.html` 어디서도 이 파일들을 참조하지 않는 것을 사전 grep 으로 확인 후 삭제 진행. 사전 진단에서 `blog-upload-board.html` + `devlog.html` 의 nav 링크 6개가 이들을 가리키고 있던 게 발견 → A 옵션 (project.html 로 마이그레이트) 으로 통일:

```diff
- <a href="project-masterboard.html" class="nav-link">프로젝트</a>
- <a href="maxout-board.html" class="nav-link">맥스아웃</a>
- <a href="nudge-board.html" class="nav-link">넛지</a>
+ <a href="project.html?slug=salarykorea" class="nav-link">월급연구소</a>
+ <a href="project.html?slug=maxout" class="nav-link">맥스아웃</a>
+ <a href="project.html?slug=nudge" class="nav-link">넛지</a>
```

"프로젝트" 라는 모호한 라벨 → 슬러그에 맞춰 "월급연구소" 로 명확화.

**B. 레거시 필드 4종 제거** — `text` / `done` / `tag` / `tagColor`:
- 84개 태스크 전부에 박혀있던 4개 필드를 Python 스크립트로 일괄 제거
- 데이터 크기: 38,259 → 28,462 bytes (**25% 감축**)
- 파일 크기: project-data.js 49KB → 37KB (메타데이터 포함)
- 새 스키마 필드 (`id` / `project` / `title` / `status` / `priority` / `phase` / `tags` / `createdAt` / `completedAt` / `notes`) 무결성 확인 — 84/84 보유
- project-data.js / project-data.json byte-exact 일치 재확인

**C. Dead CSS 정리** — `project.html` L111 `.task.done { opacity:0.55; }` 는 어떤 코드도 `task` 요소에 `done` 클래스를 부여하지 않아 실효 없는 죽은 룰이었음. 제거.

**D. 문서 갱신:**
- `PROJECT_CONTEXT.md` 완전 재작성 (2026-04-14 → 2026-05-17 기준):
  - 옛 보드 3개를 가리키던 "주요 파일" 표 → 단일 `project.html` + 신규 파일 (README, update-devlog-index.py) 반영
  - 데이터 스키마 v2 섹션 추가, 제거된 레거시 필드 명시
  - devlog 인덱스 빌드 프로세스 안내 + README 로 자세히 링크
  - 4개 프로젝트 themeColor 표 정리
- `README.md` 신규 작성:
  - 빠른 시작 (`open index.html`)
  - 파일 구조 표
  - devlog 인덱스 빌드 프로세스 (파일명 규칙 / 헤더 / 재생성 / 자동 반영 / idempotent)
  - 데이터 추가 워크플로 (새 태스크 / 상태 변경 / 새 프로젝트)

## 🤔 결정 사항

- **`blog-upload-board.html` 은 존치.** Phase 3 결정 그대로. salarykorea 블로그 업로드 워크플로 전용 페이지로 65개 포스트 데이터/로직이 들어있어 흡수 비용이 큼. masterboard 의 다른 페이지들과 nav 만 동일하게 맞춰서 일관성 확보.
- **레거시 필드 제거에서 `text` 도 같이 정리한 결정.** `text` 가 `title` 과 100% 같은 내용을 중복으로 들고 있는 상태. 옛 보드 3개가 어딘가에서 `task.text` 를 읽었을 수 있어서 Phase 1~3 에서는 유지했는데, 이제 그 보드들이 삭제됐으니 안전. 사전 grep 으로 `.text\b` 사용처 = `r.text()` (fetch API 메서드) 하나뿐, 무관 확인 후 제거.
- **devlog.html / blog-upload-board.html 의 nav 라벨도 "월급연구소" 로 변경.** 기존 "프로젝트" 라벨이 어떤 프로젝트를 가리키는지 불분명 (실제로는 salarykorea 였음). slug 기반 명확화.
- **README.md 신규 생성 (vs 안 만들기).** GitHub 저장소에 들어가는 첫 인상 + devlog 빌드 프로세스가 PROJECT_CONTEXT.md 에 묻혀있으면 새로 합류하거나 한참 만에 돌아왔을 때 찾기 어려움. README 는 "빠른 진입점" 역할만 하고 자세한 건 PROJECT_CONTEXT 로 위임.

## 🐛 문제와 해결 과정

- **삭제 권한 문제.** Cowork 모드에서 처음 `rm` 명령이 "Operation not permitted" 로 실패. `allow_cowork_file_delete` 도구로 폴더 단위 권한 요청 후 정상 진행.
- **첫 검증에서 잔존 참조 발견.** 청크 ① 후 검증 단계에서 `devlog.html` 의 nav 링크 3개가 여전히 옛 보드를 가리키고 있는 게 잡힘 (사전 진단에서 `blog-upload-board.html` 만 보고 `devlog.html` 은 놓쳤었음). 같은 패턴이라 한 번에 동일하게 수정. 이걸 사전 단계에서 잡지 못한 것은 "삭제 후보 3개를 참조하는 파일" 을 grep 할 때 결과를 7건 이상 안 본 것이 원인. **교훈: 사전 진단 grep 결과를 head 로 자르지 말고 전수 확인할 것.**

## 🔍 검증 결과 (배포 시)

- 깨진 링크 검증: ✅ project-masterboard / maxout-board / nudge-board 모두 참조 0건 (devlog/*.md 안의 과거 기록 제외)
- 레거시 필드 검증: ✅ 0개 잔존, 84개 태스크 전부 새 스키마 필수 필드 보유
- JS↔JSON 정합성: ✅ byte-exact 동일
- devlog 인덱스 idempotent: ✅ 재실행 시 MD5 동일 (`5946202afc36...`)
- 로컬↔원격 일치: _(push 후 확인 예정)_
- 실제 작동: _(현태가 브라우저에서 확인 — `index.html` 정상 렌더 / `project.html?slug=salarykorea` 등 4개 슬러그 / `blog-upload-board.html` + `devlog.html` 의 nav 클릭 시 새 project.html 로 이동 / 404 케이스)_

## 📅 다음에 할 일

masterboard 자체 작업은 **완전히 종결**. 이후로는 다른 프로젝트(salarykorea/maxout/nudge)의 진행이 발생할 때 `project-data.js/json` 한 줄 추가 + devlog 추가 + `update-devlog-index.py` 한 번 돌리는 패턴만 유지.

- **🔴 다음 세션 우선순위 (다른 프로젝트):**
  - Nudge 실기기 로그 캡처 — Watch OS 업데이트 완료 후 STEP 3 부터 재개
  - Salarykorea — 3개 🔴 NOW 태스크 (GA4 설치 / Search Console 실측 / i18n 33개 noindex)

## 💭 느낀 점

- **부채 청산은 새 기능 추가보다 보상이 빠르다.** Phase 4 작업량은 1시간 남짓인데 결과는 (a) 폴더 130KB 다이어트, (b) 데이터 25% 감축, (c) 4월 14일 기준 stale 한 PROJECT_CONTEXT.md 갱신, (d) 새로 합류하는 사람(or 미래의 나) 을 위한 README 까지. Phase 1~3 에서 "옛 보드를 살려둔다" 결정이 매번 새 변경마다 "이중 유지 비용" 을 소소하게 부과하고 있던 셈. 진작 끝낼 걸.
- **dead code 는 검색에서 발견될 때까지만 dead 가 아니다.** `.task.done` CSS 룰을 발견했을 때 "이게 어디서 부여되는지 1분만 찾으면 되네" 의 가벼움이 정상이지만, 그 1분이 누적되면 디버깅 비용이 됨. 한 번 정리해두는 게 다음 누군가의 1분을 줄임.
- **"보류 결정 vs 제거 결정" 의 비대칭성.** `blog-upload-board.html` 은 Phase 3 에서 보류, Phase 4 에서도 보류. 진작 제거하든가 진작 흡수하든가 결정해야 하는데, 단지 "65개 포스트 데이터 이관 비용이 크다" 는 이유로 매번 미뤄짐. 다음 salarykorea 작업 들어갈 때 이 결정을 다시 들여다보기.
