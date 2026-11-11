# Modificações — Pós-Vendas BR05

## v0.3.2 — 11/11/2026 — Turno único "ADM" (BR05 não diferencia turno)
- Contagem (`index.html`): seletor de turno (contagem e conferência) passou de 1º/2º/3º → **único "ADM"**; `estado.turno` default = `ADM`.
- Admin (`admin/index.html`): modal "Atualizar SBF/MB52" lista só **ADM**; filtro de turno idem.
- Server (Apps Script) **não mudou**: `resetarBase`/`mapaResets_` guardam `resets[dia][turno]` de forma genérica — "ADM" funciona.
- Verificado no server: trava do cliente (`validarBaseTurno`) bloqueia lançamento sem reset do turno no dia (= "libera ao atualizar"). Reforço no server (`travaAtiva_()`/`TRAVA_ATIVA`) ainda DESLIGADO.

## v0.3.1 — 11/11/2026 — QTDE SBF visível na contagem
- A coluna **QTDE SBF** (qtde do SAP) deixou de ser mascarada na tela de contagem.
- Antes a célula era criada vazia + borrada (classe `esf`, "contagem às cegas").
- Agora mostra o valor real do SBF e o cabeçalho não fica mais acinzentado.
- Obs.: remove a proteção de contagem às cegas (o conferente passa a ver a qtde do sistema).

## v0.3.0 — 11/11/2026 — No ar (GitHub Pages)
- Repo publicado: **github.com/ICoutinho-10/pos-vendas-br05** (branch `main`).
- **GitHub Pages** ativado (Deploy from a branch → `main` / root).
- No ar e testado (sem login, 0 erros no console, planilha zerada → "0 contagens"):
  - Contagem: https://icoutinho-10.github.io/pos-vendas-br05/
  - Gestão:   https://icoutinho-10.github.io/pos-vendas-br05/admin/
- Atalhos do Desktop ("Pós-Vendas BR05 — Contagem/Gestão") apontam pro endereço online.

## v0.2.0 — 11/11/2026 — Backend ligado (App da Web publicado)
- Apps Script do BR05 **publicado como App da Web** (Versão 1), acesso **"Qualquer pessoa"**.
- URL `/exec` colada nos placeholders de `index.html` e `admin/index.html` (`var API = ...`).
- ID da planilha BR05 colado no link "Abrir planilha" do `admin/index.html`.
- Testado: `?api=ultimos` retorna JSON `{ok, dados}` lendo a planilha BR05. Sem tela de login.
- Planilha BR05 zerada e aba **LANÇAMENTOS** removida; no script `ABA_LANC` → `_LOG_CONTAGENS`.
- Placeholders só permanecem em README/MODIFICACOES (documentação).
- Pendente: criar repo GitHub `ICoutinho-10/pos-vendas-br05` + push + ativar GitHub Pages.

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
