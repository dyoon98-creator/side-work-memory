# Side Work Memory — 설명만화 제작 기록

- 입력 가이드: `운영가이드.md`
- 제작 입력 SHA-256: `f1f7b6fcd8947d6c52ef8fed35c40b9be1102680a865b213c2e62a83b59e6799`
- 승인 브리프: `temp/guide-refresh-20260926/apps/luna-comic-briefs/85-side-work-memory-brief.md`
- 제작 규약: 2026-09-26 승인된 2페이지, 페이지당 6패널. 원문 밖의 runtime 완료·성공 상태는 표시하지 않는다.
- 리서치 범위: 승인 가이드와 brief만 사용. 추가 외부 사실 없음.

## 사실 coverage

| 페이지/패널 | Fact IDs |
|---|---|
| 1/1 | F85-01 |
| 1/2 | F85-02 |
| 1/3 | F85-03 |
| 1/4 | F85-03, F85-06 |
| 1/5 | F85-02, F85-03 |
| 1/6 | F85-04 |
| 2/1 | F85-05 |
| 2/2 | F85-05 |
| 2/3 | F85-05 |
| 2/4 | F85-06 |
| 2/5 | F85-06 |
| 2/6 | F85-02, F85-06 |

## Prompt page 1

```text
Use case: illustration-story. Asset type: first page of a Korean adult-operator explainer comic. One tall portrait PNG with a clean white background and exactly six numbered panels in a 2-column by 3-row grid, read left-to-right then top-to-bottom. Repeat the same adult operator and friendly small guide robot. Clean black line art, restrained blue accents, generous gutters, legible Korean text. Exact title “Side Work Memory — 1/2”. Show only abstract windows and icons, never real personal data. Panel 1: operator searches a work screen inside a clearly marked allowlist boundary while everything outside stays dim; bubble “허용한 화면의 기록만 찾아요.” caption “모든 과거를 읽지 않음”. Panel 2: installed app, permission and capture state appear as separate unchecked boxes; bubble “설치가 기록 시작은 아니에요.” caption “UI · TCC 확인 필요”. Panel 3: operator excludes finance, customer and authentication screens before choosing scope; bubble “허용 범위를 먼저 정해요.” caption “필요한 권한만”. Panel 4: capture, OCR, summary transfer and MCP are four separate switches, each considered individually; bubble “기능마다 따로 판단해요.” caption “한 경로가 다른 경로를 켜지 않음”. Panel 5: a public test window is selected but the capture result remains an empty checklist; bubble “실제 기록은 상태에서 확인해요.” caption “수집 확인 필요”. Panel 6: e, s and h result types have different symbols and operator opens the original source; bubble “유형을 보고 원문에서 확인해요.” caption “검색은 단서”. Sequential explainer comic, not a dashboard. Use only these exact labels; no real names, messages, screenshots, tokens, successful-capture badges, source paths, hashes, or watermark.
```

## Page 1 evidence

- Source guide SHA-256: `f1f7b6fcd8947d6c52ef8fed35c40b9be1102680a865b213c2e62a83b59e6799`.
- Built-in imagegen source `/Users/dongchanyoon/.codex/generated_images/01a0dbc4-28d8-7332-9013-8289f9b39887/exec-b6c57527-d219-4174-8123-dd4aeb1a671a.png`; selected PNG `/Users/dongchanyoon/Documents/Work/Projects/85.side-work-memory/image_side-work-memory_explainer_20260926_01.png`; SHA-256 `7fd418a4deab59a03cdd8521e6c0790c997f35c0fae5a9a4aec4db9f3ec5576d`; dimensions: 1024×1536.
- Prompt: this companion file, `Prompt page 1`; prompt SHA-256 `1b60870c9548d6b92e54f9b6db32855f058361c24326d6b8a5b3875123c7c3da`.
- Direct `view_image` and Main review: X1 PASS — permitted capture scope, installed/permission/capture distinction, and no claim that recording occurred. X2 PASS — exclusion boundaries and separate capture/OCR/summary/MCP choices match the guide. X3 PASS — Korean labels are legible and no personal data appears. X4 PASS — exactly six numbered panels with the intended reading order.
- Defects / regeneration: none; one generation call. Main accepted X1–X4.

## Prompt page 2

```text
Use case: illustration-story. Asset type: second page of a Korean adult-operator explainer comic. One tall portrait PNG with a clean white background and exactly six numbered panels in a 2-column by 3-row grid, read left-to-right then top-to-bottom. Continue the same adult operator and friendly small guide robot from page one. Clean black line art, restrained blue accents, generous gutters, legible Korean text. Exact title “Side Work Memory — 2/2”. Show privacy boundaries with abstract symbols and no personal data. Panel 1: pause stops only new capture while the existing record drawer stays; bubble “pause는 삭제가 아니에요.” caption “새 기록 중단”. Panel 2: clear all is behind an explicit confirmation gate and requires the app running; bubble “명확한 삭제 의사와 확인이 필요해요.” caption “앱 실행 중 확인”. Panel 3: side-memory records, browser history and Keychain are three separate drawers; bubble “브라우저 이력과 Keychain은 따로예요.” caption “clear all과 별개”. Panel 4: some protected source columns are shown distinct from plain-text dates, summaries and indexes; bubble “로컬 저장이 전부 암호화되진 않아요.” caption “마스킹은 알려진 패턴만”. Panel 5: backup covers the whole data root and separately checks Keychain while a DB-only copy is marked incomplete; bubble “DB 파일만으로 복구를 보장하지 않아요.” caption “데이터 루트 + Keychain”. Panel 6: live UI, TCC, external summary, MCP and doctor checks remain empty; bubble “미검증 항목은 확인 필요로 남겨요.” caption “첫사용과검색 · 설치와복원 · manual · agents · QA”. Sequential explainer comic, not a dashboard. Use only these labels, never depict real accounts, personal records, credentials, green success badges, source paths, hashes, or watermark.
```

