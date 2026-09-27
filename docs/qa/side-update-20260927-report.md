# Side 포크·설치 앱 업데이트 점검 기록 (2026-09-27)

이 기록은 2026-09-27에 upstream `Chris-Chai-Minjae/side-work-memory`의 신규 두 커밋을 로컬 포크 main과 `/Applications/Side.app`에 반영한 절차와 검증 결과를 정리한다. 상위 기준은 [운영가이드](../../운영가이드.md)와 [설치와복원](../../운영가이드.d/설치와복원.md)이다.

## 1. 소스 반영 결과

**로컬 main에 upstream/main을 일반 merge로 통합했고, merge commit `fd383cd`가 origin(dyoon98-creator fork)에 push되어 원격 SHA와 일치한다.**

- merge-base: `cabbb2b` (이전 설치 기준 SHA와 동일)
- 로컬 고유 커밋 보존: `32a32c7`, `7c95eb4` (운영가이드·설명만화 문서)
- upstream 반영 커밋: `53dc16d` AXObserverHub 중첩 콜백 String 인덱스 수정, `7d4021e` side-resume 번들 스킬+문서
- merge commit: `fd383cd2fdb4dd7833317d17ed203a05f448506c`
- push 후 `git ls-remote origin main` = `fd383cd2…506c` (로컬 HEAD와 동일)
- 나간 커밋은 위 3개뿐이며 무관한 변경 없음
- `docs/index.html`만 양쪽이 건드렸으나 서로 다른 hunk라 ort 전략으로 자동 병합됨 — upstream의 `#resume` 섹션·내비 링크와 로컬의 운영 만화 버튼이 모두 유지됨을 grep으로 확인

## 2. upstream 변경 내용

| 파일 | 변경 |
|---|---|
| `apps/side-mac/Sources/Side/Capture/AXObserverHub.swift` | `pendingText`를 `bufferedText`로 스냅샷해 emit 콜백 중 String 인덱스 무효화를 차단, 완성 문장 emit을 루프 뒤로 이동 |
| `apps/side-mac/Tests/SideCaptureKitTests/ObserverTests.swift` | 재진입 회귀 테스트 추가(`testReentrantBrowserURLReadPreservesCompletedSentences` 등) |
| `apps/side-mac/scripts/build-app.sh` | 번들에 `skills/side-resume/SKILL.md` 복사 단계 추가 |
| `skills/side-resume/SKILL.md` | 신규 — Side 기록 기반 작업 재개 스킬 |
| `scripts/gates/p5-bundle-smoke.ts` | 필수 번들 자산에 side-resume SKILL.md 추가 |
| `docs/agents.md`, `docs/agents.en.md`, `README.md`, `README.en.md`, `docs/index.html` | 스킬 설치·사용 안내 추가 |

## 3. 검증 결과

| 항목 | 결과 | 근거 |
|---|---|---|
| 계보·보존 | PASS | merge-base `cabbb2b`, merge 그래프에 로컬 2커밋+upstream 2커밋 모두 유지 |
| Swift 캡처 회귀 | PASS | `swift test --package-path apps/side-mac --filter SideCaptureKitTests` → 184 tests, 0 failures. 신규 `testReentrantBrowserURLReadPreservesCompletedSentences` 포함 |
| TS 정적검사 | PASS | `bun run check` (tsc --noEmit + biome check 201 files) — 오류 없음 |
| 빌드 | PASS | `bun run build` — 외장 SSD `.build` 심볼릭 경유, 모델은 `SIDE_MODEL_CACHE_SOURCE`로 기존 캐시 재사용(다운로드 없음) |
| 번들 스모크 | PASS | `bun run scripts/gates/p5-bundle-smoke.ts` → `PASS bundled web, SQLite vec0, and compiled MiniLM inference`, `Resources/skills/side-resume/SKILL.md` 존재 |
| 서명 | PASS | `codesign --verify --deep --strict` 통과, `Identifier=com.minjaechai.Side`, `Signature=adhoc` |
| 설치 해시 일치 | PASS | 설치본 `Contents/MacOS/Side` = 빌드본 `fbce01d5…f38f`, `Resources/side` = `6cda511a…44b` (직전 설치본은 `ef894768…`) |
| 앱 실행 | PARTIAL | `/Applications/Side.app` 재시작됨(PID 68426). 데몬은 Keychain 승인 대기로 미기동 — §4 참조 |
| MCP tools/list | PASS | 신규 번들 `side mcp`로 initialize+tools/list 정상, `history_search`·`history_read`·`memory_search` 3종 확인(비공개 기록 호출 없음) |
| 설정·데이터 보존 | PASS | `~/Library/Application Support/Side/` 22M, 교체 전후 파일 목록·크기 동일(ledger.db 22,933,504B, settings.json 806B). Keychain 항목 mdat 변경 없음(회전 없음). `contextAwareness.enabled=true` 유지 |
| 중복 앱 인덱싱 | PASS | Mac-Ops `scripts/audit-spotlight-app-duplicates.sh /Applications/Side.app` → `PASS | com.minjaechai.Side | /Applications/Side.app` |

