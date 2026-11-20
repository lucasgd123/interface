================================================================================
PROJETO VERSAILLES — BLUEPRINT PARA IA
================================================================================
Gerado em: 20/11/2026
Local: C:\project V
Idioma: pt-BR
Destino: Contexto completo para outra IA entender o projeto, suas features,
organizacao, capacidades e plano de trabalho (plano 2.0 / blueprint).

================================================================================
1. VISAO GERAL
================================================================================
Projeto "Versailles" — customizacao do RPG Maker MZ (versao 1.1.1).
Pipeline de personagens "TV/mannequin" (PixelLab -> charsets MZ 8-dir).
Jogo principal: games/versailles/ (projeto MZ com data/, img/, js/plugins/,
game.rmmzproject).
Remote: https://github.com/lucasgd123/project-V.git (branch main).
Root: C:\project V

O projeto se propõe a transformar o RPG Maker MZ em uma "versao propria":
substituir templates originais pelos bodies TV padronizados, movimentacao 8-dir
unificada (sem codigo por personagem), animacao por esqueleto (PixelLab) e
sistema de camadas (cabelo, barba, olhos, nariz, acessorios).

Tom: dark/maduro, inspirado na tensao, escassez e consequencias de Fear &
Hunger (inspiracao de tom/mecanicas, SEM copia de conteudo protegido).
Ambientado na Francia pre-Revolucao Francesa ficcionalizada.
Jogador controla protagonista customizavel em mundo opressivo com narrativa de
escolhas, exploracao, sobrevivencia social/economica e gestao cotidiana
(fazenda/casa).

================================================================================
2. O QUE O PROJETO FAZ (ESTADO ATUAL — FASE 9 EM ANDAMENTO)
================================================================================

FASES CONCLUIDAS (conforme PROJECT_STATUS.md):
  Fase 0 — Fundacao & Token Guard
  Fase 1 — Extensao Core & Workbench
  Fase 2 — Map Editor (Dual Mode)
  Fase 3 — Database Editor
  Fase 4 — Character Composer Completo
  Fase 5 — Aspasia + 7 Agentes
  Fase 6 — IA Nativa Integrada
  Fase 7 — Sprite Editor + Inspector + UI Theme Editor
  Fase 8 — Build, Deploy & Pipeline

FASE 9 (EM ANDAMENTO) — Polish, Docs & Validação Final:
  - Documentacao pendente: IMPLEMENTATION_PLAN_MASTER.md,
    IMPLEMENTATION_PLAN_UNIFIED.md, PROJECT_CONVENTIONS_AND_BACKUP.md,
    MULTI_AGENT_ORCHESTRATION.md
  - Testes de integracao: E2E webview -> engine-bridge -> agentes -> validacao
    BIBLE (pendente); validar "Jogar Versailles.bat" completa (pendente)
  - Bug conhecido (baixo prioritario): ui-theme-engine.cjs — canvasToGrid()
    retorna array 2D, mas lockToPalette() espera { width, height, data }
  - Validacao final: node --check em todos os .js/.cjs/.mjs (0 falhas)

ITENS VALIDADOS (checklist):
  [x] Build pipeline: esbuild -> pngcrush -> palette-quant -> HTTP 8080
  [x] Git hook .husky/pre-commit valida BIBLE (Score >= 80)
  [x] Comando @git-commit via chat (engine-bridge)
  [ ] Testes end-to-end completos
  [ ] Validar launcher "Jogar Versailles.bat"

================================================================================
3. O QUE O PROJETO SE PROPOE A FAZER (PLANO / ROADMAP 2.0)
================================================================================
Fonte principal: docs/IMPLEMENTATION_PLAN.md (12/11/2026)

PRIORIDADE 1 — Templates (bodies TV):
  - Extrair zips preferidos de projeto/template/ para generator/TV/ com nomes
    canônicos:
    * TV_Adult_Male_Body
    * TV_Adult_Female_Body
    * TV_Child_Male_Body
    * TV_Teen_Male_Body
    * TV_Teen_Female_Body
  - Normalizar cada um contra o adulto de referencia (HeightRatio: 1.0 / 0.70 /
    0.85).
  - Buildar charsets (768x2048), copiar para img/characters/.
  - Atualizar data/LayeredCharacters.json (scale 0.311688).
  - Arquivar raws/testes; remover $Victor.png e sua entrada.

