# [마스터보드] 2026-04-14 (2차 세션) — devlog 통합 위치 정리(masterboard/devlog/) + WORKFLOW_RULES 5번 항목 갱신

> 작업 시간: 오후
> 분류: #프로세스 #문서화 #리팩토링 #배포
> 프로젝트: masterboard

## 📌 오늘 한 일

- devlog 산재 문제 해결 — 모든 프로젝트의 작업일지를 `~/Desktop/Projects/masterboard/devlog/`에 통합 보관하기로 결정·실행
- 파일명 규칙 통일: `YYYY-MM-DD-{프로젝트}.md` (예: `2026-04-14-maxout.md`, `2026-04-14-salarykorea.md`)
- 같은 날 같은 프로젝트 두 번째 세션은 `-2`, `-3` 접미사 (예: 이 파일이 `2026-04-14-masterboard-2.md`)
- 기존 파일 이동/리네이밍:
  - `maxout/devlog/2026-04-13.md` → `masterboard/devlog/2026-04-13-maxout.md`
  - `masterboard/devlog/2026-04-14.md` → `masterboard/devlog/2026-04-14-masterboard.md`
- `masterboard/devlog/_TEMPLATE.md` 단일 통합 템플릿으로 갱신 (프로젝트 필드 + 파일명 규칙 명시)
- 구 위치(`maxout/devlog/_TEMPLATE.md`, `salarykorea/devlog/_TEMPLATE.md`)는 deprecated stub으로 변환하여 새 위치로 안내
- `WORKFLOW_RULES.md` 5번 항목 갱신 — 통합 위치, 파일명 규칙, 표준 템플릿, 디렉터리 구조 명시
- `maxout/PROJECT_CONTEXT.md` 3번 섹션의 devlog 경로를 새 통합 위치로 갱신
- Claude 메모리 `project_maxout.md`의 4곳 동시 업데이트 항목 중 devlog 경로를 새 위치로 갱신

## 🤔 결정 사항

- **단일 위치(`masterboard/devlog/`)에 통합** — masterboard는 이미 포트폴리오 역할이고 git에 포함되어 GitHub Pages로 노출되므로, 모든 프로젝트의 작업 일지가 한 곳에 모이는 게 의미적·운영적으로 자연스럽다. 추후 `index.html` 타임라인 자동 렌더링도 단일 디렉터리만 스캔하면 됨.
- **파일명에 프로젝트 접미사 의무화** — 디렉터리가 통합되었으므로 파일명만 보고도 어떤 프로젝트인지 즉시 식별 가능해야 함. `YYYY-MM-DD-{project}.md` 형식 채택.
- **구 폴더는 삭제하지 않고 stub만 남김** — 샌드박스 권한상 `rm`이 불가능했고, 향후 누군가(혹은 미래의 Claude)가 구 위치를 참조했을 때 새 위치로 안내받을 수 있도록 deprecated 안내문 유지.
- **WORKFLOW_RULES.md는 로컬에만 보관** — 별도 git 저장소를 만들지 않음. 다중 프로젝트 공통 규칙이라 어느 한 프로젝트 git에 종속시키지 않는 게 깔끔.

## 🐛 문제와 해결 과정

- **샌드박스에서 구 `_TEMPLATE.md` 파일 `rm` 불가** — `Operation not permitted` 오류. 삭제 대신 Write 도구로 같은 경로에 deprecated 안내 stub을 덮어쓰는 방식으로 우회. 결과적으로 "지운 흔적"이 아니라 "안내가 남는" 형태가 되어 오히려 더 친절한 마이그레이션이 됨.
- **마운트 잔존(`dashboard/` 빈 폴더)** — 1차 리네이밍 작업의 잔재로 샌드박스에 빈 마운트가 남아 있었으나, 사용자 macOS Desktop을 Glob으로 직접 확인한 결과 실제 폴더 트리는 `masterboard/`, `maxout/`, `salarykorea/`만 존재. 사용자에게 영향 없음 확인.

## 🔍 검증 결과

**1차 검증 (로컬):**
- `masterboard/devlog/` 안에 `_TEMPLATE.md`, `2026-04-13-maxout.md`, `2026-04-14-masterboard.md` 정상 존재
- `maxout/devlog/_TEMPLATE.md`, `salarykorea/devlog/_TEMPLATE.md`는 deprecated 안내 stub으로 변환 확인
- `WORKFLOW_RULES.md` 5번 항목, `maxout/PROJECT_CONTEXT.md` 3번 섹션 모두 새 경로 반영 확인

**2차 검증 (현태가 직접 push & 배포 후):**
- ✅ Git push 정상 완료 (masterboard / salarykorea 양쪽)
- ✅ 로컬 ↔ GitHub 파일 일치 확인
- ✅ 새 위치(`masterboard/devlog/`)에서 devlog 파일들 정상 노출

## 📅 다음에 할 일

- (선택) `masterboard/index.html`에 통합 devlog를 자동으로 읽어와 최근 N개를 카드/타임라인에 표시하는 미리보기 기능 — 단일 디렉터리가 되었으니 구현이 단순해짐
- (선택) GitHub 저장소 이름을 `Hyunnn135/dashbord` → `Hyunnn135/masterboard`로 변경 + remote URL 갱신
- 다음 maxout 작업 세션 시작 시 새 devlog 위치(`masterboard/devlog/2026-04-XX-maxout.md`)에 작성하는 첫 사례로 검증

## 💭 느낀 점

- "공통 자원은 하나의 장소에"라는 단순한 원칙이 운영 부담을 크게 줄임. 프로젝트가 늘어날수록 이런 통합 결정의 가치가 커짐.
- 삭제가 막혔을 때 "그럼 안내문을 남기자"로 전환한 게 결과적으로 더 나았음. 강제 정리보다 부드러운 마이그레이션이 자기 자신(미래의 Claude)에게도 친절함.
- WORKFLOW_RULES.md를 계속 다듬는 흐름이 자리 잡혀가는 게 좋음. 규칙 자체가 "살아 있는 문서"로 동작.
