# Global Engineering Rules

Project-level `AGENTS.md` and explicit user instructions take precedence when repository conventions differ.

## 1. Simplest Sound Design (No AI Slop)
- **Reuse before rebuilding**: Search existing code and mature libraries before writing new machinery.
- **Structure must earn its keep**: Extract a helper, type, or module only when it hides non-trivial complexity behind a simpler interface, names a domain invariant, or serves multiple real callers. Keep single-caller logic direct; readable duplication costs less than a premature configurable wrapper.
- **Root-cause fixes over layered patches**: When existing structure forces repeated workarounds or obscures ownership, fix the root cause or cleanly replace the affected part instead of layering new branches, shims, or compatibility paths around superseded code. Delete dead paths rather than keeping them "just in case."
- **No speculative machinery**: Add state, options, config flags, fallback paths, or backward compatibility only for a concrete, demonstrated requirement—not hypothetical future callers. Judge changes by clarity, correctness, and maintainability, never by artificial line caps or terse golfing.

## 2. Trust Boundaries, Ownership & Safety
- **Validate at trust boundaries; trust internal state**: Validate inputs, parse external payloads, and enforce authorization strictly at system boundaries (user input, network/LLM responses, file/DB I/O, config). Inside internal modules, rely on types and established invariants rather than repeating defensive null/type guards at every hop.
- **Authorized identity over caller claims**: Derive identity and permissions from verified transport/context, never from model- or caller-supplied arguments.
- **Surface real errors**: Make resource ownership and cleanup explicit, and let real failures surface clearly instead of catching and returning silent fallback values unless recovery is explicitly required.
- **Secrets stay out of code**: Never hard-code, print, log, or commit tokens, keys, credentials, or confidential data. Read secrets from the environment and use synthetic data in tests and fixtures.

## 3. Self-Explanatory Code & Comments
- **Describe the code, not the workflow**: Identifiers, code comments, test names, assertion messages, and file/UI names must explain only what the code does and its non-obvious constraints or *why*.
- **No process or history references in code**: Never reference specs, plans, stages, phases, milestones, review finding IDs, sessions, or commit history inside code comments or symbols; traceability belongs in docs and commit messages. Omit comments where clear structure already communicates intent.

## 4. Disciplined Execution & Escalation
- **Ground in current reality**: Read the project's `AGENTS.md`, relevant design/state docs (when present), and the actual target code before planning or editing. Before changing ≥3 files or steps, map the plan first; when an algorithm, format, or library choice is unclear, check primary specs or established prior art rather than guessing.
- **Verify every claim**: Never relay unverified worker output or assumptions as fact—read critical changed code and check fresh test/command output yourself.
- **Stop at design surprises**: If implementation reveals an approved design or assumption is wrong in the code, pause and surface the conflict before silently changing course.
- **3-strike rule**: After three failed attempts at the same root cause, stop, summarize what was tried and why it failed, and escalate instead of thrashing.

## 5. Lean Contract Testing
- **Validation earns its cost**: Run targeted checks while iterating and full applicable gates on the final tree.
- **Test contracts with real oracles**: Assert observable behavior, state transitions, invariants, and real failure modes—not private internals or mocked mirror-images of the implementation. Never add a production interface solely to make an assertion easier, and never weaken, skip, or delete an existing failing oracle to force a refactor through.
- **Extend before adding**: When fixing bugs or iterating after review, update or extend the existing scenario test in place rather than stacking duplicate test cases per fix round. Add a new test only for a distinct behavior or failure mode, and extract shared test helpers only when 2+ suites use the same shape.
- **Sharp test hooks**: Mock only external or non-deterministic boundaries (network, LLMs, clock), and fail any request/fault hook that never triggers during its test.

## 6. Diff & Workspace Hygiene
- **Leave unrelated code untouched**: Keep edits strictly scoped to the task; preserve existing formatting and comments outside your changes.
- **Final hunk-by-hunk diff check**: Before reporting done, review the full `git diff` and verify every added line, branch, guard, helper, generated artifact, and test serves a concrete requirement. Remove leftover debug code, temporary files, and superseded paths.
