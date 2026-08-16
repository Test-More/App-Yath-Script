# AGENTS.md

## MANDATORY: read the universal agent rules first

This project opts into shared agent guidance while keeping its own documents
authoritative for project-specific rules and design.

- Repository: `git@github.com:exodist/Agents.git`
- Default location: `~/projects/Agents`
- This project's location: as declared in `AGENTS_OVERRIDE.md` under "Agents
  repository location", when that section is present.
- Last synced: 287d42e3e6f9054c9d2df6d6884eba0014ce4a83 (2026-08-16)

Use the declared location when there is one, otherwise the default. If no
checkout is there, **stop and ask the user** whether to clone it and where.
Never clone it for them, and never guess a location.

Shared documents spell their paths against the default location. When this
project declares another, read every such path against the declared one.

Check for pending syncs once, at the start of the session:

    git -C <location> log --oneline <last-synced-sha>..HEAD -- \
        AI_AND_LLM_POLICY.md templates/ agent_scripts/ SYNC.md

No output means nothing to apply. Otherwise follow "Staying in sync" in the
shared `AGENTS.md`: show the user what is pending and let them sync now or
skip it and carry on with what they came here to do. Never sync unasked.

Then read `AGENTS.md` in that checkout and follow the shared guidance this
project has adopted. It points at task-specific guides and procedures.

All documents in THIS repository take priority over the shared repository.
Read the project documents named below; `AGENTS_OVERRIDE.md` records
declarations and explicit shared-rule overrides when present.

---

## What this project is

The `yath` script and its bootstrap. `App::Yath::Script` is loaded from
`scripts/yath` during `BEGIN`, works out which harness version to run, and
hands off to an `App::Yath::Script::V{X}` module. The harness itself lives in
Test2-Harness; this distribution is only the entry point.

It exists as a separate distribution so the installed `yath` script stays
stable while harness versions come and go.

CPAN distribution name: `App-Yath-Script`.

---

## Canonical sources of truth

1. **`AGENTS_OVERRIDE.md`** — this project's declarations and overrides.
2. **This file** — project context and conventions.
3. **The POD in `lib/App/Yath/Script.pm`** — the authoritative description of
   version detection and rc-file lookup order.

This project has no `ARCHITECTURE.md`. When the code and the POD disagree
about lookup order or version selection, that is a bug in one of them: stop
and ask which.

---

## Testing

```
~/projects/Agents/bin/agent-test-lock -- prove --timer -Ilib -j16 -r t/
```

The suite is small and finishes well inside the default ceiling.

Nearly every change here touches `@INC`, `require`, or the re-exec path, so
the release-path run described in `~/projects/Agents/TESTING.md` under "The
release path" is routine work in this project rather than an exception:

```
perl Makefile.PL && make && \
  ~/projects/Agents/bin/agent-test-lock --timeout 900 --jobs 16 -- make test
```

Acceptance tests run the real `scripts/yath` in a temporary directory with its
own rc file. They are the only coverage of the `BEGIN`-phase behavior, because
that code has already run by the time a unit test could observe it.

---

## Related repositories

- **Test2-Harness** (`App::Yath`) provides the `App::Yath::Script::V{X}`
  modules this distribution dispatches to. Changing the contract between them
  — the `do_begin` / `do_runtime` interface, `T2_HARNESS_INCLUDES`, or the
  version-selection rules — requires checking Test2-Harness before shipping.

---

## Architecture quick-reference

Foundational rules an agent must internalise before writing any code here:

- **`scripts/yath` deliberately duplicates a little of `App::Yath::Script`.**
  Its `BEGIN` block sets `@INC` up before the module can be loaded, so the
  logic cannot simply be called. When such logic changes, change both, and
  remember that an installed `yath` script must be replaced for the fix to
  take effect — upgrading the module alone is not enough.
- **`V0` is reserved for script validation.** It is never auto-selected and
  must be requested explicitly.
- The script re-execs itself for several reasons (dev-libs, an alternate
  script found on `PATH`). Anything that must survive that crossing goes
  through the environment, which means it must be a string.