PRIORIDADE 2 — Movimentacao padronizada:
  - Bodies $-prefixed 768x2048 -> 8Dir_Movement trata 8 dirs + diagonais sem
    codigo por personagem.
  - Convenção: eventos/atores usam o charset name diretamente (ex: $Adult_Male_Body).
  - HD_Charset_Scaler le scale por nome (ja implementado).
  - Testar em mapa: 1 evento por body, verificar frames/diagonais.

PRIORIDADE 3 — Animacao por esqueleto (PixelLab):
  - Criar script reutilizavel Animate-Body.ps1 que recebe -PackDir, -Action
    (walking, running, idle breathing, punch, ...), -Directions (default 8) e
    gera/baixa a animacao via create-character-animation (template_action).
  - Walking primeiro (charset principal, 3 colunas: frame0, idle, frame4).
  - Outras acoes -> charsets opcionais ($Body_run.png, $Body_idle.png, ...).
  - Estender Fetch-PixelLabCharacter.ps1 para baixar frames de animacao
    (pastas por direcao: south/0.png...).
  - Build-CharacterSheet.ps1 deve aceitar parametro para selecionar frames.
  - Custo: ~8 gens/direcao × 8 dirs = ~64 gens por acao por body.
  - Camadas animadas só para acoes usadas (custo).

PRIORIDADE 4 — Camadas (cabelo, barba, olhos, nariz, acessorios):
  - Cada camada = charset $ 768x2048, fundo transparente, mesmo alinhamento/
    celula.
  - Geração: personagem completo com a camada -> color-key do mannequin ->
    normalizar -> buildar.
  - Piloto de color-key em 1 camada antes de escalar.
  - z-order: cabelo-traseiro -5 -> body 0 -> olhos 5 -> nariz 6 -> barba 7 ->
    cabelo-frontal 20 -> acessorios 30.
  - Fases: 4a cabelo (3-4 estilos x cores), 4b barba, 4c acessorios.
  - Olhos/nariz: manter default no body; camada de rosto opcional (requer body
    sem olhos) — decidir depois.

PRIORIDADE 5 — Faces:
  - Gerar faces HD (256x256) via PixelLab alinhadas aos bodies para diálogo.

PRIORIDADE 6 — Banco de dados (Actors.json):
  - Atores apontam para $<BodyName>; classes, inimigos, tropas.

PRIORIDADE 7 — Sistema de batalha:
  - Decidir: padrao / SV battlers / custom. Gerar assets conforme.

PRIORIDADE 8 — Tilesets:
  - Manter 48x48 (compatibilidade) ou criar HD.

PRIORIDADE 9 — Polimento:
  - Sombras, UI/menus/fontes, audio, titulo/save, animacoes de batalha,
    pictures, documentacao.

DECISOES JA TOMADAS:
  - Adulto = base de referencia (100% altura, 154px conteudo na celula 256x256).
  - Crianca = 70% (~108px), adolescente = 85% (~131px), normalizados contra
    adulto de referencia (pés maxY e centroX alinhados por direcao).
  - Scale visual unica = 0.311688 (= 48/154) para todos os bodies; diferenca
    de tamanho vem da arte, nao da scale.
  - Crianca female usa mesmo template da crianca (unissex nesta etapa);
    diferenca de sexo só a partir do adolescente/adulto.
  - Manter um olho default (dois pontos do mannequin) trocavel depois via camada.
  - Pele do mannequin: pálida por padrao.
  - Movimentacao por esqueleto = animacoes PixelLab (template_action) geradas
    por personagem, mantendo layout 8-dir.
  - Templates preferidos do usuario em projeto/template/ (zips PixelLab).
  - Teen female: gerar AMBAS as variantes (bald head featureless + female base body).
  - Crianca female: mesmo template da crianca (unissex).
  - Color-key: pele pálida por padrao; testar piloto em 1 camada de cabelo.
  - Olhos/nariz: manter default (dois pontos) no body; trocar depois via camada.
  - Bodies originais: substituir totalmente pelos novos.
  - Idle breathing: sim, gerar alem de walking.
  - Faces: depois das camadas.

================================================================================
4. ORGANIZACAO DO PROJETO (ESTRUTURA DE PASTAS)
================================================================================

