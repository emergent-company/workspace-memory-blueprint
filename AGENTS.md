# workspace-memory-blueprint

Emergent Memory blueprint implementing a Dynamic Multi-Agent Orchestration System. Entirely declarative (YAML + JSONL, no code). Applying it installs a `multi-agent-task-pack` schema (14 object types, 17 relationship types), 10 pre-defined AI agent definitions, and seed data for pools, models, and KPIs.

Remote repo: `github.com/emergent-company/memory-blueprint-multi-agent`

## Run

```bash
# Preview (dry run — no mutations)
memory blueprints /path/to/workspace-memory-blueprint --project <id> --dry-run

# Apply to a project
memory blueprints /path/to/workspace-memory-blueprint --project <id>

# Apply from GitHub
memory blueprints https://github.com/emergent-company/memory-blueprint-multi-agent --project <id>

# Update after editing agent prompts or pack
memory blueprints /path/to/workspace-memory-blueprint --project <id> --upgrade
```

## Key Conventions

- No build step — consumed directly by `memory blueprints` CLI
- Local directory `workspace-memory-blueprint` maps to remote repo `memory-blueprint-multi-agent`
- Edit agent prompts in `agents/*.yaml`, schema in `packs/multi-agent-task-pack.yaml`
