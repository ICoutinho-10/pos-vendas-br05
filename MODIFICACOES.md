# Modificações — Pós-Vendas BR05

## v0.1.0 — 11/11/2026 — Criação (clone do BR02 sem login)
- Clonado de `inventario-br02` (contagem pública + painel de gestão).
- **Removido o sistema de login** do painel de gestão:
  - sem pasta `functions/` (Cloudflare Functions), sem `login.html`, sem `login/`, sem KV.
  - `admin/index.html`: removido o redirect github.io→Cloudflare; o "gate" de `/api/sessao`
    virou stub aberto (`uPapel='adm'`, esconde o `#gate`, o botão Sair e a aba Usuários).
  - aba **Usuários** escondida e inerte (não há backend de login).
- Rebrand **BR02 → BR05 / Pós-Vendas** (títulos, cabeçalho, marca, link "Abrir contagem").
- Cabeçalho do operacional passou a **exibir** o selo "· Pós-Vendas" (`.pv` de `display:none` → `inline`).
- `API` (Apps Script) deixada como **placeholder** `COLE_A_URL_EXEC_DO_APPS_SCRIPT_DO_BR05` nos dois arquivos.
- `sbf.json` subiu **vazio** (fallback offline) — não leva dados do BR02.
- Pendente: criar a planilha/Apps Script do BR05 e colar a URL `/exec` (ver README).
