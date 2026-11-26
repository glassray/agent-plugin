---
name: glassray-test-fix
description: Test whether a code change fixes a Glassray deviation before it ships, by replaying stored traces through the agent's code at production's commit and at the fix, judging both with the deviation's evaluator, and recording the result as a fix test. Use when someone asks to test, check or measure a fix, a branch or a pull request against a deviation.
---

# Test a fix with replay

Show what a change does to a deviation: how many flagged traces it fixes and how many clean ones it breaks, measured the same way every time and kept in Glassray as a fix test.

Keep the replay adapter, downloaded traces and replayed files uncommitted. Keep traces and replays outside every repo: they are client data. If a run needs a model API key, read it from the environment.

Use plain language in every message to the user, from the first update to the final report. Start with what happened, what you found, or the decision they need to make. Use their words for the work and explain any necessary Glassray term the first time it matters, such as baseline (the code before the change) or adapter (the small script that runs one step of their code on a stored trace). Keep tool details in the background unless they help the user act. Keep counts, commits and IDs exact.

For example, say “The change fixed 3 of the 17 reports that were flagged before, in both runs, and broke none of the 3 clean ones. That would take the rate from about 19% to about 16%.”

When a run behaves unexpectedly, tell the user what you expected and what actually happened. Say what caused it if you know, what you are changing, and what a rerun will verify. Never count a trace that failed or timed out as passing.

## Set up

1. **Read the live guide and record.** Use the Glassray MCP connection for the requested workspace and project. Check that `get_evaluator_guide`, `get_deviation`, `get_trace_download_links`, `start_fix_test`, `score_fix_test`, `save_fix_test` and `get_fix_test` are available. Read the guide's `replay` section for the current commands, the adapter contract and the trace-count rule. Read the deviation, its published evaluator version and the earlier fix tests for it (`get_fix_test` with `include_unsaved`). If tools are missing, report the limitation before changing anything.
2. **Find the change and what it switches on.** Ask where the change is: a branch, a pull request or uncommitted changes, and commit uncommitted changes to a temporary branch so the replay runs one fixed version. Read the diff against production's commit. List every feature flag or setting the change adds or reads, and ask the user which ones belong to the fix. A branch often carries experiments that are not part of it, and turning one on changes the result.
3. **Check what the evaluator reads.** Read the published version's source and find the steps it judges. Compare them with the steps the change edits. When they differ (the fix edits a final letter, the evaluator reads the sections written before it), the adapter has to put the fix's output where the evaluator reads it, and the baseline has to be replayed through the same adapter at production's commit, so both sides are judged on the same text.
4. **Get an adapter.** Use the repo's `.glassray/replay.json` when it exists and runs the step the change edits. Otherwise write one as the guide's `replay` section describes: run only that step, feed it from the trace, replace only how the code gets its settings (flags, prompts, the API address), and exit non-zero when it could not replay. Leave it uncommitted and point at it with `--adapter`.
5. **Check the adapter before trusting it.** Replay 3 to 5 traces at production's commit, at least one the evaluator passed, judge them, and compare with production's verdicts. They must match. When the adapter makes no model calls at production's commit, also replay one trace at the fix and confirm the changed step ran and made its model calls.

## Agree and record

Before asking to run anything, say where it will run and what pays for it.

- **Subscription.** `npx @glassray/replay` answers the code's model calls through Claude Code, signed in on this machine, by default. Name the plan and say the run uses its quota with no separate API charge. Say it is a trial: Claude Code wraps each call, so it is not production's exact request.
- **Provider API.** With `--via api-key`, calls go to the Anthropic API on `ANTHROPIC_API_KEY` and are billed to the account that owns the key. Use it to confirm a result that matters.
- **Judging** follows the evaluator guide's billing: add `--via coding-tool` to the evaluator runner on a subscription, or it uses any API key left in the shell.

1. **Propose the setup.** Give the traces, the baseline, the number of runs with the change, the settings, the evaluator version, how long it will take and what pays. Work out the trace counts with the guide's rule, not a fixed number: the deviated count sets how precisely the fix rate is known, and the clean count grows the rarer the deviation is. Say which trigger rate you used. Use 2 runs by default. Check that the chosen traces hold what the adapter needs; older traces may predate a step the change relies on.
2. **Record it before running.** When the user agrees, call `start_fix_test` with the setup, the user's name as `started_by`, and `budget_usd` when they set one. Its answer lists the steps with the exact commands. Every replay and judgement belongs to that test.

## Run and score

1. **Download.** `get_trace_download_links` with `inline: false`, then run the `save_with` command its reply gives, which saves `links.json`. Save each trace under its `file_name`, outside every repo, right away: the links stop working after a few minutes, so call again for fresh ones rather than keeping old ones. Never paste a link into chat, a ticket or a commit.
2. **Replay.** Run the commands the test gave: `npx @glassray/replay run --repo <checkout> --commit <commit> --out <folder>/fix --runs <n> --env KEY=VALUE ...`, and the same at production's commit into `<folder>/baseline` when the baseline is replayed. One output folder holds one setup. A run can take an hour: start it in the background, raise `--concurrency` when the model route allows it, and give the user progress counts from the output folders. When a slow model call hits the adapter's `timeout_seconds`, copy the adapter file with a longer limit and replay only that trace and run again with `--adapter`, into its own folder.
3. **Judge.** `npx @glassray/evaluator run --links links.json ... <folder>/fix/run1`, once per run and once for the baseline, with the evaluator version the test names. Keep each trace's verdict per run.
4. **Score.** Call `score_fix_test` once, with every planned run's verdicts and its model route. A trace a run failed on is `failed`, never passed.
5. **Report.** Give the headline (fixed, still flagged, broke, unstable), the traces in each group, and what it means in production: trigger rate × (1 − fix rate) + (1 − trigger rate) × break rate, against the rate today. Say when the counts are too small to tell a modest change from noise, and when the runs were a subscription trial. Offer to read the evaluator's reasons for the traces still flagged.
6. **Save only on request.** When the user stands behind the result, call `save_fix_test` with their name as `saved_by` and their note, if any. Saving changes nothing else: not the deviation, the evaluator or the pull request.

End with the fix test ID, the commits compared, the settings, the result, where it ran and what paid for it, and anything left unchecked. Merging, publishing or changing the deviation's status requires separate authorization.
