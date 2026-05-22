# Skills

This repository contains Codex skills that can be installed into `$CODEX_HOME/skills` or `~/.codex/skills`.

## Available Skills

- `prompt-writer`: writes copy-ready staged execution prompts for Codex/agent conversations from planning docs, handoff notes, issue analyses, or user requirements.

## Install

Install `prompt-writer` from this repository with the Codex skill installer:

```bash
python /path/to/skill-installer/scripts/install-skill-from-github.py \
  --repo shunbao-p/Skills \
  --path skills/prompt-writer
```

After installation, restart Codex to pick up the new skill.

## Manual Install

Copy `skills/prompt-writer` to:

```text
~/.codex/skills/prompt-writer
```

Then restart Codex.