games/versailles/            — Jogo principal MZ
  data/                      — Banco de dados MZ (Actors, Classes, Enemies,
                               Items, Skills, States, Weapons, Armors, Troops,
                               MapInfos, Map001, Map003, System, Tilesets,
                               CommonEvents, Animations, LayeredCharacters.json)
  img/                       — Imagens do jogo (characters/, parallaxes/, pictures/)
  js/                        — Codigo JS do jogo
    plugins.js               — Lista de plugins ativos (ver secao 6)
    plugins/                 — Plugins JS individuais
    rmmz_*.js                — Core do RPG Maker MZ (rmmz_core, rmmz_managers,
                               rmmz_objects, rmmz_scenes, rmmz_sprites,
                               rmmz_windows)
  main.js                    — Entry point
  game.rmmzproject           — Arquivo de projeto do MZ

studio/                      — Versailles Studio (UI, engines de pixel/mapa,
                               live-sync, bridge)
  workbench/                 — VSCodium/workbench (ferramentas locais)
  ui/                        — Interface do studio (generator-agent.js,
                               storage-manager.js, online-provider.js,
                               pixellab-integration.js, pipeline-orchestrator.js,
                               agent-manager.js, ai-chat.js, ...)

tools/                       — Scripts PowerShell do pipeline
  Normalize-BodyTemplate.ps1 — Altura relativa + alinhamento pés/centro por
                               direcao ao adulto de referencia
  Build-CharacterSheet.ps1   — Charset $ 3x8, 768x2048, celula 256x256
  Fetch-PixelLabCharacter.ps1— Baixa personagem do PixelLab
  Convert-ComfyUIToTVPack.ps1— Conversao ComfyUI para pacote TV
  Measure-TVBBoxMetrics.ps1  — Medicao objetiva de bbox (QA)

assets/templates/            — Zips/templates PixelLab canonicos
docs/                        — Contexto e planos
  AI_HANDOVER_CONTEXT.md     — Contexto para IA (handover)
  AI_HANDOVER_NITROV15.md    — Contexto Nitrov15
  IMPLEMENTATION_PLAN.md     — Plano de implementacao (fonte deste blueprint)
  IMPLEMENTATION_PLAN_MASTER.md — Plano mestre (pendente refatoracao)
  ART_BIBLE.md               — Diretrizes visuais e pipeline de arte
  GDD_VERSAILLES.md          — Game Design Document (rascunho vivo)
  STORY_BIBLE.md             — Biblia da historia/narrativa
  specs/                     -> Especificacoes (schemas, exemplos, reports)
  architecture/              -> Arquitetura (blueprint, dependency graph, gaps)
  comfyui/                   -> Workflows e instalacao ComfyUI
  legal/                     -> Auditorias de licenca (RPG Maker, third-party)
  setup/                     -> Setup de ambiente

