# State Management and Duplicate-Run Safeguards

[← Back to project overview](../README.md)

## Objective

SharePoint fields provide business-level request state and help control correction, resubmission, and potential duplicate execution.

## Key fields

| Field | Intended responsibility |
| --- | --- |
| `Status` | Current business status of the request. |
| `CorrectionRequired` | Whether the requester must make corrections. |
| `FlowLock` | Safeguard against overlapping or duplicate processing. |
| `ResubmissionCount` | Count of correction/resubmission cycles for traceability. |

## Resubmission pattern

1. Administrative review identifies a correction requirement.
2. The request is marked for correction.
3. The requester updates the SharePoint item.
4. A SharePoint button triggers the update/resubmission flow.
5. The workflow resumes the applicable review path.
6. The request's status and resubmission count support follow-up and auditability.

## Why a lock field matters

A `FlowLock` flag can reduce accidental overlapping executions when a user resubmits or when multiple triggers could act on the same record. **A SharePoint flag alone is not a guarantee of atomic concurrency control**; effectiveness depends on how the flow checks, sets, and clears the flag and on trigger settings. These details are not exposed in this public case study.

## Tracking versus run history

SharePoint stores the **business lifecycle** of a request. Power Automate run history supports **technical troubleshooting**. The two views are complementary, not interchangeable.

## Open verification points

- Exact trigger conditions and lock-release paths.
- Behavior after timeouts or failed runs.
- How repeated resubmissions are prevented or handled.
- Whether status transitions are validated against allowed states.

These are suggested technical review points; the source documentation does not establish their detailed implementation.

## Related documentation

- [Approval workflows](approval-workflows.md)
- [Error handling](error-handling.md)
