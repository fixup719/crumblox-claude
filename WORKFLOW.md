# WORKFLOW.md

---

### [1] 혹시 Block Blaster 같은 게임 알아?
Block Blast 스타일 게임 개요를 설명하고 구현 방향을 안내함.

---

### [2] 테트리스 변형에 가까워 / 블록을 드래그해서 끼우는거지!!! Google Play에 Block Blast 앱 참고하면 돼!
8×8 그리드에 블록을 드래그앤드롭으로 배치하고, 행/열이 완성되면 클리어되는 게임을 단일 HTML 파일로 구현함.

---

### [3] 실행해줘
브라우저에서 index.html을 직접 열어 게임 동작 확인.

---

### [4] 블록이사라질 때 서서히 사라지는게아니라 팡 터지듯이 사라지도록 수정해줘.
라인 클리어 시 flash 애니메이션으로 즉각 폭발하듯 사라지도록 변경.

---

### [5] 블록이 좀더 입체적으로 생기면 좋겠어
`linear-gradient` + `inset box-shadow` 조합으로 블록에 3D 입체감 적용.

---

### [6] 블록이 깨지고 사라질때 부서지듯이 사라지면 어때?
클리어 시 파편(shard) 요소를 생성해 `@keyframes shard`로 사방으로 튀기듯 사라지는 애니메이션 구현.

---

### [7] 처음 메인화면 들어갈때 GUEST모드랑 로그인 2가지 모드가 필요해. GUEST모드는 SCORE저장을 못하고, 로그인해야 SCORE가 기록에 남아. 로그인을 하려면 회원가입을 해야하는데, 회원가입은 아이디, 비번만 입력하면 되고, 유효성점검은 아이디 중복체크 및 4글자이상, 비밀번호도 4글자이상이면 돼.
auth-screen에 GUEST / 로그인 / 회원가입 패널을 추가하고, localStorage 기반 인증 및 점수 저장 구현.

---

### [8] 유저끼리 랭킹 조회도 할 수 있으면 좋겠는데, 기능을 어떻게 추가하는게 좋을까?
localStorage는 기기 간 공유 불가이므로 외부 DB 연동이 필요함을 안내.

---

### [9] 아녀 supabase로 하자!!!
Supabase 연동 계획 확정. `leaderboard` 테이블과 `fetchRanking` / `uploadScore` 함수 구현 및 랭킹 오버레이 UI 추가.

---

### [10] 사용방법을 SUPABASE.md파일로 작성해줘.
Supabase 프로젝트 생성, API 키 설정, 테이블 SQL, RLS 정책, 이메일 인증 비활성화 방법을 담은 SUPABASE.md 작성.

---

### [11] 이제 git에 commit & push 부탁해
변경사항 commit 후 remote가 앞서 있어 pull --no-rebase 후 push 완료.

---

### [12] 게임이름 뭐로하는게 좋을까?
게임 특성(블록이 부서짐)에 맞는 이름 후보 제안.

---

### [13] 흠 이미 있는 게임명이야. 존재하지 않는 걸로 해야해.
기존 게임명과 겹치지 않는 오리지널 이름으로 CRUMBLOX 제안.

---

### [14] 죠아!!!! 그렇게 하고 화면에도 동일하게 반영해줘.
index.html 헤더/타이틀/overlay 등 전체 UI에 CRUMBLOX 브랜드 적용.

---

### [15] 변경사항 git에 올려주고, 그전에 미리 랭킹 조회 가능한 화면도 추가부탁해!
랭킹 오버레이 UI 완성(메인 화면에서도 접근 가능) 후 commit & push.

---

### [16] 이제 supabase에 접속할 수 있도록 소스를 수정해줘. Public URL은 https://nlkwfsbhriagikyuqejf.supabase.co 이고 anon public key는 ... 야.
Supabase URL / anon key를 index.html에 적용하고, Supabase JS SDK CDN 스크립트 추가.

---

### [17] 회원가입을 했는데 왜 SUPABASE USER목록에는 조회되지 않을까?
localStorage 인증은 Supabase Auth API를 호출하지 않음을 설명. Supabase Auth 마이그레이션 필요 안내.

---

### [18] A로 해서 다시 SUPABASE.md 파일 작성후 git push까지해줘
`signUp` / `signInWithPassword` 기반 Supabase Auth로 인증 전면 마이그레이션. `profiles` 테이블로 username 관리. SUPABASE.md 재작성 후 push.

---

### [19] 그러면 게임에서 회원가입할때 @뒤는 crumble.game형식으로 고정하는 걸로 하자. 그러면 이메일로 가입한 유저들끼리 SCORE비교할수있또록 기능 추가해줘. 그리고 SCORE창은 로그인여부 상관없이 버튼을 누르면 스코어 볼 수 있도록 해주고, SCORE 달성 시간(년월일시)도 함께 표기해줘.
이메일 도메인 `@crumble.game` 고정, `updated_at` 컬럼 조회, 랭킹 달성 시간 표기(`fmtTime`), 비로그인 랭킹 접근 가능하도록 수정 후 push.

---

### [20] 현재 메인에서 랭킹보기를 누르면 아무 반응이 없어
`#ranking-overlay` z-index(800)가 `#auth-screen` z-index(900)보다 낮아 가려지던 문제 수정 → z-index 1100으로 변경.

