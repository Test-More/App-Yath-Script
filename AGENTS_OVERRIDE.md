# AGENTS_OVERRIDE.md

This project's answers to the choices the universal agent rules deliberately
leave open, plus every deliberate departure from them.

Universal rules live in `~/projects/Agents` (see `AGENTS.md` for the clone
URL). Every project-local document already takes priority over shared rules;
this file keeps declarations and explicit overrides easy to find.

---

## Declarations

### Minimum Perl version

> **Minimum: 5.014000**

Matches the `perl` prerequisite in `dist.ini`. No shipped module or script
carries a higher `use VERSION`.

Reason: `yath` is the first thing a user runs, and it has to start on whatever
perl they already have before it can tell them anything useful. Raising the
floor is a compatibility decision, not a cleanup.

### Subroutine signatures

> **Policy: disabled**

> **Enabling pragma: not applicable**

Argument handling follows the surrounding code using `@_`.

Reason: the 5.014 floor above cannot express them.

### POD placement

> **Layout: all at bottom (default)**

One continuous POD document under `__END__`, in `TEMPLATE.pod` order.

Reason: the shared default, and what every file here already does.

### Test layout and provenance

> **Scheme: `t/AI/` mirror tree**

Human-authored tests in `t/`, AI-generated tests in `t/AI/` mirroring the same
subdirectory layout. No per-file origin header, and `audit-test-layout` is not
run here.

Tests that predate this declaration stay where they are and are not
reclassified or moved. The scheme governs newly authored test files: a new
one goes under `t/AI/`, while a new subtest added to a file already in `t/`
stays with the file it extends. Splitting a subtest away from the harness
setup it depends on would cost a duplicated copy of that setup and buy
nothing.

Reason: chosen by the owner over categories-plus-origin-headers. The existing
suite is four files and rewriting their headers and paths would have churned
every one of them to record something the tree layout can carry instead.

### perltidy

> **Config: shared**

`.perltidyrc` at the project root is a byte-for-byte copy of
`~/projects/Agents/templates/perltidyrc`.

Reason: nothing here needs a local variant.

---

## Overrides

None.

---

## Prior rulings

Recorded in `RULINGS.md`, not here. This file holds declarations and
shared-rule overrides; a ruling is neither. This project has no `RULINGS.md`
yet, which means it has no recorded rulings.
