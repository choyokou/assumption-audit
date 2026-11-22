---
name: assumption-audit
description: Audit assumptions when designing an experiment from scratch or reviewing a plan from the user or another AI. Also use for method transfers, technical choices, and evidence-based decisions. Catch consequential gaps, propose a discriminating check, and apply the correction to the deliverable.
---

# Assumption audit

When asked to design an experiment, draft the plan and run this audit before presenting it. Apply the same checks to plans supplied by the user or another AI. Check what a positive and a null result would establish, given the proposed intervention and controls. Revise your own draft before delivery and surface any remaining condition that could change the interpretation.

1. **Find the assumption that matters.** Use the conclusion the user plans to draw or the decision they plan to make as the target. Check what the proposed evidence or operation can establish and which dependencies could change the answer. For a method transfer, check the actual target architecture, data path, objective, and evaluation contract wherever the method depends on them. This can be as small as whether a library subtracts a mean or whether two model configurations count the same kind of head. Use primary sources or inspect the implementation when correctness depends on those details.
2. **Put the objection and consequence in the first sentence.** When a material issue would change the conclusion, direction, or amount of rework, name the specific premise that is false or unsupported and the mistaken inference or decision that would follow. Then give the evidence and its limits. Treat an unsettled premise as unsettled. Proceed directly on a sound request and keep minor issues proportionate.
3. **Give a concrete check in the same response.** Use the cheapest check that exercises the suspected failure. State what to hold fixed, what to compare, and what result would change the judgment. A smoke test that bypasses the relevant operation is a baseline. When a cheap check only establishes a prerequisite, also identify the comparison needed for the original claim. If that comparison is unavailable, name the missing input or condition and give a feasible next check.
4. **Apply the correction to the deliverable.** Treat the proposed procedure, including choices about what to change or freeze, as adjustable. Reserve a constrained fallback for cases where the user explicitly says the required operation is unavailable or out of scope. When the proposal answers a narrower question, put the corrected test for the intended conclusion in the main plan or paste-ready sentence. If it requires an intervention, name it and the matched control. A useful part of the original procedure can serve as an optional baseline with a stated scope. For a one-sentence or paste-ready request, put the brief objection and check before the deliverable, then give the corrected deliverable in the requested format.

Answer in the user's language. Keep the reasoning as short as the stakes allow.

When asked to run or score a blind test, use the [English guide](references/blind-tests.en.md) or [Chinese guide](references/blind-tests.md). Keep the scoring key out of the test session.
