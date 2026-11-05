# Attachment Handling Across Microsoft 365 Connectors

[← Back to project overview](../README.md)

## Challenge

Attachment handling was one of the most complex implementation areas. Microsoft Forms uploads, SharePoint list attachments, Outlook attachments, and Power Automate Approval attachments use different action inputs and data structures.

## Connectors and scenarios

| Source or destination | Framework consideration |
| --- | --- |
| Microsoft Forms | Receive user-submitted file references and retrieve the corresponding content. |
| SharePoint Lists | Persist and retrieve request-related attachments. |
| Outlook | Build the attachment payload expected by email actions. |
| Power Automate Approvals | Build the attachment payload expected by approval actions. |

## Handling principles

- Preserve the original file name where possible.
- Retrieve actual file content, rather than assuming a metadata reference is sufficient.
- Account for content type and binary/base64 representations as required by each action.
- Prepare connector-specific attachment arrays before sending approvals or emails.
- Verify that attachments open correctly after delivery.
- Avoid reliance on manual copying or repackaging by users.

## Validation scenarios

The implementation notes emphasize extensive testing to diagnose missing or corrupted attachments. A useful verification checklist is:

- File is accessible to the intended reviewer.
- File name and extension remain correct.
- Content is not empty or corrupted.
- SharePoint and email/approval copies represent the same submitted file.
- Corrections and resubmissions use the intended updated attachments.

The checklist is a **documentation aid**, not a claim that each test case was formally recorded in the original project.

## Implementation confidentiality

This case study deliberately omits real expressions, payloads, file paths, and company attachment examples. Connector-specific schemas should be checked against the action version used in the actual solution.

## Related documentation

- [Approval workflows](approval-workflows.md)
- [Architecture](architecture.md)
