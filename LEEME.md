# Skills y agentes para el equipo BizzGrowth

Paquete con skills, agentes y prompts de terceros para trabajar más rápido y gastar menos tokens en Claude Code, Cursor u otros agentes de código.

**Todo el contenido es de terceros.** Cada carpeta trae su LICENSE (o LICENSE.txt). Respétenla al copiar o redistribuir.

## Qué trae cada carpeta

### 01-gstack — https://github.com/garrytan/gstack — MIT

El setup público de Garry Tan (YC): un “equipo virtual” en forma de slash commands (oficina, review, QA, diseño, ship, etc.).

- 62 skills de primer nivel, más 1 skill meta en la raíz y 6 variantes (OpenClaw, Capy, QM y una de browser).
- No hay una librería aparte de agentes: los roles *son* las skills. Trae `agents/openai.yaml` y el digest portable `agents-digest/gstack-AGENTS.md`.
- Versión 1.103.3.0, commit `1145e80`.
- Para aligerar el archivo se omitieron `test/` y `CHANGELOG.md`. El repo completo está en GitHub.

### 02-anthropics-skills — https://github.com/anthropics/skills — Apache-2.0

Repo oficial de Anthropic. 18 skills con `LICENSE.txt` Apache-2.0 (PDF, Word, Excel, PowerPoint, frontend-design, skill-creator, mcp-builder, etc.).

- Dentro de `skill-creator` hay 3 agentes: analyzer, comparator y grader.
- Se excluyó `doc-coauthoring` porque no traía licencia verificable.
- Commit `e4bb5d9`.

### 03-obra-superpowers — https://github.com/obra/superpowers — MIT

Metodología muy usada: definir bien el problema, planear, TDD y revisar.

- 15 skills. Las más útiles para no inflar el contexto son `subagent-driven-development` y `dispatching-parallel-agents`.
- Commit `22f0b46`.

### 04-caveman — https://github.com/JuliusBrussee/caveman — Apache-2.0

Skills pensadas para respuestas cortas y menos tokens (repo muy conocido).

- 22 skills (`caveman`, `ultracave`, `megacave`, `lean-build`, `investigate-first`, etc.).
- 3 agentes: cavecrew-builder, cavecrew-investigator, cavecrew-reviewer.
- Comandos: `/caveman`, `/ultracave`, `/megacave`, entre otros.
- El árbol también incluye motor y proxy opcionales. **Para ahorrar tokens basta copiar las skills y los agentes.** No hace falta el instalador `curl | bash`.
- Commit `47c61be`.

### 05-gbrain — https://github.com/garrytan/gbrain — MIT

Memoria persistente de Garry Tan para agentes: notas, decisiones y docs de la empresa en un solo cerebro que el agente consulta entre sesiones. El README de gstack apunta a este repo.

- 76 skills canónicas en `skills/` (query, ingest, setup, briefing, capture, etc.). Hay copias empaquetadas en `plugin/` (72) y en las variantes `plugin-variants/gbrain-coding` (21) y `gbrain-daily` (20).
- No hay una librería de agentes en Markdown. Sí trae `.agents/gbrain-launcher`, manifests de plugin (`.claude-plugin/`, `.codex-plugin/`) y los prompts `AGENTS.md`, `CLAUDE.md`, `INSTALL_FOR_AGENTS.md` y `BOOTSTRAP_FOR_AGENTS.md`.
- Versión 0.60.774.0, commit `1701a0a9`.
- **Esto no es solo un set de skills:** hace falta el CLI de GBrain. En este paquete se omitieron `src/`, `rust/`, tests, `docs/eval/` y el changelog para no cargar WASM ni el código del motor. Ver `05-gbrain/QUE-SE-OMITIO.txt`.

**Requisitos para usarlo de verdad:**

- Bun 1.4.4 o más nuevo. **No** instalar el paquete `gbrain` de npm (es otro, no oficial). Lo correcto: `bun install -g github:garrytan/gbrain#stable`.
- Motor local: `gbrain init --pglite` (Postgres embebido, sin Docker ni servidor). También se puede usar Postgres o Supabase.
- La búsqueda por palabra clave **no pide claves de API**. Las API keys (OpenAI, Anthropic, etc.) son opcionales, para embeddings y respuestas sintetizadas.
- Luego conectar el agente: `gbrain setup claude-code` (o `codex`, o Cursor vía MCP). También existe hospedaje de pago en gbrain.io; no es obligatorio.
- Lean `05-gbrain/docs/INSTALL.md` y `05-gbrain/INSTALL_FOR_AGENTS.md` antes de cualquier instalador. Hay un `curl | bash` para el hosted; no lo corran sin leerlo. Con gstack, `/setup-gbrain` y `/sync-gbrain` orquestan el alta.

Ver también `VERSIONES.txt`.

## Cómo instalarlo

**No ejecuten** `./setup`, `install.sh` ni un `curl | bash` sin leer el script antes. En este paquete no se corrió ningún instalador.

### A. Copiar a la carpeta de skills (lo más simple)

