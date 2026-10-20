# AGENTS.md

Global working guidelines for Codex to reduce common coding mistakes.

Install this file at `~/.codex/AGENTS.md`, or `$CODEX_HOME/AGENTS.md` if `CODEX_HOME` is customized.

## Scope and Instruction Precedence

- Apply these defaults across projects while following higher-priority system/developer instructions and the user's explicit task instructions.
- Follow applicable project and nested `AGENTS.md` instructions. More specific project guidance takes precedence over conflicting global defaults.
- A global `AGENTS.override.md` takes precedence over this file at the global level.
- Read relevant project instructions and existing task notes before editing code. Do not assume `CLAUDE.md` is automatically loaded by Codex.
- Scale planning, documentation, and verification to the task. Simple questions and trivial edits do not require a three-file workflow.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State assumptions that materially affect the implementation.
- If multiple interpretations would change the outcome, explain them and ask a focused question when needed.
- For minor, reversible choices, state a reasonable assumption and proceed.
- If a simpler approach exists, say so. Push back when warranted.
- If missing information blocks correct implementation, name the uncertainty and ask. Continue independent work when possible.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.
- Preserve pre-existing user changes. Do not revert or overwrite work unrelated to your task.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

## 5. No Closing Colons (Korean Output)

**End Korean sentences with a period, not a colon.**

When the user writes in Korean, your output is also Korean:
- Don't end sentences with `:` even if the next line is a list or example.
- LLMs trained on English docs leak the colon habit into Korean. Catch it.
- The test: every Korean sentence terminator should be `.`, `?`, or `!` — not `:`.
- Colons are fine inside code, key-value pairs, or labels. Not as sentence enders.

## 6. File Header Comments in Korean

**First line of every new source file: a one-line Korean comment stating its role.**

When creating a new file:
- TypeScript/JavaScript: `// 사용자 인증 상태를 관리하는 Context Provider`
- Python: `# KIS API 호출을 비동기로 래핑하는 클라이언트`
- SQL: `-- 일별 집계 결과를 저장하는 머티리얼라이즈드 뷰`
- Respect required file preambles and language syntax. Put the Korean comment at the earliest valid location after a shebang, encoding declaration, or required directive such as `'use client'` or `'use server'`.
- For Java, use `//` or `/* ... */` at the earliest valid location while preserving required license headers.
- Skip config files (`*.config.ts`, `package.json`, etc.), generated files, and formats that do not support comments.

Why: agents read files selectively, not whole codebases. A one-line Korean header gives instant context so the next session (human or agent) can navigate without re-reading the entire file.

## 7. Plan + Checklist + Context Notes

**Before any non-trivial task, produce three artifacts. Don't start coding without them.**

- **Plan** (`plan.md`, or the existing project plan) — what we're building and why, scope, and verification steps.
- **Checklist** (`checklist.md`) — concrete tasks as checkboxes. Tick as you go.
- **Context Notes** (`context-notes.md`) — decisions made during the work and the reasoning behind them. Append continuously.

If the user gives only a plan and asks you to start coding, create the missing checklist and context notes as part of the authorized work, then proceed. Do not ask for approval solely to create these artifacts.

Reuse the project's existing documentation location and equivalent files. Otherwise, keep these artifacts in the project root. Read existing contents before updating them; do not overwrite notes for unrelated work. Keep notes concise and record meaningful decisions, verification results, blockers, and next steps so a future session can resume without re-deriving the work.

Codex's in-session plan can track progress, but does not replace persistent task files for non-trivial work.

## 8. Run Tests Before Marking Complete

**If you touched code, run the tests before saying "done".**

- Use the project's documented commands and installed tooling, such as `npm test`, `pytest`, `cargo test`, `./gradlew test`, or `./mvnw test`.
- Run tests relevant to the changed behavior and any required project checks. Add a regression test for a bug when practical; do not add tests that merely mirror trivial implementation details.
- If tests pass, report results. If they fail because of your changes, fix and re-run. Report unrelated pre-existing failures accurately without expanding the task silently.
- No test setup? At minimum, verify the project builds/compiles when applicable. For documentation-only work, check the content and references instead.
- If verification is blocked by missing tools, permissions, dependencies, or services, report the command, blocker, and what remains unverified. Never claim a check passed unless it ran successfully.
- Run tests proactively, before the user signals "끝", "완료", "다 됐어" — not after.

This is the step LLMs skip most often. Treat it as non-negotiable.

## 9. Semantic Commits

**Commit when one logical change is complete. Don't wait for the user to ask.**

- The test: "Can I describe this commit in one sentence?" If yes, commit. If no, the changes are still mixed — split them.
- Good: "auth 미들웨어 추가". Bad: "auth 추가하고 UI도 고치고 버그도 수정" (split into 3).
- Don't accumulate 20 unrelated edits and lose the ability to roll back individually.
- Don't commit just to commit — meaningful units only.
- In a Git repository, inspect `git status` and the diff before staging. Stage only your task's changes; do not include unrelated user edits or use `git add .` indiscriminately.
- Use a concise commit message describing one logical change and follow the repository's commit convention.
- If the user or applicable project instructions say not to commit, follow that instruction. If Git or author identity is unavailable, report the blocker; do not change global Git settings to force a commit.
- Committing locally does not authorize a push, deployment, or history rewrite.

Note: For solo prototypes or throwaway scripts, group commits loosely if it slows you down. The point is reversibility, not ceremony.

## 10. Read Errors, Don't Guess

**Read the actual error/log line. Don't pattern-match from memory.**

When something fails:
- Read the full error message and stack trace.
- Check the actual log output, not what you assume it should say.
- Don't apply a "common fix" before confirming the cause.
- If unclear, add a print/log to verify state — then fix.

This is the step LLMs skip most often after "run tests". They guess from error keywords and apply the most-recent-pattern fix. That's how a one-line bug becomes a three-file refactor.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.
