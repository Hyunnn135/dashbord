# salarykorea 2026-04-14 — AI 내성 재포지셔닝 전략 수립

> 작업 시간: 오후~저녁 세션
> 분류: #기획 #전략 #피벗
> 프로젝트: salarykorea

## 📌 오늘 한 일

- 오전에 완성한 **프리미엄 PDF 기획안(v1)** 에 대한 현태님의 본질적 질문에서 출발:
  > "AI에 다 물어보는 시대에 사람들이 귀찮게 계산기 두드리거나 긴 PDF 글을 볼까? 차별점은?"
- 이 질문을 정면으로 받아 **AI 내성 재포지셔닝 전략 문서** 작성:
  - `salarykorea/docs/ai-resistance/README.md` — 마스터 전략 문서 (9섹션)
  - `salarykorea/docs/ai-resistance/components.md` — 공통 컴포넌트 설계서 (Trust Badge · Result Actions · Sources Footer + trust-layer.js 스펙)
- `PROJECT-CONTEXT.md` 남은 작업 리스트에 신규 항목 5개 추가 (#11~#15).
- `masterboard/project-data.json` 에 t27~t31 추가 — AI 내성 전략, 공통 컴포넌트 구현, 파일럿, 롤아웃, PDF v2 리라이트.

## 🤔 결정 사항

- **"정보 전달" 게임은 포기**. AI가 구조적으로 더 빠르고 싸다. salarykorea가 여기서 싸우면 진다.
- 대신 **AI가 구조적으로 못 하는 5가지**에 집중:
  1. 최신성 보증 (2026 개정 세법 배지)
  2. 할루시네이션 없는 검증 (돈이 움직이는 영역에서 신뢰)
  3. 산출물 (PDF/이미지/공유 링크/인쇄)
  4. 의사결정 템플릿 (워크시트·체크리스트·협상 스크립트)
  5. 큐레이션 ("이 5개만" 압축)
- 이를 **3축 재포지셔닝**으로 구현: **신뢰 레이어 + 산출물 + 도구**
- 실행 순서 결정: **공통 컴포넌트 설계 → 파일럿 1개 페이지 → 컨펌 후 전 페이지 롤아웃 → PDF v2 리라이트**
- 오전 PDF 기획안은 **폐기가 아니라 v2로 리라이트** — "긴 글 40p" 구조를 "가이드 20p + 워크시트 20p"로 변경 예정
- 파일럿 후보: income-tax / insurance / retirement — 현태님 컨펌 대기
- **공격적 옵션(ChatGPT 프롬프트 삽입)**은 파일럿 결과 본 후 판단으로 보류

## 🐛 문제와 해결 과정

- 본질적 질문을 받고 **방어하지 않은 것**이 이번 세션의 핵심. 기획자 입장에서 자기 기획을 옹호하는 본능을 누르고 "지금 기획은 AI 시대에 불리하다"를 인정하는 것부터 시작. 그래서 피벗 방향이 뾰족하게 나옴.
- PDF v1 기획을 폐기하는 것이 아니라 "v2 리라이트"로 연결 — 지금까지 한 작업을 살리면서 방향만 튼다.

## 🔍 검증 결과

- 로컬 파일 생성 확인:
  - `salarykorea/docs/ai-resistance/README.md`
  - `salarykorea/docs/ai-resistance/components.md`
  - `salarykorea/PROJECT-CONTEXT.md` (업데이트)
  - `masterboard/project-data.json` (t27~t31 추가)
- 원격 푸시는 이 devlog 직후 진행 예정.

## 📅 다음에 할 일

- **현태님 컨펌 대기**:
  1. AI 내성 3축 방향성 OK?
  2. 파일럿 페이지 선택: income-tax / insurance / retirement 중 하나?
  3. "ChatGPT 프롬프트 섹션 삽입" 같은 공격적 옵션 포함 vs 제외?
- 컨펌 받으면 다음 세션에 실제 구현:
  - `common.css` 에 Trust Badge · Result Actions · Sources Footer 스타일 추가
  - `/trust-layer.js` 작성 (html2canvas + jsPDF lazy-load)
  - 파일럿 페이지 패치 적용 → 로컬 테스트 → 배포 → 실제 작동 검증

## 💭 느낀 점

- 오전 내내 PDF 기획에 시간을 썼는데, 현태님의 한 마디("AI 시대에 이걸 왜?")가 더 큰 가치를 만들었다. **방향을 검증하는 질문은 실행보다 앞선다.**
- "정보 전달"이 상품이 되던 시대가 저물고, 이제 상품은 **"신뢰 + 산출물 + 도구"**의 묶음이다. salarykorea가 이 전환을 빨리 하면 AI 시대에 오히려 더 뾰족한 포지션을 가질 수 있다.
- 특히 "결과 PDF 저장" 같은 산출물은 기술적으로 어렵지 않으면서 AI 채팅과 **결정적 차이를 만드는 기능**. 낮은 비용, 높은 차별화.
- PDF v2의 킬러 아이디어: "읽는 PDF"가 아니라 **"채워넣는 PDF"**. AI가 대화로 대체 못 하는 유일한 영역.