En Claude Code, para tu usuario:

```bash
mkdir -p ~/.claude/skills ~/.claude/agents

# gstack se instala como un solo árbol
cp -a 01-gstack ~/.claude/skills/gstack

# el resto, skill por skill
cp -a 03-obra-superpowers/skills/* ~/.claude/skills/
cp -a 04-caveman/skills/* ~/.claude/skills/
cp -a 02-anthropics-skills/skills/* ~/.claude/skills/

# agentes de Caveman
cp -a 04-caveman/agents/cavecrew-*.md ~/.claude/agents/
```

GBrain no se aprovecha solo copiando skills: instalen el CLI oficial y corran `gbrain setup claude-code` (ver arriba). Las skills de `05-gbrain/skills/` quedan como referencia y también las registra el propio setup.

Para un solo proyecto, copien lo mismo a `.claude/skills/` y `.claude/agents/` en la raíz del repo (no en `~`).

En **Cursor**: `~/.cursor/skills/` (usuario) o `.cursor/skills/` (proyecto). gstack también documenta `./setup --host cursor`. GBrain se conecta por MCP (`gbrain serve`).

Reinicien el agente después de copiar.

No instalen todo si el equipo no lo va a usar: cada skill suma texto al contexto. Un buen mínimo para ahorrar tokens: **Caveman + Superpowers** (subagentes) y, si usan el flujo de equipo, **gstack**. GBrain suma si quieren memoria entre sesiones; no reduce tokens por sí solo (a veces evita repetir contexto, a veces añade una consulta).

### B. Instalador oficial de gstack (Claude Code)

Requisitos: Claude Code, Git y Bun 1.4.2+. Lean `01-gstack/docs/install.md` y el propio `./setup` antes de correrlo (puede registrar hooks y, si no hay navegador Aside, descargar uno).

```bash
cp -a 01-gstack ~/.claude/skills/gstack
cd ~/.claude/skills/gstack
./setup
```

En el `CLAUDE.md` del proyecto, solo esto (corto; no listen las skills):

```
## gstack
Use /browse from gstack for all web browsing. Never use mcp__claude-in-chrome__* tools.
```

### C. Plugins oficiales (si ya usan Claude Code)

- Superpowers: `/plugin install superpowers@claude-plugins-official`
- Anthropic: `/plugin marketplace add anthropics/skills` y luego `document-skills` o `example-skills`
- Caveman: `claude plugin marketplace add JuliusBrussee/caveman` y `claude plugin install caveman@caveman`
- GBrain: `/plugin marketplace add garrytan/gbrain` y `/plugin install gbrain@gbrain` (además del CLI)

## Hábitos que de verdad bajan el consumo de tokens

Las skills ayudan, pero estos hábitos son los que más recortan la factura:

1. **`/clear` entre tareas distintas**, para no arrastrar contexto viejo.
2. **`/compact`** cuando la conversación se alargue, antes de que se hinche.
3. **Mantengan `CLAUDE.md` / `AGENTS.md` cortos**: reglas del repo, no tutoriales ni listas de skills (se quedan viejas y gastan tokens en cada turno).
4. **No peguen archivos enteros.** Den la ruta; el agente debe buscar y leer solo las líneas necesarias.
5. **Modelo más barato** (Haiku u equivalente) para búsquedas, resúmenes y tareas simples. Reserven el modelo fuerte para diseño y código delicado.
6. **Subagentes para explorar** (Superpowers: `dispatching-parallel-agents` / `subagent-driven-development`; Caveman: Cavecrew investigator). Al chat principal debe volver un resumen corto, no el volcado.
7. **Modo conciso**: activen la skill `caveman` o `/caveman`. Eviten que el agente narre cada paso (“Voy a leer…”, “Perfecto, ahora…”).
8. **Una tarea por chat.** No relean un archivo que acaban de editar. Agrupen llamadas independientes. No instalen decenas de skills que nadie usa.

## Revisión de seguridad

Antes de empaquetar se buscó secretos, tokens, claves y patrones tipo `curl | sh` o post-install raros. **No se ejecutó ningún instalador.**

- No hay archivos `.env` reales ni claves privadas de producción.
- Hay un placeholder en `01-gstack/.env.example`: `ANTHROPIC_API_KEY=sk-ant-your-key-here`.
- En tests de Caveman (y en tests de GBrain, que no se incluyeron) aparecen claves de fixture y certificados de prueba, no credenciales vivas.
- gstack, Caveman y GBrain documentan instaladores `curl | bash`. Léanlos antes de usarlos.
- Ningún `package.json` incluido define scripts `postinstall`.
- Telemetría de gstack: apagada por defecto (hay que aceptar). El chequeo de actualizaciones es aparte; se apaga con `gstack-config set update_check false`. Detalle en `01-gstack/docs/privacy.md`.
- GBrain en modo clave (`--no-embedding`) no necesita API keys. Si más adelante añaden claves o una URL de Postgres/Supabase, no las suban a un repo ni las dejen en el chat.
