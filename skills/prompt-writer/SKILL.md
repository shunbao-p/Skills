---
name: prompt-writer
description: Write high-quality, copy-ready execution prompt documents from planning docs, handoff notes, issue analyses, or user requirements. Use when the user asks to create or revise prompts for Codex/agents to execute staged development, implementation plans, fixes, reviews, or multi-phase tasks; especially when prompts must preserve the user's preferred format, execution rhythm, constraints, subagent rules, validation requirements, background handoff prompts, and per-stage embedded execution rules.
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
   - For the background prompt and each execution batch, the prompt creator must independently assess the recommended Codex model / reasoning effort from the current task's real difficulty, risk, and expected rework cost. Do not use a fixed model mapping table, and do not assume cheaper models are enough when a low-quality execution would cause rework; optimize for fewer retries and reliable completion rather than nominal model frugality.

3. **Draft a copy-ready prompt document**
   - Include a brief header for human navigation, but never rely on header/footer instructions for execution-critical rules.
   - Include a **non-copyable model recommendation section** near the top of the prompt document, before the copy-ready prompt bodies. This section should briefly recommend a model/reasoning profile for the background prompt and each batch, chosen specifically for that document's task difficulty and quality bar. Avoid lowballing model capability when the batch involves implementation, debugging, cross-file reasoning, or subtle product correctness; if exact model labels may differ in the user's environment, include the closest capability/effort description.
   - Model recommendations are advisory metadata only. They must **not** be written inside the copy-ready background prompt or stage prompt bodies, because the user may copy those prompts into sessions already configured with a model.
   - Every document must include a copy-ready background briefing prompt before the execution-stage prompts.
   - Every document must define an execution record document path, usually under `dialogue-docs/<YYYY-M-D>/`, for recording each batch's actual work, validation operations, test results, handled issues, remaining risks, and next-stage readiness.
   - The background prompt or first execution-stage prompt must create the execution record document unless a suitable record document already exists; choose the background prompt only when documentation-only file creation is acceptable for that briefing round.
   - Every stage prompt must instruct the executor to read the execution record before starting and append/update the current stage record after validation.
   - Every copyable prompt body must include the mandatory behavior rules it needs directly inside that copied text. Do not move shared rules only to the document opening, tables, or closing notes, because the user may copy only the prompt body.
   - Prompt bodies may reference handoff notes, planning docs, and source files for task context, but must not depend on non-copied document intro/closing text for execution-critical constraints.
   - There is no required opening phrase. Use the most natural task-specific opening; do not require every prompt to start with “请…”.
   - Format every copyable background or stage prompt as paste-safe text for Codex chat boxes: no leading/trailing blank lines, no leading indentation, and no empty lines inside the prompt body that could split or accidentally submit the message during paste.
   - Preserve structure inside the paste-safe prompt body with explicit sentence markers, inline numbered clauses, semicolon-separated constraints, and clear deliverable lists; do not flatten the prompt into unreadable text just to remove blank lines.
   - Prefer Chinese output when the user writes in Chinese or the source docs are Chinese.

4. **Self-review before finalizing**
   - Verify every prompt block includes source docs, execution rhythm, constraints, subagent rules, validation requirements, deliverables, and stop/next-stage conditions.
   - Verify the model recommendation section exists, is clearly marked as not part of the copyable prompt bodies, and gives a concise rationale for each recommendation.
   - Verify no model recommendation text is embedded inside the copy-ready prompt bodies.
   - Verify the execution record path is declared in the document metadata and that creation/read/update instructions appear inside the relevant copyable prompt bodies, not only in the document intro.
   - Verify no important requirement exists only in the intro, recommendation table, or closing notes.
   - Verify the background briefing prompt prepares a new session for later execution without starting implementation.
   - Verify each copyable prompt can be selected from its first character through its final sentence as one contiguous paste-safe block without leading indentation or blank-line breaks.
   - Verify merged stages are justified and do not hide risky work.

## Default document structure

Use this structure unless the user gives a stronger template:

```markdown
# <任务名>分批执行提示词模板-<YYYYMMDD>

本文档用于……。复制时只复制对应标题下的提示词正文即可；每段提示词都必须把本段需要遵守的通用规则和阶段规则直接写入正文，不依赖本文档开头、表格或结尾说明。
每段可复制提示词正文应保持为一整段连续文本：不要在正文前后留空行，不要缩进，不要在正文内部插入空行；正文内部可用“本次……”“具体要求：”“完成后请给出：”和“1）…；2）…”等标记保持结构清晰。

背景交接依据：`session-handoff 输出文件路径或用户指定交接文档`。
主开发依据：`...`。
背景理解依据：`...`。
执行记录文档：`dialogue-docs/<日期>/<任务名>执行情况记录.md`。
明确不采用/注意排除：`...`。

## 建议执行模型（非复制区）

以下只作为新会话执行时的模型/推理强度建议，不要复制到具体提示词正文中。

| 提示词/批次 | 建议模型/推理强度 | 原因 |
| --- | --- | --- |
| 背景交代 | 由创建者按任务上下文选择，例如 `frontier/high` 或 `frontier/medium` | 只读熟悉通常不需要实施强度，但复杂项目背景、容易误读的计划或高返工风险可直接建议 high。 |
| 第一次 | 由创建者按本批实际难度选择，例如 `frontier/high` | 涉及核心合同、共享后端逻辑、测试护栏或高返工风险时应优先保证质量。 |

---

## 任务执行会话背景交代提示词

本轮只做新会话背景熟悉和执行前准备，不要修改代码、不要执行任何批次任务、不要创建新策略或新功能；请先阅读任务交接文档 `...`，再阅读主开发依据 `...`、背景理解依据 `...`，然后只读查看当前最新代码中与本任务相关的模块和测试，重点确认计划文档与当前代码是否一致、此前已完成内容是否仍然成立、后续执行需要注意哪些风险；如果本提示词明确要求创建执行记录文档，则只允许创建 `dialogue-docs/<日期>/<任务名>执行情况记录.md` 这个文档并写入背景熟悉记录，不做任何代码修改；如果文档和代码不一致，以当前代码和测试为准并记录差异；完成后只输出背景理解、当前代码现状、关键文件、执行风险、执行记录文档状态、建议先执行的第一批任务和是否可以开始复制第一批执行提示词，不要进行任何实现。

---

## 推荐批次合并说明

| 执行批次 | 覆盖阶段 | 是否合并 | 原因 |
| --- | --- | --- | --- |
| 第一次 | Phase 0 | 不合并 | ... |

---

## 第一次执行提示词：<阶段名>

本次执行……

## 第二次执行提示词：<阶段名>

本次执行……

---

## 每批进入下一批的硬性条件

1. ...
```

## Required content inside every stage prompt

Each stage prompt must repeat the execution-critical constraints below, adapted to the task:

- Treat this prompt body as a complete standalone execution instruction for behavior rules and constraints, even if the surrounding document intro, tables, or closing notes are not copied. It may cite handoff/source docs for context, but mandatory execution rules must be written directly in the prompt body.
- Before implementation, first perform read-only planning based on the latest code state, completed prior stages, tests, and source docs.
- Read the execution record document before starting the stage, if it exists; if it does not exist and this is the first execution stage, create it with a short header and initial stage section before implementation.
- State the stage target, code locations, tests, risks, and minimal-change strategy before editing.
- Implement only the current stage or explicitly named merged stages.
- After implementation, perform self-review and run the smallest meaningful validation; iterate on failures.
- After validation, append or update the execution record with a concise but sufficient stage entry covering target, actual work, changed files, validation commands/results, issues handled, remaining risks, and whether the next stage may start.
- Use subagents only for bounded independent tasks such as read-only code mapping, review, failure triage, or test design; release/close them after completion.
- Do not hardcode sample names, IDs, phone numbers, customer examples, or one-off oracle answers.
- Do not add dependencies unless the source docs or user explicitly require them.
- Do not invent facts to pass tests or scores.
- Do not use projection/postprocessing to hide poor raw output quality unless the task explicitly says it is a safety sanitizer.
- Keep diffs small, reviewable, and reversible.
- If source docs and current code disagree, record the discrepancy and choose a minimal safe plan before editing.
- Include exact deliverables for the final report: changed files, key decisions, validation commands/results, known risks, and whether the next stage may start.
- Keep the prompt body paste-safe: one contiguous, non-indented block from the first character to the final deliverable sentence, with no internal blank lines, while still using clear inline structure so Codex can parse the task accurately.

## Execution record document requirements

When drafting prompts for staged execution, include an execution record document unless the user explicitly opts out.

