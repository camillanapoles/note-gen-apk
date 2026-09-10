# PLAN — note-gen-apk

> Roadmap incremental. Cada etapa (Snn) tem: objetivo, entregável, requisito, stack,
> UC, testes, report (docs/reports/Snn.md — claim → evidência → veredito → ação).
> Regra de continuidade: **só avança com a etapa anterior verde.**

## S00 — Fundação (topologia + docs) ✅ 10/09/2026
- Objetivo: fork vivo com estratégia de branch R4 + docs-first.
- Entregável: `main` (espelho upstream/dev) + `feature/cnmfs-android` + PROJECT.md/PLAN.md commitados.
- Requisito: RF05 (parcial). Stack: git/gh (Termux). UC: —.
- Testes: `git rev-parse main == upstream/dev` (FF); branch feature existe no origin.
- Report: docs/reports/S00.md.

## S01 — CI garantista de build (adaptado do release.yml upstream)
- Objetivo: workflow próprio que builda o feature branch e produz APK assinado gated.
- Entregável: `.github/workflows/build-apk.yml` + secrets (KEYSTORE_BASE64/PASSWORD/ALIAS/KEY_PASSWORD) + keystore PKCS12 gerado.
- Requisito: RF04. Stack: GHA, pnpm 9, Rust stable, NDK 29, tauri android init+restore, apksigner.
- UC: UC-05.
- Testes (T-S01-*): build verde; `apksigner verify` V1+V2 OK; `aapt dump badging` contém package/versão; grep MANAGE_EXTERNAL_STORAGE no manifest merged (pós-S02); artifact `apk-candidate` publicado só se gates passam; job `promote` separado (workflow_dispatch `e2e-passed`) → `apk-validated` + release.
- Report: docs/reports/S01.md.

## S02 — Workspace Android livre (RF01)
- Objetivo: workspace em armazenamento compartilhado, leitura+escrita.
- Entregável: patch Rust (path resolution/resolução dual-path p/ /sdcard) + AndroidManifest (permissões + request runtime via plugin/Tauri mobile) + UI picker de pasta.
- Requisito: RF01, RNF01. Stack: Rust (src-tauri), Kotlin plugin (gen/android), TS settings.
- UC: UC-01.
- Testes: T-S02-01 CI grep permissões; T-S02-02 E2E dispositivo: criar nota → verificar .md em /sdcard/Documents/…; T-S02-03 E2E: persistência pós-reinstalação.
- Report: docs/reports/S02.md.

## S03 — Knowledge: ZIP + pastas via symlink (RF02)
- Objetivo: knowledge aceita zip (unpack + index) e pastas (symlink).
- Entregável: patch no pipeline RAG (processMarkdownFile/folder walk) + handler zip (Rust: zip crate) + criação de symlink Android (compat: fallback cópia rasa se FS sem symlink).
- Requisito: RF02. Stack: Rust + TS (skills/knowledge UI).
- UC: UC-02, UC-03.
- Testes: T-S03-01 unit Rust (zip → unpack → n arquivos); T-S03-02 E2E: add zip → RAG encontra conteúdo; T-S03-03 E2E: symlink pasta → index sem duplicação.
- Report: docs/reports/S03.md.

## S04 — Sync modular por pasta (RF03)
- Objetivo: cada pasta do workspace = módulo de sync independente.
- Entregável: refactor FolderSync → FolderModule (estado/queue por pasta; caminho por módulo no repo remoto; isolamento de falha).
- Requisito: RF03. Stack: TS (src/lib/sync/*).
- UC: UC-04.
- Testes: T-S04-01 unit TS (3 módulos, mudança em 1 → commit só no caminho dele); T-S04-02 E2E sync GitHub real com repo de teste.
- Report: docs/reports/S04.md.

## S05 — E2E completo + release validado
- Objetivo: prova E2E no Nothing A065 do APK final; promote a release.
- Entregável: artefato `apk-validated` + tag `cnmfs-v<versão>+<sha7>` + report.
- Requisito: RF04. Stack: Termux/adb + gh.
- UC: UC-05.
- Testes: T-S05-* (instala, abre, UC-01..04 no dispositivo).
- Report: docs/reports/S05.md.

## S06 — Manutenção upstream (contínuo)
- Objetivo: manter feature viva sob updates upstream.
- Entregável: `scripts/upstream-sync.sh` (fetch → FF main → merge feature → gates) + runbook + job agendado opcional (weekly check upstream).
- Requisito: RF05.
- Testes: T-S06-01: simular novo commit upstream → sync → gates verdes.
- Report: docs/reports/S06.md.

## Procedimento de merge upstream (runbook resumido)
1. `git fetch upstream`
2. `git push origin upstream/dev:main` (FF only; se rejeitar, investigar — NUNCA reescrever main)
3. `git checkout feature/cnmfs-android && git merge main`
4. Conflitos: resolver preservando features; conferir testes GWT das etapas afetadas
5. Push → CI gates → se verde, novo `apk-candidate`; E2E → promote
