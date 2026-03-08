# Multi-Agent Task Management Blueprint

A deployable Emergent Memory blueprint that implements the Dynamic Multi-Agent Orchestration System.

Apply it to any Emergent Memory project to get a complete knowledge schema, a pre-wired set of AI agents, and seed data for pools, models, and KPIs — ready to run.

## What this installs

### Template pack — `multi-agent-task-pack`

Two knowledge layers:

**Domain layer** (the actual work):

| Type | Purpose |
|---|---|
| `WorkPackage` | Top-level unit of work; owns the full execution tree |
| `Task` | Any unit of work; forms a tree via `has_subtask` |
| `AcceptanceCriteria` | Checklist item defining what "done" looks like |
| `TaskResult` | Structured output produced by an agent |
| `FeedbackSignal` | Accept / reject signal with optional explanation |

**System layer** (meta-knowledge about the system):

| Type | Purpose |
|---|---|
| `AgentDefinitionRecord` | Data identity of an agent variant (type, tools, model, pool) |
| `AgentPool` | A named pool of agent variants managed by a pool manager |
| `AgentRun` | A single execution of an agent against a task |
| `Experiment` | An A/B test comparing variants on the same task |
| `ExperimentVariant` | One arm of an experiment |
| `KPI` | Tracked performance metric |
| `KPISnapshot` | Point-in-time reading of a KPI |
| `JanitorProposal` | Structural change proposed by the janitor; requires human approval |
| `Model` | Registered LLM available in the system |

### Agent definitions (10 agents)

| Agent | Type | Model | Purpose |
|---|---|---|---|
| `leaf-enricher` | leaf | gpt-4o-mini | Extract and enrich structured data from raw content |
| `leaf-researcher` | leaf | gpt-4o-mini | Research tasks; synthesise findings |
| `leaf-coder` | leaf | gpt-4o | Produce code artifacts against acceptance criteria |
| `leaf-reviewer` | leaf | gpt-4o-mini | Evaluate TaskResults; emit structured FeedbackSignals |
| `leaf-designer` | leaf | gpt-4o | Architecture, API specs, design docs |
| `pool-manager-research` | pool_manager | gpt-4o | Owns research pool; runs A/B experiments |
| `pool-manager-coding` | pool_manager | gpt-4o | Owns coding pool; manages escalation |
| `pool-manager-review` | pool_manager | gpt-4o-mini | Owns review pool; monitors review quality |
| `orchestrator` | orchestrator | gpt-4o | Decomposes work packages; human checkpoints |
| `janitor` | janitor | gpt-4o | System analysis; proposes structural improvements |

### Seed data

- 5 `AgentPool` objects (research, coding, review, enrichment, design)
- 10 `AgentDefinitionRecord` objects (one per agent above)
- 5 `Model` objects (gpt-4o-mini, gpt-4o, claude-3-5-sonnet, o1, o3-mini)
- 10 `KPI` objects with target baselines
- Relationships: pool membership, manager assignments, model bindings, KPI scoping

## Usage

```bash
# Preview what will be applied (no mutations)
memory blueprints /path/to/this/repo --project <your-project-id> --dry-run

# Apply to a project
memory blueprints /path/to/this/repo --project <your-project-id>

# Apply from GitHub (once pushed)
memory blueprints https://github.com/your-org/workspace-memory-blueprint --project <id>

# Update existing resources (e.g. after editing agent prompts)
memory blueprints /path/to/this/repo --project <your-project-id> --upgrade
```

## Directory layout

```
packs/
  multi-agent-task-pack.yaml      ← full schema (14 object types, 17 relationship types)
agents/
  leaf-enricher.yaml
  leaf-researcher.yaml
  leaf-coder.yaml
  leaf-reviewer.yaml
  leaf-designer.yaml
  pool-manager-research.yaml
  pool-manager-coding.yaml
  pool-manager-review.yaml
  orchestrator.yaml
  janitor.yaml
seed/
  objects/
    AgentPool.jsonl               ← 5 pools
    AgentDefinitionRecord.jsonl   ← 10 agent records
    Model.jsonl                   ← 5 LLM models
    KPI.jsonl                     ← 10 KPI definitions
  relationships/
    member_of_pool.jsonl          ← agents → pools
    managed_by.jsonl              ← pools → managers
    uses_model.jsonl              ← agents → models
    kpi_for_pool.jsonl            ← KPIs → pools
```

## Lab phase validation

Per the concept doc's recommendation, validate with a contained test before connecting to real workloads:

1. Apply this blueprint to a test project
2. Create a `WorkPackage` with `title: "Build a to-do list app"` using `memory graph create`
3. Trigger the `orchestrator` agent with the work package key
4. Observe task tree formation, agent assignments, acceptance criteria creation
5. Let at least one rejection cycle complete (reviewer → enricher/coder)
6. After work package closure, trigger `janitor` and review its `JanitorProposal` objects
7. Calibrate KPI baselines from observed run data; update via `--upgrade`

## Customising

### Adding a new agent variant to a pool

1. Copy an existing leaf agent YAML, change the `name`, `model.name`, and `config.tier`
2. Add a new `AgentDefinitionRecord` entry to `seed/objects/AgentDefinitionRecord.jsonl`
3. Add a `member_of_pool` entry to `seed/relationships/member_of_pool.jsonl`
4. Re-apply with `--upgrade`

### Changing model defaults

Edit the `model.name` field in any agent YAML and re-apply with `--upgrade`.

### Adjusting KPI targets

Edit `seed/objects/KPI.jsonl` and re-apply with `--upgrade`.

## Spec

Full design rationale: see `docs/multi-agent-blueprint-spec.md` in the source repo.
Concept document: `docs/multi-agent-work-concept.md`.
