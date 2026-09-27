*Version: v1.0 (2026-09-25)*

# Project 85 설치·점검 기록

## 판정

포크·소스 배치·로컬 앱 설치·이전 프로젝트 보존 이관은 확인했다. 실제 화면 수집, 권한 승인, 장시간 운전, 외부 모델 요약, MCP 등록은 수행하지 않았다. 독립 보안 리뷰는 `agent type is currently not available` 응답으로 `REVIEW_PENDING`이다. 아래 합성 테스트 결과를 실기기 개인정보 보호 수용으로 확대하지 않는다.

기준 소스는 `cabbb2b12aefdf2b12e70ddc610a79af66cebd02`이며 제품 소스 수정은 없다. 한영 상세 설명서의 제거 순서와 로컬 운영가이드만 변경했다. 커밋·push는 하지 않았다. 신규 fork 생성만 사용자 요청대로 수행했다.

## 요청별 검증

| ID | 범위 | 실행·근거 | 결과 |
|---|---|---|---|
| A1 | 포크 | `gh repo fork`, fork API의 parent 조회 | `dyoon98-creator/side-work-memory`, parent 일치 |
| A2 | 기존 85번 보존 | `ditto`, 기존 `verify_tree.py`로 경로·유형·크기·SHA 대조 | 13,968/13,968 일치, 추가·누락·불일치 0 |
| A3 | 보존본 이동 | 로컬 원본을 `_archive/20260925-side85.noindex/85.RemotrClone`으로 이동 후 재대조 | 13,968/13,968 일치. 이동으로 대상 경로가 바뀐 영향만 재검증 |
| I1 | 빌드 | `bun install --frozen-lockfile`, `bun run build` | exit 0, MiniLM q8 번들 포함 |
| I2 | 설치 실행 파일 | `/Applications/Side.app` 복사, `codesign --verify --deep --strict`, 설치 CLI `help` | exit 0, 명령 목록 출력 |
| I3 | 앱 중복 | Mac-Ops `audit-spotlight-app-duplicates.sh` | `com.minjaechai.Side`, 설치본 단일성 PASS |
| T1 | 설정·마스킹·암호화·인증·검색·MCP | 아래 Bun 테스트 8파일 | 309 pass, 0 fail, 690 assertions |
| T2 | 타입·정적 검사 | `bun run check` | 201파일, exit 0, 자동 수정 없음 |
| T3 | Swift 캡처·앱 코드 | `swift test --package-path apps/side-mac` | exit 0. 합성 OCR fixture 포함, 실제 사용자 화면 수집 없음 |
| R1 | 설치 후 미실행 상태 | 설치 CLI `status` | `Side is not running. Open Side.app.`. 기록 시작하지 않음 |
| D1 | 제거 절차 | `clear`가 `callDaemon` RPC에 의존함을 확인, 문서 순서 검사 RED 후 수정 | 삭제 의도 확인 → 실행 중 수집 중단·삭제 → 종료로 수정 |
| D2 | 운영 문서 | 루트 `운영가이드.md`, 기존 초보자 HTML 링크 | 설치·검색·중단·삭제·프라이버시·원복 구분 |
| S1 | 독립 보안 검토 | code-reviewer 호출 | REVIEW_PENDING, 역할 실행 불가 |

```sh
bun test tests/crypto.test.ts tests/config.test.ts tests/redact.test.ts tests/api/server.test.ts tests/contracts/settings.test.ts tests/daemon/reconcile.test.ts tests/recall/search.test.ts tests/mcp/server.test.ts
```

검증 범위는 실행 전에 기본 비활성 설정·마스킹·암호화·로컬 인증·검색·빌드·서명으로 선언했다. 같은 제품 소스의 테스트는 반복하지 않는다. 문서 변경은 제품 실행 파일을 바꾸지 않으므로 재빌드하지 않았다.

## 확인된 제약과 남은 항목

