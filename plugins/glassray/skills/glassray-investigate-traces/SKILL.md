---
name: glassray-investigate-traces
description: Investigate recent Glassray traces locally when someone wants to explore agent behavior, look for potential issues in a flow or customer’s cases, or check a suspicion before deciding whether to create an issue and evaluator.
---

# Investigate traces with the customer

Start with the connected Glassray project and what the person wants to learn. If they already named a flow, company, behavior or example trace, use that starting point. For an open-ended request, help them choose a small set of cases worth reading.

## Choose what to look at

If the person named a flow, start there. Otherwise use recent activity to shortlist flows. Read the expected behavior before proposing what to investigate.

- Use `list_flows` to understand the workflows. Use `group_traces` for counts in the window. A flow's total membership and the latest page of traces can each give a misleading picture of recent activity. If flows are empty, check for unassigned traces before offering tracing setup.
- Read each shortlisted flow with `get_flow`. Its rules, code anchors and good/bad examples describe what the agent is expected to do. Use `get_spec` for relevant system-wide rules. Pick a rule whose conditions and outcome are visible in the traces. If rules are missing or appear to describe a different version, use the actual trace instructions and identify any expectation that needs confirmation.
- With a few cases, show how one case followed or departed from a specific rule. In an established workspace, inspect linked issues and their evaluators before suggesting a new check. Read what an evaluator actually tests; a watched rule or a similar evaluator name alone does not establish coverage. If the scope is already clear, proceed without another selection question.
- Within a promising flow, look for a property that changes what you can learn: request type, document type, model version, or a tool the agent used. Discover keys with `list_attribute_keys`. To see actual values and counts together, use `group_traces` with `flow_id`, `since_hours`, `group_by: "attribute"` and `attribute_key`. For a company comparison, group by customer in the same window and resolve identifiers with `list_customers`.
- Check both sides of a proposed comparison. If grouping times out, narrow the window or use `list_attribute_values` and verify returned values inside the flow with filtered `list_traces`. If counts come from a limited page, say “at least” when the page is full. Missing metadata is a reason to choose a different comparison; it is not evidence the behavior never occurred.

Offer two or three distinct **questions** the cases could answer. Lead with the one that gives the clearest evidence of expected versus actual behavior. For each choice, give its scope, window, case count if known, and one plain reason for choosing it. For instance, “Did the revisions make the edit the adviser asked for?” explains why someone might choose revision cases. Offer a company when the person cares about it or there is a useful within-company comparison. High volume alone says nothing about quality. Keep a broad recent sample as the fallback when the person wants to explore. Ask which direction they prefer and leave room for their own suspicion.

## Read the cases locally

Use `list_traces` to select IDs for the agreed window, flow and filters. It accepts `flow_id` alongside a customer or metadata filter. For a comparison, keep the time window and other filters comparable and record the number reviewed on each side. If a slice is empty, tell the person before looking elsewhere.

Use the returned IDs to read the evidence. For local file inspection, use `get_trace_download_links` and keep the downloads outside repositories. Keep working files uncommitted, read any required keys from the environment, and keep signed URLs private. Do the reasoning in this session. This investigation needs no hosted discovery, deep search or general evaluator run.

For a suspicion, follow the relevant step from its instructions and inputs through its action or final output. For exploration, look for specific departures from the requested behavior. Check whether similar cases show the same problem and look for a close case where the agent handled it correctly. A difference between companies or flows can reflect different instructions. If the expected behavior is unclear, show the evidence and ask what should have happened.

## Show findings before creating issues

Lead with what you found and where you looked. For each distinct candidate, name the applicable flow rule (keep its ID for the evaluator handoff), give the expected and observed behavior, a short excerpt or action with its trace ID and step, and the number of inspected cases that support it. Show a useful counterexample or uncertainty when available. Count separate cases once. A count from recent or deliberately selected cases describes that sample only. If nothing supports a suspicion, say so.

Let the customer select which findings to turn into Glassray issues. Check existing deviations first, so a matching issue can be reused. For each new issue they select, call `create_deviations` with its representative `trace_id`, a short label, and the supporting evidence. Return the issue reference. When they want an evaluator, hand the reference, scope, evidence IDs and any close passing case to `glassray-create-evaluator`; use `glassray-calibrate-evaluator` once the draft is ready.
