# Modificações — Pós-Vendas BR05

## v0.8.4 — 12/11/2026 — Correção: a ETIQUETA cortava em janela estreita
- Com `overflow-x:hidden` (v0.8.3), em janela estreita (ex.: a do atalho kiosk ~1040–1300px) a tabela não cabia e **cortava a coluna ETIQUETA** + a direita da SITUAÇÃO; o cabeçalho "DESCRIÇÃO" encostava no "LOCAL".
- Correções: o balão **"Posições lançadas" some em telas < 1500px** (a tabela ganha a largura toda) e as **colunas foram afinadas** (soma fixa ~652px, descrição = sobra).
- Verificado ao vivo (R01 A 01 A) em **1280px e 1040px**: sem rolagem lateral, **ETIQUETA 100% visível**, descrição com reticências, cabeçalhos sem encostar.

## v0.8.3 — 12/11/2026 — Conferência: tabela ocupa a largura, sem rolagem lateral, linhas densas
- Pedido do Ivan: **sem barra de rolagem horizontal**, **tabela redimensionada p/ a largura toda** e **linhas mais baixas** (caber o máximo de itens).
- Grid da Conferência passou de `minmax(0,1080px) 280px` → **`minmax(0,1fr) 300px`**; `fcard/ktool/kmsg` sem `max-width` → usa a tela toda. Abaixo de 1100px o balão "Posições lançadas" some e a tabela vira 1 coluna.
- `table-layout:fixed` + largura por coluna (descrição = sobra) e **`.fscroll{overflow-x:hidden}`** → acaba a rolagem lateral. Cabeçalhos longos quebram em 2 linhas (não estouram).
- Linhas compactas: `td` 1px, `input.fq` 26px, chips/seg menores → **~33px por linha** (era ~68). Testado ao vivo em **R01 A 01 A**: 0 overflow horizontal, coluna ETIQUETA inteira visível.

## v0.8.2 — 12/11/2026 — Etiqueta redesenhada + código de barras que lê
- **Pedidos do Ivan atendidos:** removido o **PN pequeno** (linha PN/Val.Lote) e o **PROCESSO/NF**; removido o **"46"** (nº da semana ISO — não serve) e a **hora**; **data** e **descrição** com fonte bem maior; **Quantidade ~dobrada** (fonte gigante, com auto-ajuste p/ 1–5 dígitos); **código grande descido** com folga do barcode. `Oper:` foi pra baixo da data.
- **Código de barras agora lê:** a causa era (1) `margin:0` → **sem quiet zone**, e (2) `+getAttribute('width')` virava `NaN` (valor vem como `"580px"`) → caía no fallback e **distorcia** as barras. Corrigido: `parseInt(...)` p/ largura/altura reais + **quiet zone** (`marginLeft/Right:44`), módulo mais largo (`width:4`), `preserveAspectRatio` sem esticar e `shape-rendering:crispEdges`. SVG vetorial = alta definição na Zebra.
- Testado render no navegador (barras uniformes com margem, layout sem corte). **Falta 1 impressão real** p/ validar a leitura no coletor e o tamanho 80×50.

## v0.8.1 — 12/11/2026 — Etiqueta: modal (peças + cópias), sem sobreposição e 1-clique na Zebra
- **Fluxo pedido pelo Ivan:** o botão **não imprime direto** mais — abre uma **janela (modal)** com **Qtd de peças** (vai no campo Quantidade da etiqueta, já pré-preenchido com a física/SBF) e **Qtd de etiquetas** (cópias, 1–200); Imprimir manda tudo de uma vez (uma etiqueta por página).
- **Sobreposição corrigida:** na faixa de cima, **ano/semana** não encostam mais (textLength trava a largura por coluna) e **FIFO / "Aprov. Qualidade:"** deixaram de ser cortados.
- **Impressão direta na Zebra (sem escolher impressora):** a Zebra `\\h2bruno-li\ZDesigner 170Xi4 300 dpi` **já está instalada** no PC do Ivan (e era a padrão). Criado perfil Chrome `...\AppData\Local\ZebraEtqKiosk` travado nessa Zebra (sem margem) + atalho **"Etiquetas BR05 — Zebra (1 clique)"** na Área de Trabalho (`--kiosk-printing`). Abrindo o app por esse atalho, Imprimir sai **direto na Zebra, sem diálogo**. Aditivo: não mexe no atalho ForkLift nem no padrão do Windows.
  - ⚠ Um perfil kiosk imprime sempre no destino travado dele: use o atalho **Zebra** p/ etiquetas e o **ForkLift** p/ o Kardex. **Falta 1 impressão real de teste** p/ confirmar tamanho 80×50 no driver.

