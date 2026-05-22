---
name: prompt-writer
description: Write high-quality, copy-ready execution prompt documents from planning docs, handoff notes, issue analyses, or user requirements. Use when the user asks to create or revise prompts for Codex/agents to execute staged development, implementation plans, fixes, reviews, or multi-phase tasks; especially when prompts must preserve the user's preferred format, execution rhythm, constraints, subagent rules, validation requirements, and per-stage self-contained instructions.
---

# Prompt Writer

## Purpose

Create a prompt document that the user can copy directly into a Codex/agent conversation to execute staged work. The output must be operational, not merely descriptive: each prompt block must contain all rules the executing agent must obey.

## Workflow

1. **Gather source context**
   - Read user-provided planning docs, analysis docs, handoff notes, and previous prompt templates.
   - If paths are provided, inspect those files before drafting.
   - If no template is provided, use the default structure below.
   - Do not implement the target task unless the user explicitly asks; this skill writes execution prompts.

2. **Extract execution requirements**
   - Identify phases/stages, dependencies, shared modules, tests, risks, forbidden approaches, acceptance criteria, and expected final report fields.
   - Identify whether phases should be merged because they modify the same files, share validation, or can be safely handled together.
   - Identify which phases should remain separate because they freeze tests, establish foundations, change risky behavior, or perform final validation.

3. **Draft a copy-ready prompt document**
   - Include a brief header for human navigation, but never rely on header/footer instructions for execution-critical rules.
   - Every stage prompt must be self-contained and start with “请…”.
   - Put all mandatory conditions inside every stage prompt body, even if they are repeated.
   - Format every copyable stage prompt as paste-safe text for Codex chat boxes: no leading/trailing blank lines, no leading indentation, and no empty lines inside the prompt body that could split or accidentally submit the message during paste.
   - Preserve structure inside the paste-safe prompt body with explicit sentence markers, inline numbered clauses, semicolon-separated constraints, and clear deliverable lists; do not flatten the prompt into unreadable text just to remove blank lines.
   - Prefer Chinese output when the user writes in Chinese or the source docs are Chinese.

4. **Self-review before finalizing**
   - Verify every prompt block includes source docs, execution rhythm, constraints, subagent rules, validation requirements, deliverables, and stop/next-stage conditions.
   - Verify no important requirement exists only in the intro, recommendation table, or closing notes.
   - Verify each copyable prompt can be selected from its first “请” through the final report sentence as one contiguous paste-safe block without leading indentation or blank-line breaks.
   - Verify merged stages are justified and do not hide risky work.

## Default document structure

Use this structure unless the user gives a stronger template:

```markdown
# <任务名>分批执行提示词模板-<YYYYMMDD>

本文档用于……。复制时只复制对应标题下从“请……”开始的整段内容即可；每段提示词已经内嵌必要通用约束，不依赖本文档开头或结尾说明。
每段可复制提示词正文应保持为从“请……”开始的一整段连续文本：不要在正文前后留空行，不要缩进，不要在正文内部插入空行；正文内部可用“本次……”“具体要求：”“完成后请给出：”和“1）…；2）…”等标记保持结构清晰。

主开发依据：`...`。
背景理解依据：`...`。
明确不采用/注意排除：`...`。

---

## 推荐批次合并说明

| 执行批次 | 覆盖阶段 | 是否合并 | 原因 |
| --- | --- | --- | --- |
| 第一次 | Phase 0 | 不合并 | ... |

---

## 第一次执行提示词：<阶段名>

请……

## 第二次执行提示词：<阶段名>

请……

---

## 每批进入下一批的硬性条件

1. ...
```

## Required content inside every stage prompt

Each stage prompt must repeat the execution-critical constraints below, adapted to the task:

- Treat this paragraph as a complete standalone execution instruction, even if the surrounding document intro or closing notes are not copied.
- Before implementation, first perform read-only planning based on the latest code state, completed prior stages, tests, and source docs.
- State the stage target, code locations, tests, risks, and minimal-change strategy before editing.
- Implement only the current stage or explicitly named merged stages.
- After implementation, perform self-review and run the smallest meaningful validation; iterate on failures.
- Use subagents only for bounded independent tasks such as read-only code mapping, review, failure triage, or test design; release/close them after completion.
- Do not hardcode sample names, IDs, phone numbers, customer examples, or one-off oracle answers.
- Do not add dependencies unless the source docs or user explicitly require them.
- Do not invent facts to pass tests or scores.
- Do not use projection/postprocessing to hide poor raw output quality unless the task explicitly says it is a safety sanitizer.
- Keep diffs small, reviewable, and reversible.
- If source docs and current code disagree, record the discrepancy and choose a minimal safe plan before editing.
- Include exact deliverables for the final report: changed files, key decisions, validation commands/results, known risks, and whether the next stage may start.
- Keep the prompt body paste-safe: one contiguous, non-indented block from the first “请” to the final deliverable sentence, with no internal blank lines, while still using clear inline structure so Codex can parse the task accurately.

## Stage prompt composition checklist

For each stage, include these parts in one continuous copy-ready prompt:

1. **Source and scope**: planning doc paths, background docs, handoff docs, and documents explicitly excluded.
2. **Execution rhythm**: read-only analysis → implementation → self-review → tests/verification → report → next-stage gate.
3. **Stage-specific objectives**: exact phases, modules, functions, scripts, UI areas, migrations, tests, or docs.
4. **Shared constraints**: no sample hardcoding, no invented facts, no unnecessary dependencies, no large unreviewable diff, no masking raw quality with downstream projection.
5. **Subagent policy**: allowed purposes and mandatory release after completion.
6. **Validation**: target commands or how to select target tests, plus what failures are expected vs unexpected.
7. **Final report contract**: require evidence, files changed, tests run, risks, and next-stage readiness.
8. **Paste-safe formatting**: one contiguous stage prompt body starting with “请”, no leading/trailing blank lines, no indentation, no internal blank-line breaks, and enough inline markers to preserve logical structure.

## Batch merge guidance

Recommend merging phases only when all are true:

- They touch mostly the same modules or prompt/config chain.
- They share the same test surface.
- Implementing separately would cause repeated churn or contradictory edits.
- The combined scope still fits one reviewable slice.

Keep phases separate when:

- A phase freezes tests or contracts before implementation.
- A phase builds a foundation required by later phases.
- A phase changes risky production behavior and needs isolated validation.
- A phase is final UI/e2e/release validation.

## Quality bar

A good prompt document is successful when:

- The user can copy only a stage prompt body and the executing agent will still obey all critical conditions.
- The copied stage prompt body is safe to paste into a Codex chat box as one message without accidental blank-line splitting or leading-whitespace issues.
- The executing agent knows what to inspect before editing and what to report after validation.
- The prompt prevents common failure modes specific to the task, not just generic “be careful” language.
- Stage boundaries and merged phases are justified by code/test coupling.
- The document reads like an execution contract, not a summary of the plan.