## 4. 잔여 항목 — Keychain 승인 대기(사람 조치 필요, 이후 해소 — §7)

**새 ad-hoc 서명 바이너리가 기존 Keychain 항목 ACL의 신뢰 cdhash와 달라, 마스터키 읽기에 로그인 키체인 암호 승인이 필요하다.** 이 상태에서는 데몬이 기동되지 않아 `side status`가 `Side is not running. Open Side.app.`을 반환한다. **(18:50 사용자 승인 후 데몬 기동·RPC 응답 확인 — §7)**

- 근거: securityd 로그 `displaying keychain prompt for /Applications/Side.app(68426)`, ACL 신뢰 요구 `cdhash H"40a5873…"`(구 빌드) vs 신규 `CDHash=3feab1bc…`
- 화면 요구: SecurityAgent 프롬프트 「Side이(가) 'local-context-awareness-ledger'에 저장된 비밀 정보를 사용하려고 합니다… '로그인' 키체인 암호를 입력하십시오」 — 항상 허용/거부/허용 버튼과 암호 입력란
- 필요한 조치: 사용자가 로그인 키체인 암호를 입력하고 허용(권장: 항상 허용) → `DaemonSupervisor`가 masterKey를 얻어 데몬을 spawn
- 참고: ad-hoc 재빌드마다 cdhash가 바뀌어 같은 프롬프트가 재발할 수 있음(설치와복원 문서의 「임시 서명 빌드이므로 권한을 다시 물을 수 있다」와 일치)
- 승인 후 확인 명령: `"/Applications/Side.app/Contents/Resources/side" status`

## 5. 백업과 원복

- 이전 빌드 앱: `/Volumes/PAUL_SSD1/ProjectsData/85.side-work-memory/backup-20260927.noindex/build-app/Side.app`
- 이전 설치 앱: `/Volumes/PAUL_SSD1/ProjectsData/85.side-work-memory/backup-20260927.noindex/installed-app/Side.app`
- 원복: 앱 종료 → 신규 `/Applications/Side.app`를 별도 보관 → 백업본을 `ditto`로 `/Applications/Side.app`에 복원. 이전 바이너리는 Keychain ACL 신뢰 cdhash와 일치해 승인 없이 기존 상태로 복귀 가능
- 데이터 루트는 이번 업데이트에서 쓰지 않았으므로 별도 데이터 원복 불필요

## 6. 남은 미검증

- 데몬은 §7 시점에 기동·RPC 응답을 확인했으나, 캡처 `running` 전환과 실제 수집·검색·health 흐름은 TCC 재승인 뒤 확인이 필요하다(`doctor`는 provider 연결 검사 가능성이 있어 별도 확인 필요)
- 설정 창의 실제 화면은 스크린샷으로 확인했다 — 「Side에 연결 중…」 스피너, 「다시 시도」 버튼, 빨간 안내문 「Side가 시작 중입니다. 다시 시도하세요.」(§7)

