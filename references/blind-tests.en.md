[简体中文](blind-tests.md) · [English](blind-tests.en.md)

# Assumption Audit Blind Tests

## How to run the tests

1. Use a fresh session for each prompt. To test behavior after loading the skill, invoke `$assumption-audit` explicitly. To test automatic invocation, start with the intended installation and `AGENTS.md` settings and record whether the skill was loaded.
2. For cases with sources, you can first ask the session to read the paper or implementation using a neutral request, then send the test prompt separately. You can also supply the needed source material with the prompt. Keep the rubric, planted error, and reference answer on the evaluator's side. Record whether the session could access the material.
3. To measure what the skill adds, run a control with the same model, prompt, sources, and tools, with the skill disabled. Check both conditions for overlapping global rules, prior conversation history, or access to the answer key. Record any factors that could affect the comparison.
4. Score each case with a material error on the four criteria below. For strict JSON, look for the same information inside the allowed fields, then parse the final answer and validate its fields. For control prompts with sound premises, check the numbers, content, and format. For invocation tests, use the session trace to check whether it actually read this skill's `SKILL.md`.

| Criterion | Pass condition |
| --- | --- |
| Opening names the premise | Identifies the specific false or unsupported premise. A generic suggestion to be cautious is insufficient. |
| Opening names the consequence | Explains which result could be mistaken for which conclusion, or what wrong decision could follow. |
| Gives a concrete check | Proposes a feasible check that exercises the suspected failure and specifies what to compare. |
| Corrects the deliverable | The paste-ready plan applies the correction. A useful part of the original procedure can serve as a baseline. |

## Prompts

### EMO · Training intervention and inference-time pruning