## v0.8.0 — 12/11/2026 — Botão "Imprimir etiqueta" (Zebra, com código de barras) na Conferência
- Nova coluna **ETIQUETA** na tabela de Conferência Kardex: um botão por item que gera a **etiqueta 80×50 mm no mesmo padrão do Robô Baixa TOTVS** (faixa data/ano/semana/FIFO · Descrição/UM/Quantidade · PN · PROCESSO-NF/Oper · código grande) **+ código de barras Code128** acima do código (como o `etiqueta_totvs.py.bkp_barras`).
- Dados da etiqueta: **PN/código de barras = CÓDIGO SAP** do item; Quantidade = física (se preenchida) senão SBF; Oper = Conferente (`#fresp`). NF/UM/Val.Lote em branco (iguais ao robô quando vazios).
- Técnica: a etiqueta é um **SVG** (viewBox 944×590 = 80×50 mm @300 dpi) montado em `etqSVG()`; o código de barras vem do **JsBarcode** (CDN cdnjs 3.11.6, Code128) embutido como `<svg>` aninhado; impressão isolada num **iframe** com `@page{size:80mm 50mm;margin:0}` → não conflita com a impressão do Kardex (ForkLift).
- ⚠ **Impressora:** o robô original manda **ZPL cru** via `win32print` pra Zebra `\\h2bruno-li\ZDesigner 170Xi4 300 dpi`. Um app **web não envia ZPL direto** — então a etiqueta sai pela **impressão do navegador** (rasterizada): no diálogo, escolher a **ZDesigner 170Xi4** (ou montar um **atalho 1-clique Zebra** com `--kiosk-printing`, igual ao da ForkLift). Testado o render/geração do código de barras no navegador (SVG válido, 0 erro). **Falta testar 1 impressão real na Zebra** e, se preciso, ajustar a mídia 80×50 no driver.

## v0.7.0 — 12/11/2026 — Conciliação TOTVS × SAP (nova aba na Gestão)
- Nova aba **TOTVS × SAP** no painel de Gestão: diferença de saldo **de cada item** entre **TOTVS (SBF)** e **SAP (MB52)**, com KPIs (SKUs comparados, TOTVS a mais, TOTVS a menos, Conferem), tabela ordenada por maior divergência, filtros (Todos / Só divergentes / a mais / a menos), busca e **Exportar CSV**.
- **TOTVS** por item = soma do saldo por endereço (`b.p[k].l`); **SAP** por item = `b.sap[k].q` (soma da coluna "Utilização livre" do MB52). Itens só de um sistema entram com o outro lado = 0.
- **Apps Script:** adicionada a função `concilSistemas()` + branch `else if (acao === 'concil')` no `responder_`. **Acréscimo aditivo** (não altera endpoints existentes). **Salvo, mas PRECISA de NOVA VERSÃO (redeploy)** pra o `/exec` enxergar — Implantar → Gerenciar implantações → lápis → Versão: Nova versão → Implantar.
- ⚠ **Descoberta:** na MB52 atual a quantidade SAP é a coluna **"Utilização livre" (E)**; não há mais "UM básica". O Script já soma essa coluna (por nome de cabeçalho), então o saldo SAP interno (`b.sap`) está correto.

