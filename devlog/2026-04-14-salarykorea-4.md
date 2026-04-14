# salarykorea 2026-04-14 — AI 내성 레이어 결과물 풀패키지 강화 (헤더/푸터/메타/부호 수정)

> 작업 시간: 4차 세션
> 분류: #기능개선 #UX수정 #AI내성

## 📌 오늘 한 일

### 1) `income-tax.html` 환급액 부호 표시 버그 수정
- 환급(refund)인데 결과 금액에 `-` 부호가 붙어 "내가 더 내야 하는 줄" 헷갈리는 UX 버그 수정
- `(isRefund ? '-' : '+') + fmt(Math.abs(finalAmount))` → `fmt(Math.abs(finalAmount))`
- `환급 예상액` / `추가 납부액` 라벨로만 의미 구분 (색상 + 라벨로 충분)
- 4개 다른 계산기(`year-end-tax`, `medical-tax`, `freelancer-tax`)도 감사 → `income-tax.html`만 동일 버그 보유 확인

### 2) PDF/이미지 결과물에 7가지 누락 요소 일괄 보강 (풀패키지)
지난 산출물은 "예쁜 계산 결과 이미지"일 뿐 AI 내성 레이어의 차별점이 거의 다 빠진 상태였음. 이번에 한 번에 보강:

- **캡처 헤더** (신규): 브랜드 + 계산기명 + 연도 + 생성일자
- **Trust Badge** (재구성): 기존 `cloneNode` 의존성 제거 → config 기반 재탕으로 신뢰성 확보
- **입력 조건 메타** (신규): 소득유형/총수입/업종경비율/부양가족/기납부세액/세액감면 6항목 그리드
- **출처 푸터** (신규): 참고자료(소득세법 §55 등 3건) + URL + 면책고지

### 3) `trust-layer.js`에 `injectCaptureChrome()` 헬퍼 신설
- onclone 단계에서 위 4개 요소를 동적으로 주입
- 각 계산기는 `TrustLayer.bind()` config에 `calculatorName / taxYear / basisInfo / sourceURL / metaId / references / disclaimer`만 넘기면 됨
- 다른 계산기로 확장 시 패턴화 쉬움

### 4) `common.css`에 캡처 전용 스타일 추가 (~120줄)
- `.capture-only` 유틸 클래스 (화면에서는 숨김, 캡처에서만 노출)
- `.capture-header-clone`, `.capture-trust-badge`, `.capture-meta`, `.capture-footer-clone` 4종

## 🤔 결정 사항

**옵션 2 채택 (숨은 메타 div 방식)**
- `<div id="captureMeta" class="capture-only">`를 미리 DOM에 두고 `calculate()` 시점에 `innerHTML` 주입
- 다른 계산기 확장 시 패턴화 쉬움 (각 페이지 calculate에서 입력값 → HTML 생성)

**Trust Badge는 cloneNode → config 재구성으로 변경**
- 이전 PDF에서 Trust Badge가 캡처되지 않은 원인을 100% 확인 못함 (off-document clone에서 inline-flex 처리 이슈 추정)
- 더 안정적인 방식으로 전환: config(`basisInfo`)에서 텍스트만 받아 onclone 시점에 새 요소로 빌드
- 부수 효과: 검증 링크(`ⓘ`)는 PDF에서 의미 없으므로 자연스럽게 제거됨 → 더 깔끔

## 🐛 문제와 해결 과정

**문제: 환급액이 마이너스로 표시되어 의미 반전**
- 위치: `income-tax.html` line 894, 939, 901
- 원인: 코드 작성 시 "환급=마이너스 부호로 표시"하는 회계 관습을 그대로 UI에 가져옴
- 해결: 라벨이 이미 "환급 예상" / "추가 납부 예상"으로 명확히 구분되므로, 금액은 항상 절대값(양수)으로 표시

**문제: 캡처 결과물에 출처 정보 0%**
- 원인: 캡처 영역(`captureTarget`)이 결과 카드만 감싸고 있고, 페이지 헤더(브랜딩, Trust Badge)는 캡처 범위 밖이었음
- 해결: onclone 시점에 캡처 전용 헤더/푸터를 동적 주입 (실제 페이지 DOM은 변경하지 않음)

## 🔍 검증 결과 (배포 시)

- 로컬↔원격 일치 여부: (배포 후 확인)
- 실제 작동 확인: (배포 후 확인 — 환급 케이스/추가납부 케이스 둘 다 PDF·이미지로 확인 필요)

## 📅 다음에 할 일

1. 사용자 테스트 → 결과물 OK 컨펌받기
2. 컨펌 후 다른 계산기 12개에 동일 패턴 확장 (`acquisition-tax`, `gift-tax`, `freelancer-tax`, `medical-tax`, `year-end-tax`, `monthly-salary`, `retirement`, `unemployment`, `silbi`, `holiday-pay`, `insurance`, `minimum-wage`)
3. 각 계산기마다 `references / sourceURL / disclaimer` 카피라이팅 작성

## 💭 느낀 점

처음에는 "버튼 색깔" 수준에서 시작했는데 결국 AI 내성 레이어의 핵심 차별점(출처/검증/입력조건/맥락)을 PDF/이미지에 다 박아넣는 작업으로 확장됨. 이제 이 PDF를 카톡으로 받아도 "어느 사이트에서 만든 무엇을, 어떤 조건으로 계산한 결과물인지" 한눈에 알 수 있음 — AI에게 물어본 결과물에는 결코 없는 것.

`injectCaptureChrome` 헬퍼를 패턴으로 만들어둔 게 핵심. 다음 12개 계산기는 config만 추가하면 동일한 격조의 결과물이 나옴.