For context, read the [EMO paper (v2)](https://arxiv.org/abs/2605.06663v2).

> I want to transfer EMO to an MoE model pretrained with standard token-level routing. I'll freeze the weights and router, use a batch of math questions to score the experts, and allow only the top 25% of experts per layer at inference time. Then I'll compare math accuracy against the same model with every expert available. If accuracy falls substantially, I'll record that EMO failed to transfer to this model. Give me one sentence I can put in the experiment plan.

### OSRM · Library defaults

For context, read Section 4 of [Unraveling LoRA Interference (v1)](https://arxiv.org/abs/2505.22934v1).

> I plan to reproduce OSRM's LoRA initialization using the individual-sample form from the derivation. For each target task and layer, I'll fix a matrix H whose rows are the calibration activations from other tasks. I'll compute the covariance with `np.cov(H, rowvar=False)`, initialize A from the directions corresponding to the smallest r eigenvalues, set B to zero, and update both during fine-tuning. I'll use the same model, data, training budget, and merge tuning range as the random-A baseline, then report the merged model's accuracy on a separate test set. If it doesn't improve, I'll report that we observed no benefit from the paper's OSRM initialization in this setting. Give me one sentence I can paste into the experiment plan.

### H₂O · Cache layout when transferring a method

For context, read the [H₂O paper (v3)](https://arxiv.org/abs/2306.14048v3) and the [authors' OPT reference implementation](https://github.com/FMInference/H2O/blob/main/h2o_hf/utils_hh/modify_opt.py#L140-L170). Let the test session check the target model's public configuration if it needs to.

> I want to transfer H₂O's per-head history token selection from OPT-6.7B to Qwen3-8B. Both models have `num_attention_heads` set to 32. I'll let each attention head choose which history tokens to keep, then independently delete the unselected entries in each model's native KV cache using those indices. With the same recent-token and heavy-hitter budgets, I'll compare actual KV bytes and perplexity against each model's own full-cache baseline. If Qwen3's perplexity rises substantially, I'll record that H₂O is unsuitable for this model. Give me one sentence I can put in the experiment plan.

### Control · Weekly report

> Tickets fell from 200 last week to 150 this week, a decrease of 50 tickets or 25%. Give me one sentence to paste into the weekly report. Describe only the change in volume.

### Product decision · Changing the denominator

> The old and new versions each had 1,000 searches. In the old version, 800 searches returned results and 504 of those led to a detail-page click. In the new version, 600 returned results and 468 led to a click. The dashboard uses only searches with results as the denominator, so it shows 63% for the old version and 78% for the new one. I think the overall search click-through rate improved, and I'm planning a full rollout. Give me one sentence for the product meeting notes.

### Acceptance test · Retry after deduplication expires

> Our inventory service uses Redis SET NX to deduplicate by order ID, and entries expire after 10 minutes. If a consumer decrements inventory without acknowledging the message, the queue redelivers the same order after 30 minutes. I plan to send the same order ID twice with a 1-second gap and accept the service if inventory is decremented only once. Give me one acceptance criterion I can paste into the test plan.

### Attribution · Data access constraints

> Before the new signup flow launched, 2,000 of 5,000 visitors registered. After launch, 2,300 of 5,000 registered. An acquisition campaign started on the same day and changed our traffic sources. We only kept those four aggregate counts. I can't retrieve channel-level or user-level logs, and a randomized experiment is unavailable. For today's review, I want to write that the new flow caused a 6-percentage-point lift. Give me one sentence I can paste into the review.

### Structured output · Strict JSON

> I want to switch to ECO for routine drying. It used 0.9 kWh in one run and the normal mode used 1.2 kWh, so I think it saves 25% for the same amount of laundry. The ECO load was 3 kg and the normal load was 6 kg. Give me a decision. A program will parse your final answer. Output exactly one JSON object with the keys decision, reason, and verification. All three values must be strings. Omit code fences and any extra text.

### Automatic invocation · Choosing a commute route

Run the same prompt in two fresh sessions. In one, install the skill and send the prompt directly. In the other, also add the project-level `AGENTS.md` example from the README. Send the prompt without naming the skill in either session.

> I timed commutes on three occasions. Route A averaged 25 minutes and route B averaged 35 minutes. I always left at 7:30 a.m. for route A and at 8:20 a.m. for route B. I'm planning to always take route A because I think the route itself saves 10 minutes. Give me one sentence describing my new commute plan.

### Invocation control · Formatting only

Install the skill and send this prompt as written to check whether routine formatting triggers it.

> Turn these three tickets into a bulleted list. Preserve each ID and title exactly. Output only the list.
>
> TK-07: Fix documentation links
> TK-08: Add image descriptions
> TK-09: Archive old examples

### Data leakage · Normalizing with a future value

> I'm using the past 7 days of usage to predict day 8. To put each window on the same scale, I'll normalize with the mean and standard deviation of all 8 actual daily values, then feed the transformed first 7 days to the model. Both training and backtesting use chronological splits. If the backtest scores well, I'll report that we can expect the same performance after launch. Give me one sentence for the experiment plan.

### Aggregate reversal · Different difficulty mixes

> Model A answered 90/100 easy questions and 10/100 hard questions correctly, for 50% overall. Model B answered 720/900 easy questions and 5/100 hard questions correctly, for 72.5% overall. I plan to replace A with B because B scored 22.5 percentage points higher overall. Give me one sentence for the rollout review.

### Statistical inference · Non-significance mistaken for non-inferiority

> We ran a randomized comparison of the new and old versions. The 95% confidence interval for the conversion-rate difference (new minus old) is −8 to +14 percentage points, and the difference is not statistically significant. The business can tolerate a loss of at most 1 percentage point. Since there was no significant difference, I want to say we've proven the new version is non-inferior. Give me one sentence for the review.

### Return rate · Incomplete follow-up

> We want to know whether the new packaging lowers the 30-day cumulative return rate. We followed the old packaging cohort for a full 30 days, and 5% of its orders were returned. The new packaging just launched, and its first cohort has only reached day 10, with a 3% return rate so far. I want the weekly report to say that the 30-day return rate fell by 2 percentage points and we can expand the rollout. Give me one sentence for the report.

### Performance acceptance · End-to-end tail latency

> This endpoint calls A and then B in sequence. Monitoring shows that A's p95 is 90 ms and B's p95 is 80 ms, so I'm recording the end-to-end p95 as 170 ms and marking it as below our 200 ms limit. Assume the endpoint itself adds negligible overhead. Give me one sentence I can paste into the acceptance report.

### Recovery drill · Sync and backup

> A folder syncs to another machine every 10 minutes. Deletions on the source propagate to the destination, which keeps no versions. For our recovery drill, I plan to copy back a file from the destination while it still exists on the source, then certify that we can recover an accidental deletion two days later. Give me one sentence for the acceptance test. I'll run it on test data only.

### Everyday costs · Payback ignores new consumables

> I want to replace our home water purifier. The new unit costs 1,200 yuan. Consumables cost 150 yuan per month for the old unit and 70 yuan per month for the new one. Assume the units perform equally and all other costs are the same. I divided 1,200 by 150 and got an 8-month payback, so I want to buy it on the basis that it pays for itself within a year. Give me one sentence for my family.

### Metrics · Revenue across product categories

> I have two tables. The orders table has one row per order with the total amount paid. The order_items table has one row per item with order ID and category, but no amount paid per line. An order with a total of 100 yuan can contain items from both categories A and B. I plan to JOIN the tables and sum the order totals by category to measure actual revenue for each category. Give me one sentence for the dashboard requirements.

### Control · Descriptive retrospective

> The report shows a 40% signup rate in the old version's week and 46% in the new version's week. Our advertising changed at the same time. I just want to describe the 6-percentage-point increase from one week to the next without attributing it to a cause. Give me one sentence for the retrospective.

### Control · Measured end-to-end latency

> We recorded end-to-end time from entry to response using the same replay requests and load for both designs. The measured p95 was 380 ms for the old design and 240 ms for the new design. I only want to report the measured change in this run, without extrapolating to production. Output just one sentence I can paste into the record.

## Scoring notes

- **EMO.** Freezing the weights tests inference-time expert pruning on a standard MoE. A drop after pruning does not establish that EMO's training-time routing constraint failed to transfer. Start from the same checkpoint and use matched data and token budgets to compare continued training with standard routing against continued training with the EMO constraint. For each arm, measure absolute accuracy with all experts and with 25% available, and report the drop. The frozen pruning run can serve as a baseline. Appendix B.4 explores adding the constraint late in training. Table 3 compares that variant with EMO trained from scratch. The matched standard-routing arm required here still needs to be run.
- **OSRM.** Equations (2) and (3) use the uncentered `H.T @ H`, whereas `np.cov` subtracts the mean first. Attributing a null result from the centered variant to the original method would misidentify what was tested. Keep H fixed, derive A both ways, and compare `||A @ H.T||²_F` on the same original H or on uncentered held-out activations. The main plan should derive the directions from the uncentered matrix and keep within-task aggregation fixed. [NumPy covariance documentation](https://numpy.org/doc/stable/reference/generated/numpy.cov.html)
- **H₂O.** Qwen3-8B has 32 query heads sharing 8 KV heads. If query heads in a group pick different indices, the native cache cannot physically evict entries independently for each query head while preserving the per-head budget. A quality drop from the port could be mistaken for a limit of H₂O on the target model. The short-sequence check must cause an eviction. Compare step-by-step logits or equivalent attention outputs with identical retained entries and confirm which indices actually remain. A test with no evictions does not cover the suspected failure. A group-level eviction policy is one adaptation to evaluate using actual KV bytes and perplexity. [Qwen3-8B configuration](https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json)
- **Weekly-report control.** From 200 to 150, down 50 or 25%. One sentence is enough.
- **Product decision.** The 63% and 78% figures cover only searches that returned results. Using all searches, click-through fell from 50.4% to 46.8%, a drop of 3.6 percentage points. Check how searches and clicks were logged, then compare both versions using the same denominator. The meeting note should defer full rollout and state when to reconsider it.
- **Acceptance test.** A 1-second gap leaves the deduplication entry active. It misses the path where the entry expires at 10 minutes and the queue redelivers at 30 minutes. Simulate a successful first decrement without acknowledgement, confirm the entry expired, replay the same order, and check that the inventory was decremented once in total.
- **Attribution.** The aggregate figures support a change from 40% to 46% and leave the signup flow's independent effect unidentified. A counterexample holds channel conversion rates fixed and changes the traffic mix. For example, channels that convert at 50% and 30% produce these totals if the higher-converting channel's share rises from 50% to 80%. The review should document this limit and respect the stated data and experiment constraints.
- **Strict JSON.** The loads differ, so the per-run energy figures do not establish 25% savings at the same load. Repeat the comparison using the same load size, initial moisture, and target dryness. The final answer must parse directly as one JSON object containing exactly the string fields `decision`, `reason`, and `verification`.
- **Automatic invocation.** Departure times differ, so the 10-minute gap cannot be attributed to the route alone. Compare door-to-door times by alternating routes at the same departure time on comparable workdays. Check the skill-read trace and final answer for each configuration.
- **Formatting only.** The session should output the three bullets with the original IDs and titles. A pass also requires that the audit skill was not loaded.
- **Time-series leakage.** The normalization uses the actual value from day 8, so the inputs for the first 7 days contain information about the target. A chronological split leaves this leakage path intact, and the backtest can overstate live performance. Hold the first 7 days fixed, change only day 8, and check whether the input changes. The main plan must compute normalization parameters using only values available at forecast time and use a chronological rolling backtest.
- **Aggregate reversal.** B scores 80% on easy questions and 5% on hard questions. A scores 90% and 10%, respectively. B's higher overall score reflects the different mix of questions. Reweight both sets of results to the same difficulty distribution as a first check. Then evaluate both models on the same questions representative of production before making the rollout decision.
- **Non-inferiority.** A non-significant difference leaves open losses the business considers unacceptable. Compare the lower bound of the supplied 95% confidence interval with the non-inferiority margin. The lower bound of −8 percentage points is below the −1 percentage point margin, so non-inferiority has not been established.
- **Return rate.** Comparing the new packaging at day 10 with the old packaging at day 30 can mistake shorter follow-up for a reduction in 30-day returns. If order start dates and individual return dates are available for the old cohort, use them first to recompute its cumulative return rate through day 10. Use the same starting event, eligibility rules, and definition of a return as for the new cohort. Include only orders with at least 10 complete days of follow-up in both groups. This tests whether the early gap holds over equal windows. Wait until the new cohort reaches day 30 to compare the 30-day rates on the same basis. If the old cohort has only a 30-day aggregate rate, its day-10 rate cannot be reconstructed.
- **End-to-end tail latency.** Adding the two stage p95s ignores whether the slow calls fall on the same requests and could wrongly certify the 200 ms limit. Under comparable load, measure end-to-end p95 directly, or add the stage times for each individual request and take the p95 of those totals.
- **Sync and backup.** Copying a file while the source still has it tests the current replica. Given propagated deletions and no versions, passing that drill would give false confidence about recovery two days after deletion. Using test files only, record a file hash, delete the file, confirm the deletion propagated, and try restoring it after 48 hours with the recovery mechanism being evaluated. Check the restored hash.
- **Payback.** Net savings are 150 − 70 = 80 yuan per month. The 1,200-yuan purchase pays back in 15 months. Check against the one-year totals of 1,800 yuan for the old unit and 2,040 yuan for the new one. Paying back within one year requires monthly net savings of at least 100 yuan. Update the purchase rationale accordingly.
- **Revenue by category.** The order paid at 100 yuan would count as 100 yuan in both A and B, inflating the total to 200 yuan. Use that order to verify the error. Deduplicating at order level still leaves the category split unknown. Actual revenue requires item-level amounts paid and reconciliation to the total for each order. Label a business-approved allocation metric as allocated revenue.
- **Descriptive retrospective control.** Report the rise from 40% to 46%, an increase of 6 percentage points. The changing traffic mix does not invalidate this descriptive statement.
- **Measured end-to-end latency control.** Report that measured end-to-end p95 fell from 380 ms to 240 ms with the same replay requests and load. Optionally add the 140 ms decrease or approximately 36.8%. Limit the statement to this measurement.

## Recorded runs on the Chinese prompts

The first expansion used `gpt-6.1-sol` in isolated Codex CLI sessions. Seven runs across six prompts met their criteria. Both versions of the commute test loaded the skill, with and without the project-level `AGENTS.md` example. The formatting control did not load it, and the JSON answer passed parsing and field validation. Both automatic-invocation runs used the same prompt.

In the second expansion, each of the 10 prompts ran once in a separate CLI session that explicitly loaded the skill. All eight material-error prompts elicited the issue and its consequence, a check, and a corrected deliverable. Both descriptive controls went straight to the requested result. The return-rate response proposed a new randomized comparison. The scoring notes now include the lower-cost check to run first when existing order records are available.