## 7. 후속 진단 — Keychain 승인 이후 권한 재확인(같은 날 추가 관측)

**사용자가 Keychain 승인을 마친 뒤 데몬은 실제로 기동돼 RPC에 응답하고, 손쉬운 사용 권한도 복구됐지만, 캡처 파이프라인은 입력 모니터링·화면 기록 두 권한의 재승인 대기로 `stopped`를 유지한다.**

- 데몬 기동 확인: 앱(PID 68426)이 18:50:49에 `Resources/side daemon`(PID 66052)을 spawn했고, `run/daemon.sock`이 정상 응답한다. §4의 Keychain 잔여는 해소됐다.
- `status --json` 관측 경과: 18:53·18:59에는 `accessibilityTrusted=false`, `inputMonitoringTrusted=false`, `screenRecordingTrusted=false`. 사용자가 권한을 다시 허용한 뒤 19:04에는 `accessibilityTrusted=true`로 바뀌었고(실행 중 반영), `inputMonitoringTrusted=false`·`screenRecordingTrusted=false`만 남았다. `state:"stopped"`, `enabled:true`, `banner:"permissions_needed"`, `permissionSheetVisible=true`, `stop_reason:null`, daemon health `state:"paused"`.
- tccd 로그(읽기 전용, 18:51~18:52): `com.minjaechai.Side`가 `kTCCServiceAccessibility`·`kTCCServiceScreenCapture`에서 `Failed to match existing code requirement`를 기록했다 — 기존 허용 항목이 남아 있으나 저장된 코드 요구사항(cdhash)이 새 바이너리와 불일치했다. 같은 시각 `TCCDEvent type=Modify … kTCCServiceAccessibility … com.minjaechai.Side` 이벤트가 관측됐고, 이후 실제로 `accessibilityTrusted=true`로 전환됐다.
- 원인 판정: ad-hoc 재빌드마다 cdhash가 바뀌어 기존 TCC 허용이 새 바이너리에 적용되지 않는, §4 Keychain ACL과 같은 기전이다. [설치와복원](../../운영가이드.d/설치와복원.md)의 「임시 서명 빌드이므로 업데이트 후 권한을 다시 물을 수 있다」와 일치한다.
- `observerPid`는 별도 observer 프로세스나 TCC 책임 주체가 아니다 — `apps/side-mac/Sources/Side/App/SideRuntime.swift:90`에서 `health.observerPid = foreground()?.pid`로, 관측 중인 전면 앱의 pid다(18:59엔 Orca=50102, 19:04엔 다른 전면 앱=52776으로 바뀜). TCC 주체는 `responsibleSelf=true`인 앱 자신(`com.minjaechai.Side`)이다.
- 시도한 복구와 한계: 설정 창의 「다시 시도」를 Orca computer-use로 클릭하려 했으나 Side 창이 AX window를 노출하지 않아 `permission_denied`(AX reads stayed blocked)를 반환했다. 지시에 따라 같은 조치를 재시도하거나 우회하지 않았고, Orca 권한 확대도 요청하지 않았다.
- 수행하지 않은 것: SecurityAgent 조작·암호 입력, 시스템 설정의 TCC 토글 변경, `doctor` 실행(provider 호출 가능성), 프로세스 강제 종료, 비공개 캡처 열람 — 모두 미수행이다.
- 남은 사용자 조치(19:04 기준): 시스템 설정 → 개인 정보 보호 및 보안에서 Side의 **입력 모니터링**과 **화면 기록**(screenOcr 사용 중)을 껐다가 다시 켠다. 화면 기록은 「종료 후 재실행」을 요구할 수 있어, 그 경우 Side 정상 종료 후 Finder의 `/Applications`에서 `Side.app`을 직접 재실행한다. Keychain 프롬프트가 다시 표시되면 로그인 키체인 암호 입력 후 「항상 허용」을 선택한다.
- 앱·데몬 프로세스와 데이터 루트는 현 상태로 유지했다. 남은 두 권한 허용 뒤 `status`가 `running`으로 전환되는지는 Main이 별도 검증한다.