.imports_pixellab/           — Imports do PixelLab
  metadata/                  — JSONs de metadata dos bodies importados
  extracted/                 — Bodies extraidos (Idle/rotations, walk/rotations,
                               walk/animations/Walking/*/frame_*.png, Child/rotations)
  approved/                  — Frames aprovados (victor walk, running, idle_copy)

.opencode/                   — Configuracao e agentes OpenCode do projeto
  agents/                    -> Definicoes de agentes (task-build, task-planner,
                               dev, code-review, git-commit, corrector,
                               pixellab-architect, workers Groq, entrypoints)
  plans/                     -> Planos salvos (.md) por data/tema
  state/                     -> Estados/handoffs efemeros por tarefa
    task-state.md            — Boletim de despacho canônico (~2 KB)
    _TEMPLATE.md             — Template canonico de state
    README.md                — Protocolo de handoff/estado
  templates/task-state.md    — Template canônico de state

opencode_termux/             — Repositorio clonado (fork de referencia) — NÃO
                               alterar/comitar a partir do repo principal sem
                               intencao explicita.
opencode.jsonc               — Configuracao OpenCode (providers Groq, agentes)
package.json                 — Dependencias do projeto raiz
AGENTS.md                    — Guia para agentes de IA (LER ANTES DE AGIR)
README.md                    — Visao geral e setup
PROJECT_STATUS.md            — Status detalhado por fase
api.env.txt                  — SEGREDO (token PixelLab pixl_...) — NUNCA versionar
                               nem commitar. Recriar localmente.

================================================================================
5. PIPELINE DE PERSONAGENS TV/MANNEQUIN
================================================================================
Fluxo:
  Pack PixelLab (256x256, 8 rotacoes, Idle/rotations, metadata.json)
    -> Normalize-BodyTemplate.ps1 (altura relativa + alinhamento pés/centro
       por direcao ao adulto de referencia)
    -> Build-CharacterSheet.ps1 (charset $ 3x8, 768x2048, celula 256x256)
    -> Copiar para games/versailles/img/characters/
    -> Registrar em games/versailles/data/LayeredCharacters.json

Escala visual unica: 0.311688 (= 48/154) para os bodies TV.
Diferenca de tamanho vem da ARTE (HeightRatio), nao da scale:
  Adulto = 1.0
  Crianca ~ 0.70
  Adolescente ~ 0.85

Alinhamento: normalizacao alinha pés (maxY) e centerX por direcao à referencia.
Origem e referencia devem ter as mesmas 8 direcoes.

Layout 8-dir (linhas do charset): S, W, E, N, SW, SE, NW, NE.

Walk/idle: charsets com walk usam frames (ex: 0, idle, 4). Idle breathing pode
estar em metadata.json conforme estado do pipeline.

Verificacao: NAO inspecionar imagens visualmente no modelo — usar medicao de
bbox/conteudo quando necessario (scripts/ferramentas).

Nomes com $ (ex: $Adult_Male_Body): sempre usar quoting correto em
PowerShell/git/comandos.

Arquivos de referencia do pipeline:
  - tools/Normalize-BodyTemplate.ps1
  - tools/Build-CharacterSheet.ps1
  - tools/Fetch-PixelLabCharacter.ps1
  - versailles/projeto v/js/plugins/{HD_Charset_Scaler,8Dir_Movement,
    Layered_Characters}.js
  - versailles/projeto v/data/LayeredCharacters.json
  - docs/AI_HANDOVER_CONTEXT.md

================================================================================
6. PLUGINS ATIVOS NO JOGO (plugins.js)
================================================================================
Fonte: games/versailles/js/plugins.js

  HD_Charset_Scaler          — Exibe charsets HD em escala reduzida sem alterar
                               o movimento no mapa. (Scale: 0.1875, CellSize: 256)
  8Dir_Movement              — Movimento em 8 direcoes e suporte a charsets $
                               com 8 linhas.
  MapZoom                    — Aplica zoom nativo no mapa usando
                               Game_Screen.setZoom. (Scale: 2.35)
  Layered_Characters         — Personagens compostos por varias camadas de
                               imagem. (ConfigFile: LayeredCharacters.json)
  Footsteps_System           — Sistema de passos sonoros com variacao organica
                               por terreno. (Volume: 20, PitchVariation: 8)
  Typewriter_Dialog          — Efeito typewriter com pausa organica em
                               virgulas/pontos e bips retro.
  Transitions_System         — Transicoes customizadas (Iris Wipe circular e
                               Pixelated Dissolve retro).

================================================================================
7. LAYEREDCHARACTERS.JSON (INTEGRACAO ATUAL NO JOGO)
================================================================================
Fonte: games/versailles/data/LayeredCharacters.json

Registros atuais (todos com scale 0.311688):
  $Male_Body
  $Female_Body
  $Child_Body
  $Teen_Male_Body
  $Teen_Female_Body
  $Teen_Female_Body_v2  (referencia-base de traco/coerencia estetica)
  char_adult_m_1795050391524 (template: adult_m, bibleScore: 100,
                               registeredAt: 2026-11-19T01:06:34.103Z)

Cada entry tem: scale, layers (arquivo + z-order).

================================================================================
8. AGENTES E ORQUESTRACAO (OpenCode)
================================================================================
Fonte: AGENTS.md + .opencode/agents/

  task-build  (primary) — Orquestra planejamento → implementação → revisão →
                          commit. Possui permissao para editar/gravar arquivos
                          diretamente (sem git de escrita direta; delega ao
                          git-commit).
  task-planner  — Gera planos em .opencode/plans/. Pode invocar
                  moltbook-ideation para ideaçao externa.
  dev         — Implementa tasks (edicao de arquivos/scripts/docs/config),
                sem git de escrita.
  code-review — Revisa codigo/planos (somente leitura), com vereditos
                padronizados.
  git-commit  — Staging/commits/branches (push/merge somente com confirmacao).
  corrector   — Revisor tecnico adicional (somente leitura), segunda opiniao
                via modelo/conta Groq separada.
  groq-summarizer, groq-extractor, groq-micro-reviewer — Workers de apoio
                (~8000 tokens, subtarefas pequenas). Nao substituem dev,
                code-review, corrector nem git-commit.

Regras de orquestracao:
  - NUNCA modificar codigo/arquivos diretamente — SEMPRE delegar para dev.
  - NUNCA executar comandos git de escrita — delegar para git-commit.
  - SEMPRE apresentar plano ao usuario e aguardar aprovacao (gate).
  - Code-review e obrigatorio apos cada task do dev antes de proxima task.
  - Maximo de 3 tentativas por task antes de escalar via QUESTION TOOL.
  - Circuit breaker: 3+ tasks consecutivas "Precisa de ajustes" -> pausa.

Uso de chaves Groq (mapeamento estrito, sem fallback):
  groq_main        -> GROQ_API_KEY_MAIN
  groq_review      -> GROQ_API_KEY_REVIEW  (agente corrector)
  groq_parallel    -> GROQ_API_KEY_PARALLEL
  groq_worker1..3  -> GROQ_API_KEY_WORKER1..3 (workers de apoio)

Seguranca: chaves Groq via variaveis de ambiente; nunca commitar segredos.

================================================================================
9. CONVENCOES E REGRAS CRITICAS
================================================================================
Seguranca:
  - NUNCA imprimir, logar, expor ou commitar api.env.txt (token PixelLab pixl_...).
  - Nunca commitar segredos, chaves de API (PixelLab/Groq/outros) ou
    credenciais em arquivos versionados.
  - Chaves Groq devem vir de variaveis de ambiente.

Git e versionamento:
  - Nao versionar RPG Maker MZ/ (editor/binarios) nem api.env.txt.
  - Preferir mudancas atomicas e commits claros; revisar git status/diff antes
    de commitar.
  - opencode_termux/ e um repositorio clonado dentro do workspace — evitar
    alterar/comitar nele a partir do repo principal sem intencao explicita.

Naming de charsets/bodies:
  - Body: $Adult_Male_Body, $Adult_Female_Body, $Child_Body,
    $Teen_Male_Body, $Teen_Female_Body.
  - Camada: $<Body>_<Part>_<Variant> (ex: $Adult_Male_Body_Hair_Short).
  - Charset: 768x2048 (3x8), celula 256x256.

Pipeline de personagens:
  - Scale visual fixa 0.311688 para bodies TV.
  - Layout 8-dir: S, W, E, N, SW, SE, NW, NE.
  - Alinhamento por bbox (maxY e centerX), sem inspecao visual pelo modelo.

Armadilhas conhecidas:
  - PowerShell: cuidado com quoting de caminhos com espacos; evitar \" em
    contextos que quebram o parser — preferir aspas simples quando adequado.
  - Nomes com $ sempre com quoting correto.
  - Normalize-BodyTemplate.ps1 itera sobre arquivos da referencia: packs
    precisam ter o mesmo conjunto de direcoes.

================================================================================
10. ESTADO DO PROJETO (RESUMO EXECUTIVO)
================================================================================
- Projeto Versailles em Fase 9 (Polish, Docs & Validacao Final).
- Fases 0-8 concluidas.
- Build pipeline validado (esbuild -> pngcrush -> palette-quant -> HTTP 8080).
- Testes unitarios: 33/33 aprovados (Stage 5b provider de IA real).
- 0 falhas em node --check de todos os .js/.cjs/.mjs.
- Bug conhecido de baixa prioridade em ui-theme-engine.cjs.
- Proximos passos: atualizar planos mestres, validacao final E2E, validar
  "Jogar Versailles.bat", corrigir teste de geracao de direcoes.

================================================================================
11. COMO ENTREGAR ESTE ARQUIVO PARA OUTRA IA
============================================================================

================================================================================
FIM DO BLUEPRINT
================================================================================
