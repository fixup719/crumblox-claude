# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 실행 방식

확인 질문 없이 항상 즉시 실행한다. "괜찮을까요?", "진행할까요?" 등의 확인 없이 바로 코드 수정 및 배포를 진행한다.

---

## WORKFLOW 기록

작업이 끝날 때마다 현재 작업 디렉토리의 `WORKFLOW.md`를 갱신한다.

- 사용자 프롬프트는 **원문 그대로** 기록한다 (수정·요약 금지).
- Claude가 수행한 작업은 **한두 문장으로 요약**해서 기록한다.
- 파일이 없으면 새로 생성한다. 있으면 하단에 항목을 추가한다.
- 형식: `### [N] <프롬프트 원문>` → 작업 요약.

---

## 작업 범위

이 디렉토리(`day04/blockgame/`) 이하 파일만 읽고 수정한다. 상위 디렉토리는 접근하지 않는다.

---

## Git 규칙

- 브랜치 병합은 반드시 **merge** 사용
- **rebase 금지**
- 소스 수정 후 두 레포 모두 동기화:
  1. `weable-kosa/kosa-vibecoding-2026-3rd` (class repo)
  2. `fixup719/crumblox-claude` (GitHub Pages 배포 repo)

```bash
# 동기화 방법
cp index.html ~/work/crumblox-claude/index.html
git -C ~/work/crumblox-claude add index.html && git -C ~/work/crumblox-claude commit -m "..." && git -C ~/work/crumblox-claude push origin main
```

---

## 실행 방법

서버 불필요. 브라우저에서 `index.html`을 직접 열거나 간이 서버 사용:

```bash
python3 -m http.server 7293
# → http://localhost:7293
```

배포 URL: `https://fixup719.github.io/crumblox-claude/`

---

## 파일 구성

| 파일 | 역할 |
|------|------|
| `index.html` | 전체 구현 (HTML + CSS + JS 인라인 단일 파일) |
| `SUPABASE.md` | Supabase 랭킹 연동 설정 가이드 |
| `GITHUB_PAGES.md` | GitHub Pages 배포 가이드 |
| `WORKFLOW.md` | 작업 히스토리 |

---

## 아키텍처

### 화면 구조

```
#auth-screen   (초기 표시, 로그인/게스트 선택)
  #panel-main    → GUEST / 로그인 / 랭킹보기 버튼
  #panel-login   → 로그인 폼 (아이디 + @crumble.game 고정 표시)
  #panel-signup  → 회원가입 폼 (아이디 중복확인 + @crumble.game 고정 표시)

#game-screen   (인증 후 표시)
  #user-bar      → 유저명 배지 + RANKING 버튼 + 로그아웃
  #grid-wrap     → 8×8 게임 그리드
  #pieces-area   → 드래그 가능한 블록 슬롯 3개

#overlay       (게임오버 시 표시)
#ghost         (드래그 중 블록 미리보기, position: fixed)
#combo-text    (다중 라인 클리어 시 팝업)
#ranking-overlay  (랭킹 목록, z-index: 1100 — 모든 화면 위)
```

### 게임 로직 흐름

```
pointerdown(slot)
  → drag 상태 저장 + setPointerCapture
  → updateDrag() : ghost 위치 + 그리드 preview 렌더링
pointermove → updateDrag()
pointerup   → dropPiece()
  → placePiece()   : grid 배열 갱신
  → clearLines()   : 완성된 행/열 감지 → spawnShards() + flash 애니메이션 → 배열 초기화
  → addScore()     : 점수 계산 + 로그인 유저면 Supabase leaderboard upsert
  → isGameOver()   : 남은 피스 전부 놓을 자리 없으면 true
```

### 핵심 상태 변수

| 변수 | 타입 | 설명 |
|------|------|------|
| `grid` | `(string\|null)[][]` | 8×8 배열, 빈 칸 null / 채워진 칸 색상 문자열 |
| `pieces` | `({cells,color}\|null)[]` | 슬롯 3개, 사용한 슬롯은 null |
| `currentUser` | `{id,best}\|null` | null이면 게스트 |
| `drag` | 객체\|null | 드래그 중인 피스 정보 및 예상 anchor 좌표 |
| `animating` | `boolean` | 클리어 애니메이션 중 입력 차단 플래그 |
| `cellEls` | `HTMLElement[]` | 64개 셀 DOM 참조 (flat, row-major) |

### CSS 핵심 패턴

- 셀 색상: `--bc` CSS 커스텀 프로퍼티 → `.cell.filled`의 `background`에서 참조
- 셀 크기: `--cs` CSS 커스텀 프로퍼티 → JS에서 `getCellSize()` 계산 후 `#grid`에 주입
- 3D 효과: `linear-gradient` 오버레이 + `inset box-shadow` 조합
- 파편 애니메이션: `@keyframes shard` + `--dx`, `--dy`, `--rot` 커스텀 프로퍼티
- 아이디 입력 suffix: `.input-suffix-wrap` + `.input-domain` 구조

### 인증 / 데이터 저장

- **Supabase Auth** (`signUp` / `signInWithPassword`) 사용
- 이메일 형식: `{username}@crumble.game` (사용자에게는 username만 노출)
- `profiles` 테이블: username 중복 확인용 (없으면 중복확인 건너뜀)
- `leaderboard` 테이블: best_score + updated_at upsert (username 기준)
- 게스트: Supabase 저장 없음, 세션 메모리에만 유지
- 새로고침 시 `getSession()`으로 세션 자동 복원

### 코딩 컨벤션

- ES6+, 클래스 미사용, 함수형
- `animating` 플래그로 async 클리어 애니메이션 중 중복 입력 차단
- 드래그는 Pointer Events API (`setPointerCapture`)로 마우스·터치 통합 처리
