# Skills

This repository contains Codex skills that can be installed into `$CODEX_HOME/skills` or `~/.codex/skills`.

## Available Skills

- `prompt-writer`: writes copy-ready staged execution prompts for Codex/agent conversations from planning docs, handoff notes, issue analyses, or user requirements.
- `ppt-createrskill-pan`: turns PDFs, Word documents, reports, papers, company documents, screenshots, or mixed source materials into a high-quality, mostly editable PPT/PPTX with a five-stage workflow.

## Install

Install a skill from this repository with the Codex skill installer.

### Install `prompt-writer`

```bash
python /path/to/skill-installer/scripts/install-skill-from-github.py \
  --repo shunbao-p/Skills \
  --path skills/prompt-writer
```

### Install `ppt-createrskill-pan`

```bash
python /path/to/skill-installer/scripts/install-skill-from-github.py \
  --repo shunbao-p/Skills \
  --path skills/ppt-createrskill-pan
```

After installation, restart Codex to pick up the new skill.

## Manual Install

Copy the desired skill folder to `~/.codex/skills/`.

Examples:

```text
~/.codex/skills/prompt-writer
~/.codex/skills/ppt-createrskill-pan
```

Then restart Codex.

## Notes

- `ppt-createrskill-pan` works best when the target Codex environment has `presentations`, `spreadsheets`, and `imagegen / Image2` available.
- If some of those runtime capabilities are missing, the workflow can still be partially useful, but full editable PPT delivery may be limited.
