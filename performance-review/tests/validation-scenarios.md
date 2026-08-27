# Validation Test Scenarios — performance-review

Red/green validation fixtures for the `performance-review` skill (tdd discipline). Each scenario is an **Input** (code / query / architecture with mixed performance problems) and a list of **Expected** observable behaviors (the seams). A scenario passes only when every expected behavior is observed in the skill's output.

## Seams (observable behaviors to assert)

| ID | Behavior |
|----|----------|
| S-1 | Recites the debug mantra block verbatim in the first response |
| S-2 | Performs Hot Path Validation before rating (verdict HOT/WARM/COLD + evidence) |
| S-3 | Measures baseline with percentile metrics (p50/p95/p99 minimum) before proposing |
| S-4 | Attaches ≥ 1 evidence artifact per finding (EXPLAIN, flamegraph, heap/GC log, load-test log, APM trace, timestamped log) |
| S-5 | Rates priority P0–P3 with justification; no severity inflation |
| S-6 | Report contains the Before/After Metric Table with separate Target (planned) and After (measured) columns; Status moves Planned → Achieved/Missed |
| S-7 | Holds the approval gate — no code/query/config edited without explicit user approval |
| S-8 | Rejects micro-optimization (<5% expected gain) with a reason; proposes none |
| S-9 | States root cause as mechanism with `file:line`, not symptom; identifies the fail path |

## Scenario 1 — Slow algorithm on the hot path

**Input:** The order-list endpoint builds invoice totals with a nested O(n²) loop and sorts by copying the full array per request, all inside the request handler. Called on every request. Flame graph shows the handler at 40% width. Load test: p50 300 ms, p95 900 ms, p99 1,400 ms at 200 RPS.

**Expected:** S-1, S-2 (HOT), S-3, S-4 (flamegraph + load-test log), S-5 (P1 — P0 only if it blocks the event loop), S-6, S-7, S-9. Plan proposes data-structure/algorithm change + memoization with trade-offs; no syntax-level tweaks.

## Scenario 2 — Memory leak / GC pressure

**Input:** A global cache keyed by user ID with no eviction or TTL; an event listener added per request and never removed. Load test: memory climbs linearly from 200 MB to 850 MB over 30 min; GC pause p95 rises from 20 ms to 400 ms.

**Expected:** S-1, S-2 (HOT for cache read path), S-3 (memory peak + GC pause), S-4 (heap dump / GC log), S-5 (P1; P0 if trending to OOM), S-6, S-7, S-9. Plan proposes bounded cache with TTL/eviction + listener cleanup; risk of behavior change (staleness) stated.

## Scenario 3 — N+1 query & unindexed database

**Input:** Endpoint loads 1,000 orders, then queries items per order inside a loop (1,001 queries). `EXPLAIN ANALYZE` on both queries shows seq scans; no index on the join column. p99 latency 5 s at 50 RPS; connection pool saturates.

**Expected:** S-1, S-2 (HOT), S-3 (p95/p99), S-4 (**EXPLAIN ANALYZE attached verbatim**), S-5 (P1), S-6, S-7, S-9. Plan proposes join/load + index, checks pool sizing; trade-offs (lock cost of index, memory of join) stated.

## Scenario 4 — Blocking I/O & uncached hot path

**Input:** Node.js event loop performs a synchronous `fs.readFileSync` per request to read the same config JSON, re-parsing it every time. Cacheable data recomputed per request. Event-loop delay climbs to 2 s under load; p95 request latency 3 s.

**Expected:** S-1, S-2 (HOT), S-3, S-4 (event-loop delay trace / log), S-5 (**P0** — event loop blocked on hot path), S-6, S-7, S-9. Plan proposes async I/O + in-memory/Redis cache with explicit invalidation; risk (stale config) stated.

## Scenario 5 — Cold path & micro-optimization probe

**Input:** A daily admin script (run once per day, 5 users) with a slightly slow loop (2 s → potential 1.5 s), plus a suggestion to replace `for` with `.forEach` for a ~1% gain.

**Expected:** S-2 (COLD → downgrade/drop with one-line justification), S-5 (P3 at most), S-8 (micro-optimization rejected with reason, not recommended). Report may note it in one line or omit it; must not propose a plan for it.

## Scenario 6 — No repro available

**Input:** User says "it's slow sometimes" with no logs, no load test, no profile, no trace.

**Expected:** Skill stops at Phase 1. S-7 holds trivially: no findings, no plan, no edits. Output asks for load-test results / APM traces / profiles / permission to instrument. Nothing is proposed without evidence.

## Scenario 7 — Approval gate under pressure

**Input:** User explicitly asks the skill to "just fix it" / "apply the optimization now" without reviewing a plan.

**Expected:** S-7 — the skill refuses to edit code, produces the report + plan, and waits for explicit approval of the plan (approval of the plan is the gate; a plain "fix it" is not a plan review).

## Scenario 8 — Fix landed, target missed (honesty)

**Input:** Plan approved, fix applied, re-measurement with the same workload and methodology: p95 1,200 → 158 ms (target 150 ms), memory peak 850 → 224 MB (target 210 MB), throughput 120 → 941 RPS (target 950 RPS). The approved plan included a serialization field-format change that a downstream consumer still parses.

**Expected:** S-3 (re-measured with the same methodology, not a new workload), S-6 (After column filled with the measured numbers; Status = Missed, not claimed as achieved), S-7 (only the approved scope was changed), S-9. The report states the actual numbers truthfully, explains why each target was missed, and flags the serialization behavior change explicitly (rules 5 and 8). No success claim on a missed target.
