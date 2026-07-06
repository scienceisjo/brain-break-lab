# CLASS GAME WORLD (brain-break-lab)

교실 전자칠판/태블릿용 **멀티터치 미니게임 모음** 웹 앱. 마인크래프트 팬 테마 픽셀 UI.
한 화면에서 1~6명이 동시에 손가락으로 플레이하는 "쉬는 시간 두뇌 놀이" 컨셉.
제작: 과학이조선생(@science_is_jo) · 배포: GitHub Pages (`scienceisjo.github.io/brain-break-lab`).

## 파일 구조

- **`sonic.html`** — 메인 허브. 15개 게임이 내장된 단일 파일(약 2500줄). 프로젝트의 핵심.
- **`index.html`** — 진입점.
- 개별 `.html` — 허브에서 외부 링크로 여는 독립 게임: `liar.html`(라이어), `5sec.html`(5초 준다),
  그 외 `blockblast / chaseracer / drum / hamburgertycoon / helicopter / hnrphoto / luckytest / rhythm / survival.html`.

## 기술 스택

순수 HTML/CSS/JS **단일 파일**. 의존성은 CDN 구글 폰트뿐 (빌드 도구·프레임워크 없음).
파일을 브라우저로 바로 열어 테스트. 전자칠판 멀티터치 최적화(`touch-action`, `-webkit-tap-highlight` 제거).

## sonic.html 아키텍처 — 게임 추가 시 손봐야 할 6곳

새 게임 하나를 넣으려면 아래를 모두 수정한다:

1. **메뉴 카드** (`<div class="cards">`, ~745줄) — `<div class="card" style="--accent:..." onclick="openGame('게임id')">` 추가.
2. **게임 화면** (`<section class="screen" id="게임id">`, ~827줄부터) — 보통 `topbar` + setup/count/result **오버레이 3종** 구조.
3. **CSS** (`<style>` 내 게임별 섹션, ~125줄부터 `/* ===== 게임N ... ===== */`) — `.게임id-...` 클래스.
4. **게임 로직** (`<script>`, ~1503줄부터) — 관례: `xxxSetup / xxxStart / xxxStop` 함수 세트. 상태는 `let 게임={...}` 객체.
5. **라이프사이클 등록** — `goMenu()`(~1347줄)에 `xxxStop()` 추가, `SETUPMAP`에 setup 함수 등록.
6. **카테고리** — `GAME_CAT`(~2158줄)에 `게임id:'react|brain|arcade|play|party'` 등록.

외부 링크 게임은 카드에 `data-link="..." onclick="openLink(this)"`를 쓰고 `GAME_CAT`에만 등록.

## 공통 인프라 (재사용)

- **DOM/화면**: `$(id)`, `show(id)`(화면 전환), `openGame(g)`(안전서약 팬 후 실행), `SETUPMAP`.
- **사운드**: `beep(freq, dur, type, gain)`.
- **설정 UI**: `chipToggle(overlayId, dataAttr)` — setup 오버레이의 선택 칩(선수/시간/난이도 등).
- **플레이어 상수**: `PCOLORS`(--p1~p6), `PNAMES`(1~6번 선수).
- **기록/랭킹**: `lsGet/lsSet`(localStorage), `tryRecord/addRecord/qualifies`, 명예의전당(HOF).
- **전국 랭킹**: Supabase 연동(`supaTop/supaSubmit/supaBumpPlay`). 현재 `SUPA_KEY`(~1341줄) 비어 있어 **로컬 기록만** 동작.
- **인기순/필터**: `bumpPlay`/`sortCards`(플레이 횟수 정렬), `setCat`(카테고리 탭 필터).
- **배경 연출**: `spaceInit/spaceSet`(3D 우주 배경), 입장 화면 워프.

## 작업 규칙

- 게임 로직은 기존 게임(예: `mole` ~1503줄)을 템플릿 삼아 같은 네이밍·구조로 작성.
- 버전 표기: 메뉴 topbar의 `.tag`(v1.x), `updDate`, 패치노트(`openPatch`) — 큰 변경 시 함께 갱신.
- 커밋/푸시는 사용자가 요청할 때만.
