# Scenario controls — version 0.6.0

Date: 18 November 2026. This iteration was implemented with ChatGPT assistance. This record describes the work and verification performed; it does not assign individual group contributions.

## Request and resulting change

The request was to choose the scenario in the UI. In version 0.5.0, the report selector reviewed scenarios that had already run. Version 0.6.0 adds a control screen launched with `py AgentWatch.py --ui`. It previews the selected case's task, requested action, source rows and real input records before running it. The Run scenario button executes that case under all three protection settings and saves a new result folder. Approval can be granted or refused for cases that require it.

## Design decisions

- Keep application code and checks in AgentWatch.py so the project remains easy to open in VS Code. Use Python's standard library without additional installation.
- Use a running local service for new tests. Keep report.html as a portable record of a completed test.
- Reuse the policy engine, working-copy executor and export function for both terminal and UI runs. Selecting a case does not execute it. Expected result labels do not authorize actions.
- Test one selected case once under all three settings per click. Start fresh working copies and retain separate files for every run. Record the selected scenario ID, approval setting, version and interface in the run metadata.
- Show the task and data before the results. Put account rules and evidence links in expandable sections. Disable the approval control when the selected case does not request approval, and clear old displayed results when the selection changes.

## Verification performed

The 30 existing automated checks and four new HTTP interface checks passed: 34 tests in total. New checks cover source-backed previews without execution, selected deletion and price-change cases, approval grant/refusal on a permitted export, separate evidence folders, input validation and confinement of result-file access.

The interface's JavaScript was also exercised with a DOM unit harness and the running local service. It checked 30 available choices, selection without execution, three results for a selected run, approval grant/refusal, clearing stale results, disabling controls during a run and separate saved reports. These were code-level checks. Hosted browser visual verification was unavailable because that browser could not connect to the local process.

To repeat the built-in checks, run `py AgentWatch.py --test`. To inspect the UI, run `py AgentWatch.py --ui`, choose IH02, then AH03, and compare Grant approval with Refuse approval. The saved example is an assistant-generated verification run, identified in its run metadata.

The published source transactions are unchanged from version 0.5.0. The business tasks, account permissions and approval choices remain configured experiments. No live AI agent or measured human approval time is claimed.

## Rule explanations — version 0.6.1, 18 November 2026

This AI-assisted iteration addresses the request to identify which rule accepted or rejected an action. Decisions now carry an ordered trace from the policy engine: account permission, task action, task file, allowed destination and the high-risk approval requirement. The recorded approval response is appended after resolution. A failure identifies the rule that stopped the request; later rules are marked Not applied. The baseline explicitly reports that checks were disabled.

The UI and saved report display the deciding rule and a Why this decision? section comparing requested and allowed values. Individual trial rows and execution JSON records retain the same explanation. Policy refusals and approval refusals remain distinct, and the existing expected authorization outcomes remain unchanged.

All 36 automated checks passed, including two added checks for distinct scope failures, approval outcomes, matching UI and saved explanations, and HTML escaping. The control JavaScript was exercised again against the local service with the new rule evidence included. Source transaction hashes were retained. Browser rendering was not visually verified.