1. **암호화 범위:** `src/crypto/index.ts`는 AES-256-GCM을 사용한다. `src/memory/render.ts`는 날짜 페이지를 평문으로 기록하며 `src/memory/index-db.ts`도 평문 청크를 보관한다. 이는 README에도 고지된 설계이므로 ‘모두 암호화’라는 소개를 그대로 수용하지 않았다.
2. **마스킹 한계:** 알려진 규칙과 합성 사례 통과는 모든 개인정보 차단의 증거가 아니다. 초기 업무 자료 수집은 제외 목록과 사용자 승인 후 별도 시험이 필요하다.
3. **외부 전송:** 제공자 `allowEvidence` 기본값은 false다. 요약 전송과 MCP 연결은 별도 경로다. 두 경로 모두 이번에 활성화하지 않았다.
4. **보관 문구:** `src/memory/render.ts`의 날짜 페이지 안내는 ‘14 days’로 고정되어 있지만 설정은 1~30일을 허용한다. 원본 보관 기간을 오해할 수 있는 선재 표시 결함이다. 런타임은 변경하지 않았으며 운영가이드는 실제 설정을 기준으로 보도록 명시했다. 후속 소유자는 프로젝트 유지보수자이며, 페이지 안내 수정 시 렌더 테스트·스냅샷만 재검증한다.
5. **빌드 경고:** Keychain의 `kSecUseAuthenticationUIFail` 사용 중단 예정 경고가 발생했다. 빌드는 성공했다. 코드 주석상 legacy Keychain 경로와 관련되므로 경고 제거만을 위해 인증 동작을 변경하지 않았다.
6. **실기기 미검증:** 앱 UI, 실제 키체인 접근, TCC 권한, 재부팅 자동 시작, 장시간 CPU·메모리, 한국어 실제 작업 회수 품질은 미검증이다. `doctor`는 제공자 연결을 호출할 수 있어 이번에 실행하지 않았다.
7. **클라우드:** 지정 Google Drive의 로컬 복사본과 원본은 일치한다. 서버 업로드 완료는 확인하지 못했으므로 로컬 복구본을 삭제하지 않았다.

## 경로·원복

- 클라우드 보관 경로: `$HOME/Library/CloudStorage/GoogleDrive-<계정>/내 드라이브/PARA/04_Archives/85.RemotrClone`
- 로컬 복구본: `$HOME/Documents/Work/Projects/_archive/20260925-side85.noindex/85.RemotrClone`
- 새 프로젝트: `$HOME/Documents/Work/Projects/85.side-work-memory`
- 앱: `/Applications/Side.app`
- 빌드: `/Volumes/<외장-볼륨>/ProjectsData/85.side-work-memory/build.noindex` → 프로젝트 `.build` 심링크

원복은 Side 종료 후 새 앱·프로젝트를 별도 보관하고 기존 복구본을 원래 경로로 돌리는 방식이다. 파괴적 삭제, 기존 앱 프로세스 종료, 권한 변경, MCP 등록, 외부 요약 설정은 하지 않았다. 로컬 `.git/info/exclude`에는 빌드 심링크와 Bun 생성물을 제외했으며 원격에 반영하지 않았다.

## 적용 지침

Mac-Ops 운영가이드와 외부 변경·민감정보·Git·검증 상세절차, 프로젝트 저장구조 규칙, code-review, manual-production, systematic-debugging, verification-before-completion을 적용했다. 운영가이드는 기존 초보자 안내서를 보존하여 연결하고, 소스 확인과 실제 화면 검증을 구분했다.

## 수용 경계

설치·보존 이관·문서 작성은 로컬 산출 기준으로 닫는다. 제품 전체의 안전성은 `REVIEW_PENDING`이며 상시 수집 운용으로 승격하지 않는다. `version_state=UNVERSIONED`, `promotion_state=BLOCKED`는 이번 로컬 변경과 실기기 운용 수용에 관한 상태다. 원본 Git SHA와는 별개다.