---

### [21] 회원가입할 떄 뒤에 @crumble.game은 자동으로 셋팅되어있게 보이도록 해주고, 중복확인버튼이 박스에서 살짝벗어나 있는데 그부분 보완해줘
`.input-suffix-wrap` + `.input-domain` 구조로 아이디 입력창 우측에 `@crumble.game` 고정 표시. `field-row` align-items stretch로 버튼 높이 정렬.

---

### [22] 회원가입창이 가로로 좀더 길거나 input창안에 기본 텍스트키 아이디(4자리이상) 이런거 폰트크기 줄이거나 해야할듯
`auth-card` 너비 340px → 400px 확장, suffix-wrap 내 input 폰트 0.8rem으로 축소.

---

### [23] 메인 로그인이랑 회원가입 박스창은 크기가 일정하면 좋겠어
`#panel-login`, `#panel-signup`에 `min-height: 310px` 고정으로 패널 전환 시 카드 크기 일정하게 유지.

---

### [24] 로그인창에서 아이디입력할때 아이디만 입력하면 @crumble.game은 자동으로 입력되게 해줄래? 사용자입장에서 아이디만 입력하면된다고 느껴야해. 뒤에 이메일형식은 이미작성되어있으면 좋겠어
로그인 아이디 입력창에도 `input-suffix-wrap` 구조 적용, `@crumble.game` 고정 표시.

---

### [25] 프로필 생성하면 Could not find the table 'public.profiles' int the schema cache라고 뜬다. 그러고나서 계정은 생성되어있어;
`profiles` 테이블 미생성 시에도 가입 완료되도록 개선(profileErr 발생 시 경고 로그만 출력). 중복확인도 테이블 없으면 건너뜀.

---

### [26] 중복확인하면 확인완료(테이블 미설정)이 뜨는이유가 뭐야ㅕ?
Supabase SQL Editor에서 `profiles` 테이블 생성 SQL 실행이 필요함을 안내.

---

### [27] 비밀번호창에 4자리가아니라 6자리이상으로 수정해줘
placeholder 및 유효성 검사 조건을 4자 → 6자로 변경.

---

### [28] 로그인하고 게임할떄 블록 처음에만 드래그앤드롭이되고 두번째부터 아무반응이없어
`addScore()`에서 삭제된 `getUsers()` / `saveUsers()` 호출로 ReferenceError 발생 → `animating=false` 미실행 → 이후 드래그 전부 차단. 해당 코드 제거로 수정.

---

### [29] 이 어플리케이션을 gitgub pages로 배포할 거야. 내 github 계정은 fixup719이고 만들 repo 이름은 crumblox-claude 야. 이 정보에 기초해서 이 어플리케이션을 배포하는 방법을 GITHUB_PAGES.md로 저장해줘. repo를 clone할 위치는 ~/work야. 이 문서의 내용대로 따라하면 바로 배포할 수 있도록 정리해줘.
GITHUB_PAGES.md 작성 (repo 생성 → clone → 파일 복사 → push → Pages 활성화 전 과정 포함).

---

### [30] git에도 올려줄래?
GitHub API로 `fixup719/crumblox-claude` repo 생성, index.html push, Pages 활성화까지 자동 완료. 배포 URL: https://fixup719.github.io/crumblox-claude/

---

### [31] 배포까지했는데, 왜 게임을 해도 랭킹 기록에 안남지?
`uploadScore`의 `.catch(() => {})` 제거 후 에러 로깅 추가. `addScore`에 디버그 로그 추가 후 GitHub Pages에 배포.

---

### [32] 일단 github에 배포한 crumblox-claude도 소스 동기화 해줄래?
두 레포(class repo, crumblox-claude) 최신 소스 동기화 완료.

---

### [33] 오오오 이제 갱신된다!!!!!!!!!
디버그용 `console.log` 제거 후 두 레포 모두 push.

---

### [34] 금까지 내가 전달한 프롬프트와 그에 상응해서 네가 한 작업을 정리해서 WORKFLOW.md로 저장해줘. 프롬프트는 그대로 써야하고 작업은 요약해서 작성해주면 돼. 이 내용을 CLAUDE.md에 반영해서 앞으로는 작업시 항상 WORKFLOW.md를 갱신하게 해줘. 그리고 최종적으로 git에도 올려줘
WORKFLOW.md 작성, CLAUDE.md에 WORKFLOW 갱신 규칙 추가, 두 레포 push.

---

### [35] 난이도조절이 필요해보여 Level기능 도입하고 구간별로 보드 크기를 5*5부터 조금씩늘려가야할듯?
레벨 시스템 구현 (Lv1: 5×5 → Lv2: 6×6 → Lv3: 7×7 → Lv4: 8×8). 각 레벨별 라인 클리어 목표치 달성 시 "LEVEL UP!" 애니메이션 후 보드 확장. Level 점수 박스와 레벨 프로그레스바 UI 추가.

---

### [36] 한번 클리어할떄마다 바로 레벨업은 하지 않고 ... 단계별로 클리어 횟수를 정하는게 좋으려나?
레벨업 목표 라인 수 조정: Lv1 3→5, Lv2 5→10, Lv3 8→16. 두 레포 동기화 및 배포.
