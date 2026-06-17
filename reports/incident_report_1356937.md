# Operational Incident Investigation Report

## Current Incident Number
1356937

## Reported Issue Summary
Administrators on the Cambridge One platform are unable to create reports when selecting specific dates.

## Initial Assessment
The issue is likely isolated to the reporting/analytics module or the underlying data access rules governing date-range queries. Given the historical data, there is a high probability of a corrupted rule metadata or an inconsistent state in the platform's configuration, which often manifests as failures during specific user actions or data retrieval.

## Similar Historical Incidents

| Ticket | Similarity | Root Cause |
| :--- | :--- | :--- |
| 1328197 | 0.3343 | Corrupted Lock Rule |
| 1299299 | 0.3512 | Corrupted Lock Rule |
| 1274447 | 0.3922 | Data discrepancy in DynamoDB rule record |
| 1294443 | 0.3935 | External (Cloudflare) / Sync failure |
| 1241368 | 0.3991 | Incorrect User State ("skipped" status) |

## Common Patterns Identified
1. **Rule Corruption:** A recurring theme where configuration rules (specifically "Lock Rules" or access metadata) become corrupted or inconsistent, requiring a "re-save" to refresh the state.
2. **Metadata Inconsistency:** Discrepancies between rule display and actual rule data, often found in DynamoDB records, leading to `TypeError` or `500` errors.
3. **State/Persistence Issues:** Similar to Incident #1294443, external service or synchronization hiccups can prevent data from reflecting correctly in the UI.

## Root Cause Analysis

*   **Confirmed causes:** N/A (Investigation currently in progress).
*   **Suspected causes:** 
    *   **Corrupted Reporting/Access Rule:** The reporting configuration may have encountered a data corruption event similar to Incident #1328197 and #1299299.
    *   **DynamoDB Discrepancy:** If the report generation relies on specific access rules or scope definitions, the record may have drifted (similar to Incident #1274447).
    *   **Frontend Validation Logic:** The "specific dates" filter may be triggering a client-side or backend validation error due to invalid state/parameters.
*   **Missing evidence:** 
    *   Application logs (Kibana) regarding the specific error returned when a report fails to generate.
    *   The `cf-ray` ID for the failed request.
    *   Confirmation of whether this affects all admins or only specific accounts.

## Actions Performed Previously

*   **1328197:** Investigated launch events; identified corrupted Rule 21; resolved by opening and re-saving the rule without changes.
*   **1299299:** Replicated the issue; identified corrupted Lock Rule 8; resolved by re-saving the rule to clear configuration corruption.
*   **1274447:** Network/Log analysis identified `CONTENT_ACCESS_RULES_ERROR` due to scope-change attempts; resolved by manually updating DynamoDB rule records.
*   **1294443:** Analyzed `ltigw` logs; identified Cloudflare interference; resolved by manually injecting JSON into `localStorage` and hitting the return URL.
*   **1241368:** Analyzed user state file; identified "skipped" interaction node; resolved by performing a user state reset.

## Recommended Investigation Steps
1. **Log Analysis:** Check Kibana/ELK for `500` errors or `TypeError` entries corresponding to the exact timestamp of an admin's failed report attempt.
2. **Rule Metadata Check:** Inspect relevant DynamoDB tables for report-configuration rules to ensure there are no inconsistencies between rule-data and rule-display.
3. **Replication:** Attempt to recreate the failure using the same date range and parameters to see if it triggers a specific error message.
4. **Environment Check:** Verify if recent platform updates or config changes (similar to the "10th March" change in 1328197) coincide with the reported timeline.

## Recommended Resolution Path
Based on the high frequency of "Corrupted Rule" patterns, the most likely successful resolution is to locate the specific reporting configuration rule associated with the affected accounts and perform a "re-save" of the rule via the Support Admin panel to force a refresh of the metadata.

## Confidence Level
**Medium**
The historical patterns point strongly to rule corruption or data inconsistency. However, as "reporting" involves different backend processes than "assignment access," it is possible this is an isolated service-level failure rather than a configuration metadata error.