# PROJECT — note-gen-apk (fork feature de codexu/note-gen)

> Projeto: otimização Android do NoteGen via fork mantido como feature-branch do main,
> com build CI garantista e APK como artefato validado.

## Objetivo
Manter um fork vivo de codexu/note-gen cujo branch `feature/cnmfs-android` carrega
features Android próprias (workspace livre, knowledge estendida, sync modular),
reconstruído por CI a cada mudança, com APK liberado como artefato **somente** após
testes de verificação (CI) + E2E (dispositivo) verdes.

## Estratégia de branch (R4)
- `main` (fork) = espelho **fast-forward only** de `upstream/dev`. Upstream atualiza → main avança.
- `feature/cnmfs-android` = integração das nossas features. Recebe `merge` de `main` periodicamente.
- Build CI **sempre do feature branch**. Conflitos de merge resolvidos preservando features —
  protegidas por testes GWT que devem continuar verdes pós-merge.

## Requisitos funcionais
- **RF01 — Workspace livre (Android)**: local do workspace configurável para armazenamento
  compartilhado (`/sdcard/Documents`, `/sdcard/Download`), leitura E escrita, com
  permissão `MANAGE_EXTERNAL_STORAGE` solicitada em runtime + fallback SAF.
- **RF02 — Knowledge estendida**: além de docs, aceitar **ZIP** (detecta → descompacta →
  indexa p/ RAG) e **pastas** (ligadas via **symlink** dentro do workspace/knowledge).
- **RF03 — Sync modular**: motor de sync GitHub trata **cada pasta do workspace como um
  módulo** (unidade independente de sync/estado; adicionar/remover pasta não afeta as demais).
- **RF04 — CI garantista**: workflow próprio (`build-apk.yml`) no fork: build → testes de
  verificação estáticos (assinatura, permissões, estrutura) → artefato `apk-candidate`;
  E2E no dispositivo → promote → artefato `apk-validated` + release.
- **RF05 — Manutenção upstream**: script `scripts/upstream-sync.sh` (fetch upstream →
  push FF para main → merge em feature → rodar gates) + runbook.

## Requisitos não-funcionais
- RNF01: não regredir desktop (features Android-gated por cfg/plataforma quando possível).
- RNF02: build CI ≤ ~30min (cache cargo/pnpm).
- RNF03: assinatura própria (keystore PKCS12 da conta camillanapoles; NUNCA a upstream).
- RNF04: segredos só via GitHub Secrets; keystore fora do repo.

## Entregáveis
- E1: topologia fork (main espelho + feature) + docs — ✅ 10/09/2026
- E2: `.github/workflows/build-apk.yml` com gates estáticos + keystore próprio
- E3: S01 workspace Android livre (RF01)
- E4: S02 knowledge zip+symlink (RF02)
- E5: S03 sync modular por pasta (RF03)
- E6: E2E dispositivo (Nothing A065, Android 16) + report
- E7: playbook upstream-sync (RF05)

## Casos de uso (GWT resumido)
- **UC-01**: Given APK instalado e permissão concedida, When workspace definido como
  `/sdcard/Documents/NoteGen`, Then nota criada no app aparece como .md no FS e persiste
  após reinstalação do app.
- **UC-02**: Given knowledge configurada, When usuário adiciona `pack.zip`, Then app
  descompacta e os .md internos ficam pesquisáveis no RAG.
- **UC-03**: Given pasta externa adicionada (symlink), When RAG indexa, Then conteúdo da
  pasta aparece nos resultados sem duplicar dados.
- **UC-04**: Given sync GitHub ativo com 3 pastas-módulo, When uma pasta muda, Then só o
  módulo dela sincroniza (commit/repo-path independente).
- **UC-05**: Given push no feature branch, When CI roda, Then APK só vira artefato
  validado se todos os gates verdes; otherwise falha alto e rastreável.

## Stack
Tauri 2 (Rust core) + Next.js/React/TS + pnpm · Android via gen/android (gradle) ·
CI GitHub Actions (ubuntu-latest + NDK 29) · assinatura apksigner V1+V2 ·
E2E: Termux + adb no Nothing A065.

## Fonte
Upstream: https://github.com/codexu/note-gen (branch `dev` = mainline).
Fork: https://github.com/camillanapoles/note-gen-apk (clone canônico: `~/note-gen` Termux).