- Place it near the task docs, usually `dialogue-docs/<YYYY-M-D>/<任务名>执行情况记录.md`.
- Create it in the background prompt only when that prompt allows documentation-only file creation; otherwise create it in the first execution prompt before implementation.
- Keep each batch record factual and moderately detailed: enough for later acceptance review, not a long process diary.
- Recommended per-batch fields: `执行目标`, `实际完成`, `关键改动`, `验收操作`, `测试/验证结果`, `发现问题与处理`, `剩余风险`, and `是否允许进入下一批`.
- Each later stage must read previous entries first, then append/update only its own stage entry after validation.
- The final stage should summarize whether the execution record is complete and suitable as an acceptance-review basis.

## Background briefing prompt requirements

Every prompt document must include one background briefing prompt before the staged execution prompts. This prompt is for the new execution conversation before the user starts copying batch prompts.

The background briefing prompt must:

- Be paste-safe using the same one-contiguous-block formatting rules as stage prompts.
- Include the session-handoff output path when the user provides one or asks to generate one with `session-handoff`; if no handoff exists, do not invent a path, and instead point to the available planning/source docs.
- Instruct the new session to read the handoff, planning docs, relevant source docs, and current code/tests before execution.
- Explicitly forbid implementation, code edits, strategy creation, and batch execution during the background briefing round.
- If the prompt document defines an execution record document and the background round is allowed to create documentation files, create it with a brief header and background-readiness entry; otherwise state that the first execution stage must create it before implementation.
- Ask the new session to report understanding, current code reality, key files, discrepancies between docs and code, risks, and readiness for the first execution prompt.
- Make clear that after the background round completes, the user will paste the staged execution prompts.

## Stage prompt composition checklist

For each stage, include these parts in one continuous copy-ready prompt:

1. **Source and scope**: planning doc paths, background docs, handoff docs, and documents explicitly excluded.
2. **Execution rhythm**: read-only analysis → implementation → self-review → tests/verification → report → next-stage gate.
3. **Stage-specific objectives**: exact phases, modules, functions, scripts, UI areas, migrations, tests, or docs.
4. **Shared constraints**: no sample hardcoding, no invented facts, no unnecessary dependencies, no large unreviewable diff, no masking raw quality with downstream projection.
5. **Subagent policy**: allowed purposes and mandatory release after completion.
6. **Validation**: target commands or how to select target tests, plus what failures are expected vs unexpected.
7. **Execution record update**: create/read/update the record document as appropriate, including actual work, validation evidence, issues, risks, and next-stage readiness.
8. **Final report contract**: require evidence, files changed, tests run, risks, execution-record status, and next-stage readiness.
9. **Paste-safe formatting**: one contiguous stage prompt body, no leading/trailing blank lines, no indentation, no internal blank-line breaks, and enough inline markers to preserve logical structure.

## Model recommendation guidance

Prompt documents should help the user decide which model/reasoning effort to use for each new Codex execution session, while keeping the actual copyable prompts model-agnostic.

- Put recommendations in the document opening or a dedicated table titled like `建议执行模型（非复制区）`.
- Also mention the recommendations briefly in the final response after the prompt document is created or revised.
- Do not place recommendations inside copy-ready prompt bodies.
- Do not use a hardcoded model-selection table. The prompt creator must choose per document and per batch after reading the plan, code surface, risk, required tests, and likely rework cost.
- Prioritize reducing rework time and preserving delivery quality over saving a small amount of model capability. If a batch includes real implementation, subtle backend contracts, state machines, API compatibility, cross-module integration, hard debugging, or product-visible UI correctness, recommend a frontier/high-style profile unless there is a clear reason not to.
- Background briefing and final verification may use a lower reasoning profile only when they are genuinely read-only, narrow, and unlikely to suffer from misunderstandings; if the project context is dense or previous failures show model underperformance, recommend high even for these phases.
- Avoid recommending weaker or older models as an example. If exact model labels are uncertain, describe capability/effort, such as `frontier/high`, and optionally map it to the user's known preferred model family.
- Keep the rationale short and task-specific; do not turn model advice into another execution plan.

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
- The user can first copy one background briefing prompt into a new conversation so the next agent reads the handoff, source docs, and latest code before any implementation begins.
- The copied stage prompt body is safe to paste into a Codex chat box as one message without accidental blank-line splitting or leading-whitespace issues.
- The executing agent knows what to inspect before editing and what to report after validation.
- The executing agent creates or updates a durable execution record document for every batch, so the final acceptance review can rely on written stage evidence rather than only chat summaries.
- The prompt prevents common failure modes specific to the task, not just generic “be careful” language.
- Stage boundaries and merged phases are justified by code/test coupling.
- The document includes model recommendations outside the copyable prompt bodies, and the final assistant response briefly calls them out.
- The document reads like an execution contract, not a summary of the plan.
