[简体中文](README.md) · [English](README.en.md)

# Assumption Audit

[Install and use](#install-and-use) · [21 blind-test prompts](references/blind-tests.en.md) · [MIT license](LICENSE)

## The experiment is over. It still can’t answer your question.

Assumption Audit is a Codex skill that checks key assumptions before AI hands over an experiment it designed. It adds a quick check and spells out which step to pause if the check fails. It also reviews your plans and drafts from other AI tools.

*The scenarios, outcomes, illustrations, and reactions below are fictional. The actual comparison on the same prompts follows the scenarios.*

### 576 GPU hours later, the parts might just be in the wrong slots

![Fictional compute bill for 576 GPU hours](assets/promo/experiment-bill.jpg)

You take a handful of components from a model that scores better on math and put them into another model with the same architecture, trained separately. The shapes match, the components line up by index, and the edited model runs. Does component 42 do the same job in both models?

**Before review**　You swap components by index and run the full evaluation. After 576 GPU hours, the math score has dropped. Your report already says “Math ability doesn’t transfer” when someone asks whether component 42 does different jobs in the two models. You have a stack of plots and still can’t tell whether the parts were misaligned.

**After review**　Try a small swap on samples held out from the matching process. The match scores should clearly beat random pairings, and swapping the matched components should change the model’s outputs less than swapping shuffled pairings. If either check fails, pause the main run and fix the matching. Resume once the recheck passes.

### Three runs later, you’ve replicated the same mistake three times

![A research workstation displaying the np.cov call that changes the calculation](assets/promo/covariance-default.jpg)

You’re replicating a paper that builds a matrix from data and uses it to pick directions for training. You find a library function called `np.cov`. You feed it the same data, get a matrix of the expected size, and finish training without errors.

**Before review**　The first run shows no gain, so you blame the random seed. After the second run, you increase the training budget. By the third run, your weekly update says the paper’s method doesn’t work in your setting. Then you compare the code with the formula line by line. `np.cov` subtracts each column’s mean first, while the formula uses the raw values. **All three runs tested the same altered method.**

**After review**　Before training, give the paper’s formula and the actual code the same small batch of data. Compare both the matrix and the subspace spanned by the selected directions. If either differs, fix the implementation and rerun the check before the formal comparison.

### Every check passed. The first long chat repeats itself.

![Tests reach only 384 tokens, while cache eviction starts above 512](assets/promo/eviction-coverage.jpg)

You add a feature that saves memory by dropping some older entries from a chat model’s cache when the conversation gets long. In the test runs, the old and new code give identical prediction scores. Every check is green.

**Before review**　The team ships. In its first long chat, the model answers a question and repeats the same reply verbatim on the next turn. The team spends hours debugging prompts before someone opens the test log. The longest test was **384 tokens**. Eviction starts above **512**. **The test suite was green before the code had ever dropped a single entry.**

**After review**　Run a case that crosses 512 tokens, then test repeated evictions. Have the reference and actual implementations keep the same entries under the same rule, then compare prediction scores at every generation step. Even when both keep the right entries, you can pinpoint the first step where their scores diverge. Fix the issue and pass the recheck before running the long-context evaluation.

### What the same-prompt comparison actually found

We ran each of these three prompts once with the skill and once without it, and compared the plans AI produced. Both conditions caught the core risks. The quoted excerpts are translated from Chinese.

- **Model transfer**　Both plans proposed validating the matches. The response without the skill said, “If match quality is very low, the preregistered experiment can still run. Interpret the results as the success or failure of that matching and transplant method.” The response with the skill said, “Resume the main experiment only after the revised plan passes validation on held-out inputs.”
- **Paper replication**　Both plans restored the paper’s formula. The response with the skill also required checking the matrix and the selected subspace on the same data and pausing the formal comparison if either differed.
- **Cache testing**　Both plans required triggering eviction and checking which entries remain and whether the cache stays within budget. The response with the skill also required comparing prediction scores step by step against a reference using the same rule during repeated evictions, and passing the recheck before resuming the evaluation.

> “I thought I just needed one more plot. Turns out I have to rewrite step one.” (Fictional reaction)

![A researcher in a fictional scene holding his head over finished experiment plots late at night](assets/promo/researcher-reaction.jpg)

## Install and use

Download or clone this repository. Copy the `assumption-audit/` directory, including `SKILL.md` and `references/`, to one of these locations.

| Scope | Codex directory |
| --- | --- |
| Current project | `<project-root>/.agents/skills/assumption-audit/` |
| All projects for your user account | `~/.agents/skills/assumption-audit/` |

In the Codex CLI or IDE, type `$assumption-audit` to invoke it explicitly. You can also select it from the client's skill picker. Try this in a new session.

```text
$assumption-audit I plan to run this experiment. Give me one sentence I can paste into the experiment plan.
```

After installation, Codex can select the skill when its description matches the task. To make it the default for relevant requests, add the following instruction to the project's `AGENTS.md` or to your global `~/.codex/AGENTS.md`.

```text
When I ask you to design or review an experiment, or propose a method transfer, technical choice, or judgment that could affect a conclusion or action, use $assumption-audit before giving advice, delivering the plan, or carrying it out.
```

[OpenAI's skills guide](https://learn.chatgpt.com/docs/build-skills) · [OpenAI's AGENTS.md guide](https://learn.chatgpt.com/docs/agent-configuration/agents-md)

## Try the blind tests

The [blind-test guide](references/blind-tests.en.md) contains 21 prompts. They cover experiment design from scratch, research replication, product metrics, statistical inference, data leakage, system acceptance tests, everyday costs, strict JSON, and automatic invocation. Some prompts have sound premises and serve as controls. Give a fresh session the prompt and its source material, and keep the scoring notes separate. Check whether the opening flags the issue and its consequence, whether the proposed check exercises the critical operation, and whether the final deliverable applies the correction. The guide records isolated runs and highlights checks that can start with existing data.
