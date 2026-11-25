---
name: glassray-calibrate-evaluator
description: Start or resume Glassray evaluator calibration. Use to check a draft's scope, work through open rule questions, run the final pass tests, or explain Glassray coverage and consistency for a calibration.
---

# Calibrate a Glassray evaluator

Calibration challenges the rule, records the user's decisions about ambiguous cases, and checks repeatability. Use an experiment with independently labelled traces when the goal is measuring accuracy, recall or specificity.

Keep evaluator working files uncommitted. If the runner needs a model API key, read it from the environment.

Use plain language in every message to the user, from the first update to the final report. Start with what happened, what you found, or the decision they need to make. Use their words for the work and explain any necessary Glassray term the first time it matters. Keep tool details in the background unless they help the user act. Keep quotes, counts, prices and IDs exact. If the user asks for a simpler explanation, say the same thing in simpler words.

For example, say “I checked 50 new reports. Five raised the same question about what should count. I’ll show you one of those reports before we change the rule.”

**When asking a question.** Lead with the question or decision. Give just enough context to explain why it matters or what will change. For a rule question, show a short excerpt of what the agent was given and what it produced. Add a second example only when it helps the person draw the line. Keep exact trace names, section labels and IDs that help them find the evidence. Use plain language around those labels. Make each answer choice say what it means. Use buttons when available or short labels in chat.

## Check the draft and samples

1. **Read the live guide and record.** Use the Glassray MCP connection for the requested workspace and project. Check that `get_evaluator_guide`, `get_calibration`, `start_calibration`, `draw_fresh_look_traces` and `save_calibration_open_questions` are available. Read the guide's `calibrate` and `try` sections for the current protocol and runner version. Read the deviation, exact draft, effective recipe and current calibration. If tools are missing, report the limitation before changing anything.
2. **Check the draft before sampling.** Read its source and settings first. Verify that the judge receives the recipe and the evidence it needs. Use the runner's `--stages-only --show prompt,pieces` on representative traces to confirm this and check which traces skip. Show the user what is in and out of scope, including which filter, view or flow will select those traces. Use the agreed scope, try a small full local run where possible, and report any gap before drawing the samples.
3. **Keep the samples tied to that scope.** Resume an open calibration if its population still matches. For a new one, pass the agreed scope and the trace IDs already read as `already_read`. Use the same random work sample across ordinary rounds. Keep held-out IDs and contents untouched until the round meant to pass. If feedback changes the population and the old filter no longer fits, show the revised scope and agree on replacement before drawing new samples. Carry forward standing answers and exclude traces already worked on. If Glassray cannot express the revised filter, explain which population the existing samples represent.

## Work the rounds

Before asking to run a model, say where it will run and what pays for it. Verify the billing route or say when you cannot.

- **Subscription.** Name the plan or service, such as Claude Max, ChatGPT or Cursor, and say the test uses its quota. Label API-rate estimates as comparisons.
- **Glassray.** Name the workspace and show the estimate in the units the preview gives. Say the test uses Glassray credits when credits apply. If the run uses the workspace's own API key, say which provider bills that account.
- **Provider API.** Name the provider, such as Anthropic or OpenAI, and say usage is billed to the account that owns the key.

1. **Try to break the recipe.** Run the exact draft and recipe on the full work sample locally. Recheck the traces behind earlier answers about words. Track flags, skips and errors separately and identify any different-model or subscription result as a trial. Use the guide to select traces likely to challenge each line of the recipe and check which lines remain untested.
2. **Complete a separate fresh review.** Call `draw_fresh_look_traces` for unused traces outside both samples and review the evidence in every returned trace against the recipe. Use step listings to locate the evidence, then read the relevant input and output. Keep each ID as you finish reviewing its evidence. When an independent reader is available, give that reader only the recipe and the fresh traces. Group unresolved ambiguities by type, cite the trace evidence, and record findings with `save_calibration_open_questions` including `shown_in` and whether a yes widens the deviation. After reviewing every trace drawn in the round, send the full list once each in `reviewed_trace_ids` with `save_calibration_open_questions`. Send `questions: []` if no findings remain to save. Check `fresh_look_review` is complete before using `asking` or moving to final tests. If another draw extends the round, review the additional traces and confirm the full list again.
3. **Ask and apply one ruling at a time.** Use Glassray's stop point after the review. Ask the largest unresolved question the traces cannot answer. Use the question format above with a real example, the specific ambiguity and the trace ID. Wait and save the exact answer with `save_calibration_answer`. Show recipe changes before confirming them. When needed, save a new version by file upload. Close a changed round as `fail`, report `sample_flagged`, and repeat on the same work sample. At the stop point, follow the guide's separate user message about continuing questions or moving to testing.

## Finish and report

- **Coverage** estimates how much of the scope the recipe settles. It uses the latest completed fresh review, subtracting the share of traces with unanswered cases and an allowance for unseen case types. A work-sample flag rate measures something else. Coverage stays unmeasured without sufficient fresh evidence, and opening an ordinary new round does not reset the preceding completed review.
- **Consistency** is how often repeated runs of the same evaluator version agree on the held-out traces. Keep both scores separate from accuracy. After recording fresh-look findings or a ruling, fetch `get_calibration` again before reporting either score. Use the values and `coverage_basis_round` from that reply.
- In the round meant to pass, try a cheaper setup as the guide specifies and verify that the candidate still follows the user's rulings. Use hosted tests for the required repeats on the held-out traces and the traces of answers about words. Preview each paid run with `run_evaluator_test` and `preview: true`. Include comparable local usage when available. Show the purpose, where it runs, the payer and the preview estimate. Wait for the user's approval of that run.
- Check the hosted results against the saved rulings and report instability. Record any acceptance of scores or unstable cases only after the user sees and accepts them. End the round with `finish_calibration_round` when the pass criteria are met, or record a stop if the user wants to stop. Report the version, rulings, scores and what they mean, open cases, untested rule lines, costs and who paid for the runs. Publishing or activating an evaluator requires separate authorization.