## v0.6.1 — 12/11/2026 — Kardex/app: FILIAL "AUTO" → "PÓS VENDAS"
- No **Kardex impresso** o campo **FILIAL** vinha "AUTO" (leftover do clone BR02) → agora **PÓS VENDAS** (cabeçalho estático `index.html:492` e a tabela renderizada/impressa `index.html:1057`).
- Título do app: "Inventário **AUTO** · BR05" → "Inventário **Pós-Vendas** · BR05" (removido o sufixo duplicado do subtítulo).

## v0.6.0 — 12/11/2026 — Reskin da Gestão (visual enterprise, sem cara de IA)
- **`admin/index.html`** (painel de Gestão) refeito no padrão **enterprise**: grafite / branco / cinza técnico + **vermelho BYD** como acento único e com moderação. Fontes **Geist** (textos) + **Geist Mono** (números/códigos). Sem glows, gradientes neon nem Oxanium.
- **Temas reduzidos:** saíram os neon (Azul-marinho/Lime/Cyan/Teal); ficam só **Grafite (escuro)** e **Claro**. Seletor de cor atualizado; `VALIDOS` = `["azul","claro"]` (valor salvo antigo cai pra grafite).
- Cores dos gráficos (rosca de acuracidade, barras, Top divergências) alinhadas à paleta: ok `#3f9b6d`, erro/baixa `#e2231a`, alta/alerta `#c6902f`, info `#3f78b5`. Nenhuma lógica/JS de dados alterada.
- Commit `0d49dad`, push no GitHub (Pages redeploy). Contagem (raiz) **não** mexida — só o painel de Gestão.

## v0.5.0 — 11/11/2026 — Impressão na ForkLift (1 clique no PC)
- **Impressora alvo corrigida:** o botão do Kardex apontava texto "Expedição_Auto (10.42.11.189)" → agora **ForkLift - Expedição** (`\\10.42.1.253\ForkLift - Expedição`, Brother DCP-L5652DN, 10.42.36.232), que é a impressora da área.
  - Botão: "IMPRIMIR · FORKLIFT"; `title` e a mensagem (`kMsg`) atualizados pra orientar PC (1 clique) e celular.
- **PC — 1 clique de verdade (sem caixa de impressão):** perfil Chrome dedicado em `C:\Users\ivan.silva\AppData\Local\ForkLiftKiosk` com a ForkLift semeada no `print_preview_sticky_settings`; atalho na Área de Trabalho **"Contagem BR05 — 1 clique (ForkLift)"** = `chrome --user-data-dir=...ForkLiftKiosk --kiosk-printing --app=<URL do app>`. Isolado: **não altera o padrão do Windows** (etiquetas Zebra seguem iguais). Testar 1 impressão; se cair em outra, confirmar a ForkLift uma vez no preview do perfil.
- **Celular:** navegador não imprime 100% silencioso (segurança do SO). Android = serviço de impressão Mopria/Brother; iPhone = AirPrint (a Brother suporta). Fica em poucos toques, com a ForkLift pré-selecionada.

## v0.4.0 — 11/11/2026 — Coluna SEGUIMENTO (Depósito da MB52)
- **Server (Apps Script, Versão 2):** nova `mapaDeposito_()` lê **coluna D (Depósito) da MB52** por material; `sbfSite()` passou a mandar `seg` no `?api=sbf` (array paralelo a `p`).
- **Contagem (`index.html`):** nova coluna **SEGUIMENTO** na tabela de conferência (entre LOCAL e QTDE SBF):
  - `6000 → FORKLIFT` (fundo azul claro `#bfdbfe`), `3000 → BUS/TRUCK` (fundo verde `#bbf7d0`), outros → código/`—`.
  - `seg` flui: `kardexSBF` → `k.itens` → `fItens` → `fLinha`/`fSeg`.
- Só exibe valor quando a **MB52 estiver colada** na planilha (hoje zerada). Falta (opcional): levar o seguimento pro **Kardex impresso**.

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
