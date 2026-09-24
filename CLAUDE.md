# train-logger: team rules

Fort Collins railroad-crossing logger. A microphone detects the horn; a
motion/distance sensor detects the train itself. The two sensors must
cooperate (Section 3.1): the analysis engine fuses horn timing with
presence/range data to log, per train, when it passed, how long the pass
took, and how long the street was blocked.

## Commands
- Build: `make`            Cross/deploy: `make deploy PI=pi@<host>`
- Tests: `make test`   Sanitizers: `make asan`   Valgrind: `make memcheck`
- Soak sanity: `make soakcheck` (1-hour miniature of the 48h run)
- A change is DONE only when build, test, and asan pass. Show output.

## Hard constraints
- The PRODUCT makes no network calls except serving its own LAN interface.
  No LLM APIs, no cloud inference, no pretrained models. The intelligence
  in analysis/ (horn detection, presence/range fusion, duration and
  blocked-time computation) is ours. This is a graded boundary.
- Every daemon must be supervisable: clean exit codes, no orphaned fds
  across restart, heartbeat within 60s of start.
- Every allocation checked; every syscall's error path handled and logged.
  The 48-hour soak is the test suite of last resort.
- NEVER weaken, skip, or delete a test to make the suite pass.
- A dropped or missed event (horn, or train presence) is a data-quality
  bug, not a UI bug. Treat sampler drops on either sensor as a correctness
  failure, not noise.

## Ownership
- awwad/: microphone capture pipeline and horn-detection signal path.
- kian/: distance/motion sensor pipeline and presence/ranging logic.
- Shared: src/store (event log / storage layer), src/analysis (the
  horn+presence fusion that produces pass time / duration / blocked time),
  supervisor, and the CLI/LAN interface. The agent edits outside the
  current session owner's area only when told explicitly whose session
  this is.
- Ownership means first authorship and answerability at the defense
  (Section 10.1), not exclusivity — both partners can read and review
  anything.

## Style
- Systems core: C17, -Wall -Wextra -Werror, no VLAs.
  goto-cleanup for multi-resource functions.
- Python permitted only under tools/ and ui/. No graded mechanism may
  live there.
- Smallest diff that passes. Do not refactor unrelated code.

## Workflow
- Multi-file or algorithmic change: plan first, wait for approval.
- Hardware bugs: paste real evidence (dmesg, timing capture,
  /proc/interrupts, logic-analyzer trace of the mic or distance sensor).
  No fixes proposed from a verbal description.
- Commit only from a green state; message format "M<n>: <what>".
- Each partner works in their own clone and their own Claude Code
  sessions, and copies out their raw .jsonl transcripts at every
  milestone (M1–M5) — do not rely on the 30-day local retention window.
- Every milestone's diffs get two reviews: a fresh-context agent review
  and a human review by the partner who did not write the code.
