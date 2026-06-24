# Operational Incident Investigation Report

## Current Incident Number
1351969

## Reported Issue Summary
The user is unable to view activated entitlements on the Cambridge One (C1) platform. Despite successful code activation, the products are not appearing on the user's dashboard.

## Initial Assessment
The issue affects the Cambridge One entitlement service, specifically the synchronization layer between code redemption and the user's display dashboard. Likely components involved: PEAS (Platform Entitlement/Activation System), user entitlement database, and the account refresh triggers.

## Similar Historical Incidents

| Ticket | Similarity | Root Cause |
|---------|-----------|------------|
| 1007976 | High | Synchronization failure post-activation (COS-1004) |
| 1006931 | High | Intermittent sync failure between redemption and application |
| 1007467 | High | Successful activation in PEAS, but missing in dashboard |
| 928108 | Medium | Entitlement mapping failure/sync delay |
| 1067207 | Medium | Cursor/iterator limit in the Refresh Entitlement process (COS-1274) |

## Common Patterns Identified
*   **Intermittent Sync Failures:** The most recurring pattern is a failure in the entitlement synchronization process immediately following code redemption.
*   **PEAS Discrepancy:** The system often records the activation as successful in backend systems (PEAS), but fails to update the user-facing dashboard.
*   **Logging Gaps:** Frequently, Kibana logs do not show explicit error traces for these specific synchronization failures, leading to "silent" failures.
*   **"Refresh Entitlement" Dependency:** Manual intervention via the "Refresh Entitlement" feature is the standard, effective workaround.

## Root Cause Analysis
*   **Confirmed causes:** None yet confirmed for the current incident; however, based on history, it is highly likely to be a recurrence of the sync issues tracked under **COS-1004** or a similar systemic delay.
*   **Suspected causes:** Failure in the background synchronization worker triggered by code redemption, or a persistent backlog of entitlement propagation tasks.
*   **Missing evidence:** Current Log/Root ID for the affected user, confirmation if the user was part of a bulk activation (which might trigger the **COS-1274** batch limit issue).

## Actions Performed Previously
*   **1007976:** Investigated under COS-1004; resolved via manual entitlement refresh.
*   **1006931:** Analyzed via Kibana (no logs found); resolved via "Refresh Entitlement" feature.
*   **1007467:** Verified in PEAS; utilized "Refresh Entitlement" to restore access.
*   **928108:** Escalated to engineering for platform defect analysis.
*   **1067207:** Identified a hard limit on batch processing (20 batches); resolved via code fix COS-1274.

## Recommended Investigation Steps
1.  **PEAS Verification:** Check the user's activation status in PEAS to confirm the code was processed successfully.
2.  **Log Analysis:** Search Kibana for the specific user ID and activation timestamp to check for "silent" failures.
3.  **Refresh Trigger:** Execute the "Refresh Entitlement" feature for the affected user account.
4.  **Batch Check:** Determine if the missing entitlement is part of a bulk activation; if so, refer to the findings of **COS-1274**.

## Recommended Resolution Path
Perform a manual "Refresh Entitlement" on the user account. If this fails to resolve the issue, escalate to the Engineering team for a deeper synchronization check, referencing **COS-1004** as a known recurring pattern for intermittent entitlement failures.

## Confidence Level
**High**
The reported behavior perfectly mirrors the intermittent synchronization patterns observed in tickets 1007976, 1006931, and 1007467. The "Refresh Entitlement" tool has a proven history of success in restoring user access in these scenarios.