## Prompt page 2 correction 1 (selected edit)

```text
Use case: text-localization edit. Asset type: corrected second page of a Korean adult-operator explainer comic. The referenced image is the edit target. Make a localized visual-state correction in panel 3 only; preserve the title, six panels, characters, labels, and all other panel content. Panel 3 compares the defined scope of “verify:fast” (lint and typecheck; tests not included) and “verify” (lint, typecheck, tests). All boxes currently show blue checks that can imply commands ran successfully. Change every box in both columns to an empty neutral gray outlined checkbox, including the “tests (포함되지 않음)” row. Keep exact labels and the bubble “fast에는 테스트가 없어요.” and caption “verify에는 tests 포함”. Do not imply execution or success. Leave all other panels unchanged; add no wording, source paths, hashes, or watermark.
```

## Page 2 evidence

- Source guide SHA-256: `f1f7b6fcd8947d6c52ef8fed35c40b9be1102680a865b213c2e62a83b59e6799`.
- Initial built-in imagegen candidate: `/Users/dongchanyoon/.codex/generated_images/01a0dbc4-28d8-7332-9013-8289f9b39887/exec-89780441-b89d-4b72-89d9-512983eb82d4.png`; SHA-256 `b94e3dc5d8bf47011e24ade02f268ebe7510cf8fbc18bc3839371c1fccfb08b9`; rejected because panel 4 introduced unsourced examples of encrypted items (app memo, attachment image, voice memo).
- Selected built-in imagegen edit source: `/Users/dongchanyoon/.codex/generated_images/01a0dbc4-28d8-7332-9013-8289f9b39887/exec-89c56c6d-ae7e-4c96-b03a-03a17eb3faff.png`; selected PNG: `/Users/dongchanyoon/Documents/Work/Projects/85.side-work-memory/image_side-work-memory_explainer_20260926_02.png`; SHA-256 `50c430a1002ee68f3424ca1fc345863d1658f1df5254ba431102b944473e648b`; dimensions: 1024×1536.
- Selected edit prompt SHA-256: `345ab03c64316706bd28004b1fbfc38420bf0ce85e832bd76895223fac958730`.
- Direct `view_image` and Main review: X1 PASS — pause leaves existing records; clear all requires explicit intent and app execution; browser history and Keychain are separate. X2 PASS — encryption is partial, known-pattern masking is bounded, backup includes the full data root plus Keychain, and DB-only recovery is not promised. X3 PASS — text is legible and contains no personal data. X4 PASS — exactly six numbered panels and consistent page-one characters/layout.
- Panel 3 footer reads `세 개는 서로 다른 서랍이에요. 하나를 지워도 다른 것은 그대로예요.` The three illustrations are separate storage areas, matching the source boundary among Side records, browser history, and Keychain. Main reread confirmed this exact wording; an initial differing visual read was withdrawn.
- Defects / regeneration: one targeted correction after the initial candidate; two calls total. Main accepted X1–X4.

## Source archive and entrypoints

- The original-manifest inventory did not contain an existing canonical comic for this project, and `운영가이드_만화설명.png` did not exist before installation; no original image was archived.
- Canonical page 1 installed at `운영가이드_만화설명.png`, SHA-256 `7fd418a4deab59a03cdd8521e6c0790c997f35c0fae5a9a4aec4db9f3ec5576d`; page 2 is `image_side-work-memory_explainer_20260926_02.png`, SHA-256 `50c430a1002ee68f3424ca1fc345863d1658f1df5254ba431102b944473e648b`.
- Root `운영가이드.md` input SHA-256 `f1f7b6fcd8947d6c52ef8fed35c40b9be1102680a865b213c2e62a83b59e6799`; final SHA-256 `9ee964ec9fa6bc7d5e7cc6b88d49cb76442a5cef6154ce665bfe347ec77150c6`. It links and embeds both pages and this companion.
- Existing bilingual HTML entrypoint `docs/index.html` input SHA-256 `ddc92b8fe9d0038354204d5338f103021f556d06ffb39f505c264a7f3a05158e`; final SHA-256 `8e5328648351f4309540e0499b06f3d6f17a0fb7ece2e6eae43ca20b321961c3`. Korean and English hero actions link directly to both comic pages; both relative links resolve.
- Unverified runtime states remain unverified.
