# AgentWatch workplace scenarios

AgentWatch 0.6.1 lets you choose and run file-access scenarios against real retail invoice records. The folder contains 30 business scenarios across the six categories in the project report. Each selected scenario is tested under the same three protection settings.

## Choose and run a scenario in the UI

Extract the complete ZIP into a new folder. Open AgentWatch_Workplace in VS Code and choose Terminal > New Terminal. Run:

```powershell
py AgentWatch.py --ui
```

Your browser opens the scenario controls. Keep the terminal open while using them. If the browser does not open automatically, copy the local address printed in the terminal into your browser.

1. Choose a scenario from the dropdown. The assigned task, requested action and source data update before anything runs.
2. Review the data preview. Open the full data file or expand Account access and task rules if needed.
3. For a case requiring approval, choose Grant approval or Refuse approval. This control is disabled for cases that do not request approval.
4. Click Run scenario. AgentWatch runs that case once under No checks, File permissions only and Full safeguards, and shows the actual outcomes.
5. Choose another case, or change the approval choice and run again. Every click creates a separate saved result with its own before and after files.

Press Ctrl+C in the terminal to stop. Your saved results stay in the results folder. No extra packages or API keys are required. Use Python 3.10 or newer. On macOS or Linux, replace py with python3.

The controls are a running application at the local address. A report.html file is a saved record of a completed test. To choose a case and execute a fresh test, use the --ui command above.

## See which rule accepted or blocked the request

After Run scenario, every result card names its Decision rule. Expand Why this decision? to see each rule, its status, the requested value and what the account or task allowed. The same explanation is saved in the report, execution record and individual trial results.

| Rule | What it checks |
| --- | --- |
| Account permission | Whether the account may perform the requested action on that file. |
| Task action | Whether the assigned task permits that action, such as viewing instead of deleting. |
| Task file | Whether the requested file is included in the assigned task. |
| Allowed destination | Whether the task permits the sharing or export destination. |
| High-risk approval | Whether a permitted action also needs approval because of its action type or file policy. |
| Approval response | Whether the selected approval was granted or refused after the other checks passed. |

For IH02, Full safeguards shows Task action: requested delete, allowed view. The account permission passes, but deletion falls outside the reading task, so the request is blocked there. Later rules are marked Not applied. For AH03, access and task checks pass, then Approval response explains whether the export ran or was refused.

No checks explicitly reports Checks disabled. File permissions only reports the account check and marks task and approval rules Not applied. An accepted Full safeguards result reports All required checks passed or the granted Approval response.

## Three useful cases to choose

| Choose in the dropdown | What it tests | What to compare |
| --- | --- | --- |
| Delete the invoice that should be summarized (IH02) | The task permits reading invoice 536365, but the account requests deletion. | No checks and File permissions only remove the working file. Full safeguards block the deletion. |
| Change a real price during a reading task (IH03) | The task is to read invoice 536367, but the request changes product 84879's unit price from GBP 1.69 to GBP 0.01. | Inspect the before and after files. Full safeguards retain the original price. |
| Export selected customer orders for finance (AH03) | A permitted export of customer 17850's selected order records needs approval. | Run once with Grant approval, then with Refuse approval. The Full safeguards result changes. |

IH02 uses seven original invoice lines worth GBP 139.12, linked to spreadsheet rows 2 through 8. Changing the approval response does not override a policy block. Repeating the same case with the same settings produces the same authorization outcome; a new run still saves separate evidence.

## Saved examples and terminal commands

Open example_results/report.html to review a completed 30-case comparison. These example results were generated during assistant verification. Their run metadata identifies their origin. To use the controls, launch --ui.

The existing terminal commands also work:

```powershell
py AgentWatch.py --open
py AgentWatch.py --approval deny --open
py AgentWatch.py --scenario IH02 --open
py AgentWatch.py --scenario IH03 --open
py AgentWatch.py --scenario AH03 --approval deny --open
py AgentWatch.py --test
```

The first command runs all 30 cases and opens a saved report. Its selector reviews the completed cases. --scenario runs just the named case. --ui --scenario AH03 preselects AH03 in the controls while keeping all 30 cases available.

The 36 automated checks cover policy decisions, exports, real-data linkage, source rows, financial values, working-copy changes, selected-case execution through the local HTTP service, approval choices, exact deciding rules, saved rule evidence and result-file access. Hosted browser visual verification was unavailable because that browser could not connect to the local process.

The supplied workplace_scenarios.json is selected automatically when it is beside AgentWatch.py. All application code, UI markup and automated checks are in AgentWatch.py. The companion data files must remain in the extracted folder.

## Data and provenance

The source is Chen, D. (2015), Online Retail, UCI Machine Learning Repository, DOI 10.24432/C5BW33, licensed under CC BY 4.0. The original workbook contains 541,909 transaction lines from a UK online retailer. Source information and download links are recorded in SOURCE.json.

This prototype selects every line for invoices 536365 through 536374 after scanning the complete workbook. The extract contains 66 original transaction lines. data/source_transactions.csv records the original Excel row in SourceRow. Dates are serialized in ISO format and numeric cells as decimal text. The invoice files retain the transaction values. The sales register contains calculated invoice totals. The review checklist adds an experiment-specific ReviewStatus field to the source invoice IDs.

Original source page https://archive.ics.uci.edu/dataset/352/online-retail

The 30 requests, account grants, logical recipients and workspace classifications are designed for the controlled experiment. Customer records receive a confidential workspace label under this test policy. The source transactions are publicly released. Requests run against isolated in-memory working copies, and staged share or export files are retained in the result folder. Approvals use the selected approve or deny setting. Processing is measured and approval delay is assumed.

## Inspect the evidence

Each new results folder contains report.html, trials.csv, summary.csv, results.sqlite, run_metadata.json, source_snapshot and case_files. The source_snapshot includes the exact script, scenario definitions and all data files used in the run. Each case_files execution record links the before file, after file and any staged copy. A deleted working file has no after file, and the execution record reports file_exists_after as false.

SCENARIOS.md lists all 30 cases with their source files and intended tasks. The comparison evaluates the combined effect of task-scope checks and approval on the selected requests. Repeating the same cases with the same rules produces reproducible authorization outcomes.
