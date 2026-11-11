# Pós-Vendas BR05 · Inventário

Clone do app do **BR02** (`inventario-br02`) para o Centro de Distribuição **BR05 — Pós-Vendas**.
Mesma função: **contagem/conferência de estoque por endereço** (público) + **painel de gestão**
(dashboard, performance, histórico, Kardex). A **diferença** em relação ao BR02:

- **Sem login.** O painel de gestão (`/admin/`) é **aberto** — não tem Cloudflare Functions, KV,
  tela de login nem aba de Usuários. É **site estático puro** (só HTML/JS/CSS).
- Identidade **BR05 / Pós-Vendas** e **fonte de dados própria** (planilha BR05).

## Estrutura
```
index.html         app operacional (contagem pública) — lê/grava direto no Apps Script da planilha BR05
admin/index.html   painel de gestão (ABERTO, sem login) — mesmos dashboards do BR02
img/byd.png        logo
sbf.json           fallback offline do SBF (vazio — o real vem do Apps Script; regenere p/ BR05 se quiser offline)
_headers           no-store nas páginas (usado pelo Cloudflare Pages / ignorado no GitHub Pages)
.nojekyll          GitHub Pages serve os arquivos como estão
```
> Não tem a pasta `functions/`, `login.html` nem `login/` de propósito — era o sistema de login do BR02.

## ⚠️ FALTA 1 COISA PRA FUNCIONAR: a URL do Apps Script do BR05
Hoje o `index.html` **e** o `admin/index.html` apontam para um **placeholder**:
```
var API = 'https://script.google.com/macros/s/COLE_A_URL_EXEC_DO_APPS_SCRIPT_DO_BR05/exec';
```
Passos:
1. Criar a **planilha Google do BR05** (pode partir de uma cópia da planilha do BR02).
2. No editor **Apps Script** dela, publicar como **App da Web** (Implantar → Nova implantação →
   Tipo "App da Web" → acesso "Qualquer pessoa") e copiar a URL que termina em `/exec`.
3. Substituir o placeholder nos **dois** arquivos (`index.html` e `admin/index.html`), na linha `var API = ...`.

## Hospedagem (bem mais simples que o BR02, por ser estático)
Como não há login, pode ir em **GitHub Pages** direto (ou Cloudflare Pages, tanto faz):
- **GitHub Pages**: Settings → Pages → Source = *Deploy from a branch* → `main` / `/ (root)`.
  Fica em `https://icoutinho-10.github.io/pos-vendas-br05/` (operacional) e `.../pos-vendas-br05/admin/` (gestão).
- **Cloudflare Pages**: Connect to Git → build vazio → output `/`. (Não precisa de KV nem variáveis.)

## Observação de segurança
O `/admin/` é **público** (qualquer um com o link vê os dashboards). Foi o que você pediu.
Se um dia quiser trancar, dá pra reaproveitar o esquema de login do BR02 (`functions/` + Cloudflare KV).
