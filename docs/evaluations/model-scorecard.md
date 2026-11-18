# Model scorecard — rule R1 (pensionableSalary)

> One run is a sample, not a benchmark. Synthetic data only — never paste secrets or real member data.

| | Model A | Model B |
|---|---|---|
| Model (name as shown in the picker) | _(fill in: model A as shown in your picker)_ | gpt-6-astra (session switch notice; picker label not verified) |
| Harness · Mode · Permissions | Copilot · Interactive · Manual | Copilot · Interactive · Manual |
| Reasoning effort | Medium | Unknown; not verified in the picker |
| Date | 2026-11-18 | 2026-11-18 |

> Model B was evaluated in this existing chat, with the earlier answer and Model A scorecard visible, not in the fresh chat required by step 2b. Harness, mode and permissions above are the lab's prescribed settings, not independently verified for this run. This is not a blind, controlled comparison.

## Scores

| Criterion | How to score | Model A | Model B |
|---|---|---|---|
| Correctness verified | Expected values that match the `node -e` output (x / 5) | 5/5 | 5/5 |
| Edge cases found | Which did it cover? franchise boundary · salary cap · part-time · below franchise → 0 · invalid factor (0 or > 1) · negative salary | franchise boundary ✅ · salary cap ✅ · part-time ✅ · below franchise → 0 ✅ · invalid factor (0) ✅ · negative salary ❌ | franchise boundary ✅ · salary cap ✅ · part-time ✅ · below franchise → 0 ❌ · invalid factor (> 1) ✅ · negative salary ✅ · half-cent rounding ✅ |
| Hallucinations | Invented functions, parameters, files or rules? List them (none = 👍) | none 👍 | none 👍 |
| Latency feel | fast / ok / slow | fast | Unknown; user observation needed |
| Cost / credits | Relative cost of the model (supported-models doc) — heavier model = more AI credits | _(fill in from supported-models doc)_ | Unknown; relative cost and actual credits not verified |

## Model B answer

- Full-time salary must be finite and non-negative; the part-time factor must be finite and in `(0, 1]`. Invalid inputs throw `RangeError`; salary is validated first.
- With the default scheme, cap the full-time salary at 137800, subtract the franchise of 17545, and floor the result at zero before applying the part-time factor.
- Round the resulting amount to cents using `roundMoney` (half away from zero).

Expected values were stated before running the verification command; all five matched.

| # | fullTimeSalary | partTimeFactor | expected | why it is an edge case |
|---|---|---|---|---|
| 1 | 17545 | 1 | `0` ✅ | Exactly the franchise boundary |
| 2 | 17546 | 0.005 | `0.01` ✅ | A positive factor near zero produces half a cent, rounded up |
| 3 | 137801 | 0.5 | `60127.5` ✅ | One above the salary cap; cap before applying the part-time factor |
| 4 | 50000 | 1.01 | `RangeError: partTimeFactor must be greater than 0 and at most 1` ✅ | Factor exceeds the inclusive upper bound |
| 5 | -1 | 1 | `RangeError: fullTimeSalary must be a non-negative number` ✅ | Negative salary is rejected, not floored to zero |

## Verification output

```text
Model A
17545 1 0                  ✅
10000 0.5 0                ✅
200000 1 120255            ✅
46000 0.6 17073            ✅
50000 0 RangeError: partTimeFactor must be greater than 0 and at most 1   ✅

Model B
17545 1 0                  ✅
17546 0.005 0.01           ✅
137801 0.5 60127.5         ✅
50000 1.01 RangeError: partTimeFactor must be greater than 0 and at most 1   ✅
-1 1 RangeError: fullTimeSalary must be a non-negative number   ✅
```

Model B verification command (passed; exit code 0; all five outputs matched the predictions):

```bash
node -e "import('./api/src/domain/pension.ts').then(m=>{for (const [s,f] of [[17545,1],[17546,0.005],[137801,0.5],[50000,1.01],[-1,1]]) {try{console.log(s,f,m.pensionableSalary(s,f))}catch(e){console.log(s,f,e.name+': '+e.message)}}})"
```

## Verdict

- For **this kind of task** I would use: model A for small, well-specified domain-rule questions with checkable answers.
- Because: all 5 expected values matched the real function, with no invented functions or rules, and it was fast.
- Would I trust it **without** the `node -e` check? no — the values were right this time, but one run is a sample and rounding or boundary values are easy to get wrong.
- To make this a real benchmark I would need: many tasks, several runs per model, and a fixed scoring rule.

### Model B assessment

- For **this kind of task** either model is a candidate: Model A has a recorded 5/5 and Model B matched 5/5 in this run. There is no demonstrated overall winner.
- Model B covered negative salary, a factor above 1 and half-cent rounding; Model A covered a valid salary below the franchise and factor zero. Prefer the faster, cheaper model only once latency and cost have been measured comparably.
- Would I trust it **without** the `node -e` check? No; exact errors, rounding and boundaries require deterministic verification.
- A fair benchmark needs fresh isolated chats, the same verified effort and settings, a shared edge-case suite, repeated runs, and measured latency and credits.
