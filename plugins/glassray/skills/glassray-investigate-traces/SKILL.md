---
name: glassray-investigate-traces
description: Investigate recent Glassray traces locally when someone wants to explore agent behavior, look for potential issues in a flow or customer’s cases, or check a suspicion before deciding whether to create an issue and evaluator.
---

# Investigate traces with the customer

Start with the connected Glassray project and what the person wants to learn. If they already named a flow, company, behavior or example trace, use that starting point. For an open-ended request, help them choose a small set of cases worth reading.

## Choose what to look at

Use lightweight MCP reads before asking the customer to choose. Use `list_flows` to see what the agent does and `list_traces` to see what has arrived recently. Look for one or two useful ways to split those cases. For company, check `list_customers`. For trace metadata, use `list_attribute_keys` and inspect relevant values with `list_attribute_values`. A property such as report type, workflow version or tool used can distinguish behaviors; a unique request ID usually only helps locate one case. Check that a suggested slice actually contains recent traces. An empty flow list can also mean traces have arrived but have yet to be assigned to flows.

Ask one short question using the customer's words and real choices from their project. For example, if an advisory workspace has these flows and properties: “Shall we look at the 10 most recent cases across companies, focus on annual reviews for one company, or compare annual reviews with suitability reports? Is there a particular behavior you’re curious about?” Use the actual flow and company names when they help. If the person already gave a usable scope, go straight to the cases. If the project is empty, offer the tracing skill to get a first case in.

## Read the cases locally

Use `list_traces` to select IDs for the agreed window, flow and filters. It accepts `flow_id` alongside a customer or metadata filter. For a comparison, keep the time window and other filters comparable and record the number reviewed on each side. If a slice is empty, tell the person before looking elsewhere.

Use the returned IDs to read the evidence. For local file inspection, use `get_trace_download_links` and keep the downloads outside repositories. Keep working files uncommitted, read any required keys from the environment, and keep signed URLs private. Do the reasoning in this session. This investigation needs no hosted discovery, deep search or general evaluator run.

For a suspicion, follow the relevant step from its instructions and inputs through its action or final output. For exploration, look for specific departures from the requested behavior. Check whether similar cases show the same problem and look for a close case where the agent handled it correctly. A difference between companies or flows can reflect different instructions. If the expected behavior is unclear, show the evidence and ask what should have happened.

## Show findings before creating issues

Lead with what you found and where you looked. For each distinct candidate, give the expected and observed behavior, a short excerpt or action with its trace ID and step, and the number of inspected cases that support it. Show a useful counterexample or uncertainty when available. Count separate cases once. A count from recent or deliberately selected cases describes that sample only. If nothing supports a suspicion, say so.

Let the customer select which findings to turn into Glassray issues. Check existing deviations first, so a matching issue can be reused. For each new issue they select, call `create_deviations` with its representative `trace_id`, a short label, and the supporting evidence. Return the issue reference. When they want an evaluator, hand the reference, scope, evidence IDs and any close passing case to `glassray-create-evaluator`; use `glassray-calibrate-evaluator` once the draft is ready.
