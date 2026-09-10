# TASKS — note-gen-apk

> Tasklist viva do feature branch. Atualizada a cada etapa; espelha PLAN.md.
> Convenção: `[ ]` pendente · `[~]` em andamento · `[x]` verde (report em .cnmfs/reports/)

## S01 — CI garantista de build 🔨
- [~] Run 34490027010 verde (build → gates estáticos → apk-candidate) — em 10/21 steps
- [ ] Report S01 (claim → evidência → veredito → ação)
- [ ] Primeira instalação do APK no Nothing A065 (smoke: abre, assinatura confere)

## S02 — Workspace Android livre (RF01)
- [ ] AndroidManifest: MANAGE_EXTERNAL_STORAGE + request runtime
- [ ] Core Rust: path resolution p/ armazenamento compartilhado (/sdcard/Documents, /sdcard/Download)
- [ ] UI mobile: picker de pasta p/ workspace
- [ ] T-S02-01 CI: grep permissões no merged manifest
- [ ] T-S02-02 E2E: nota criada aparece como .md no FS
- [ ] T-S02-03 E2E: persistência pós-reinstalação
- [ ] Report S02

## S03 — Knowledge: ZIP + pastas via symlink (RF02)
- [ ] Handler ZIP no core Rust (detecta → descompacta → indexa)
- [ ] Symlink de pasta no workspace/knowledge (+ fallback cópia se FS recusar)
- [ ] RAG varre conteúdo descompactado/ligado
- [ ] T-S03-01 unit Rust (zip → unpack → n arquivos)
- [ ] T-S03-02 E2E: zip adicionado → RAG encontra conteúdo
- [ ] T-S03-03 E2E: symlink → index sem duplicação
- [ ] Report S03

## S04 — Sync modular por pasta (RF03)
- [ ] Refactor FolderSync → FolderModule (estado/queue por pasta)
- [ ] Caminho remoto por módulo no repo GitHub
- [ ] Isolamento de falha (módulo com erro não derruba os demais)
- [ ] T-S04-01 unit TS (3 módulos, mudança em 1 → commit só no dele)
- [ ] T-S04-02 E2E sync GitHub real (repo de teste)
- [ ] Report S04

## S05 — E2E completo + release validada
- [ ] UC-01..UC-04 no dispositivo (Nothing A065, Android 16)
- [ ] promote → apk-validated + tag cnmfs-v<versão>+<sha7>
- [ ] Report S05

## S06 — Manutenção upstream (contínuo)
- [ ] scripts/upstream-sync.sh (fetch → FF main → merge feature → gates)
- [ ] Runbook de resolução de conflitos preservando features
- [ ] T-S06-01: simular commit upstream novo → sync → gates verdes
- [ ] Report S06
