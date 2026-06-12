# 🧩 Power Platform Business Process Automation Framework Architecture

**Institution:** Foresight Atlantic Inc.  
**Duration:** 2026 – Present  
**Role:** Business Manager / Process Automation Developer  
**Note:** Due to confidentiality, company data, real flow definitions, service account details, URLs, IDs, internal records, and business-sensitive information cannot be shared. This case study describes the architecture, design patterns, and development approach without exposing confidential data.

---

## 📌 Project Overview

This project documents a reusable **Power Platform Business Process Automation Framework** designed to support internal request intake, approvals, notifications, tracking, monitoring, and error handling.

The initial implementation was applied to **timesheet** and **expense approval workflows**, but the framework was designed to be reusable for future business process automation initiatives.

The solution uses Microsoft 365 and Power Platform components such as:

* Microsoft Forms
* SharePoint Lists
* Power Automate Cloud Flows
* Power Automate Approvals
* Outlook
* Microsoft Teams
* SharePoint attachments
* Service account strategy

The main objective was not only to automate approvals, but also to establish a maintainable and scalable development pattern for future internal automation projects.

---

## 🎯 Business Problem

Internal requests such as timesheet submissions and expense claims were handled through manual or semi-manual processes involving forms, files, email communication, and manager approvals.

Although the process appeared simple from the user perspective, the backend required a reliable structure to manage:

* Request intake
* Uploaded attachments
* Approval routing
* Email and Teams notifications
* Correction requests
* Resubmission logic
* Error handling
* Operational monitoring
* Request tracking
* Long-term maintainability

The challenge was to build a solution that was not only functional, but also traceable, reusable, and easier to maintain.

---

## 🧭 Solution Overview

The solution was designed as a modular framework based on a **parent-child flow architecture**.

The parent flow is responsible for the common intake and orchestration logic, while child flows are responsible for specific business approval processes.

The first use cases implemented were:

* Timesheet approval workflow
* Expense claim approval workflow

However, the same framework can be adapted to other internal request and approval processes.

---

## 🏗️ Framework Architecture

```text
Microsoft Forms
    ↓
Parent Flow - Submit a Request
    ├── Scope - Main
    └── Scope - Error Handling
    ↓
SharePoint List - Request Tracking
    ↓
Request Type Evaluation
    ↓
Child Flow - Timesheet Approval
    ├── Scope - Main
    ├── Scope - Error Handling
    └── Return standardized result/message to Parent Flow

Child Flow - Expense Approval
    ├── Scope - Main
    ├── Scope - Error Handling
    └── Return standardized result/message to Parent Flow
    ↓
Parent Flow evaluates child flow response
    ├── result = success
    │       └── Continue normal request status update
    └── result = error
            └── Trigger error handling and monitoring
                    ├── Post message to Microsoft Teams Channel
                    │       └── IT - Automation Alerts / General
                    └── Send email to Service Account Shared Mailbox
    ↓
SharePoint Status Updates
```

The architecture was designed to separate normal business processing from operational monitoring and error handling.

In the normal execution path, the flows process the request, send the required approval, evaluate the approval outcome, and update the SharePoint request status.

Operational alerts through Microsoft Teams and the service account shared mailbox are mainly used for error handling, monitoring, and support visibility. If a child flow returns an error response, the parent flow evaluates the standardized result and message outputs and triggers the appropriate monitoring actions.

Both parent and child flows follow the same structured pattern using:

Scope - Main
Scope - Error Handling

The architecture was designed around the following principles:

* Modularity
* Reusability
* Separation of responsibilities
* Traceability
* Error visibility
* Maintainability
* Controlled ownership
* Operational monitoring

---

## 🧩 Design Principles

The framework was developed based on practical design principles commonly used in business process automation and enterprise workflow solutions.

### Modularity

The automation was divided into parent and child flows to avoid large, hard-to-maintain workflows.

### Reusability

Common logic was centralized in the parent flow, while specific approval paths were handled by child flows.

### Separation of Responsibilities

Each flow has a clear responsibility:

* Parent flow: intake, orchestration, and routing
* Child flows: business-specific approval logic
* Error handling scopes: exception handling and notifications
* SharePoint: request tracking and status visibility

### Traceability

SharePoint Lists are used to track the request lifecycle and provide visibility beyond Power Automate run history.

### Maintainability

Naming conventions, standardized outputs, service account usage, and structured error handling were included to support future maintenance.

---

## 🔁 Parent Flow Responsibilities

The parent flow manages the common request intake process.

Main responsibilities include:

* Reading Microsoft Forms responses
* Retrieving submitter profile information
* Creating the main request item in SharePoint
* Adding uploaded attachments to the SharePoint List item
* Identifying the request type
* Calling the appropriate child flow
* Receiving standardized child flow outputs
* Evaluating success or failure responses
* Triggering operational alerts when needed

The parent flow acts as the main orchestrator of the framework.

---

## 🧩 Child Flow Responsibilities

Each child flow is responsible for a specific business process.

The initial implementation includes separate child flows for:

* Timesheet approvals
* Expense claim approvals

Main responsibilities include:

* Retrieving the SharePoint request item
* Retrieving related attachments
* Building attachment arrays for email and approvals
* Sending approval requests to the appropriate manager
* Processing approval outcomes
* Updating SharePoint request status
* Returning standardized execution results to the parent flow
* Handling process-specific errors

Each child flow returns a standardized response, such as:

```json
{
  "result": "Success",
  "message": "Completed"
}
```

or:

```json
{
  "result": "Error",
  "message": "Approval failed."
}
```

This pattern allows the parent flow to evaluate child flow execution in a consistent way and trigger monitoring actions when needed.

---

## 📎 Attachment Handling Strategy

Attachment handling was one of the most complex parts of the solution.

The framework supports attachments across different Microsoft 365 components, including:

* Microsoft Forms file uploads
* SharePoint List attachments
* Outlook email attachments
* Power Automate Approval attachments

Different connectors require different attachment structures. The solution standardized attachment handling for each scenario and ensured that file name, content type, and file content were preserved correctly.

Special attention was given to:

* File name handling
* Content type preservation
* Base64 content handling
* SharePoint attachment creation
* Approval attachment formatting
* Email attachment formatting
* Troubleshooting corrupted attachments
* Avoiding dependency on manual file handling

This part of the project required extensive testing because attachment formats vary depending on the connector and action used.

---

## ✅ Approval and Correction Logic

The framework supports different approval outcomes and request states.

The approval logic includes:

* Manager approval
* Rejection
* Correction-required scenarios
* Resubmission for review
* SharePoint status updates
* Conditional display of actions in SharePoint
* Separate approval paths for different request types

Example request states:

```text
Under Review
Approved
Rejected
Needs Correction
```

This allows the process to behave more like a controlled business workflow instead of a simple email-based approval.

---

## 🚨 Error Handling and Monitoring

Structured error handling was included as a core part of the framework.

The flows use dedicated scopes such as:

```text
Scope - Main
Scope - Error Handling
```

To improve monitoring and troubleshooting, three standard variables are initialized across all flows:

```text
vFlowName
vEnvironment (DEV or PRD)
vArea (Sales, HR, Operations, etc.)
```

These variables are included in notifications, logs, and error messages to provide context about where an issue occurred and which business area is affected.

When an error occurs, the framework can:

* Capture relevant execution details
* Return a standardized error message
* Notify the responsible team
* Send email alerts
* Post operational messages in Microsoft Teams
* Update SharePoint request status when needed
* Improve troubleshooting through consistent messages

The objective is to avoid silent failures and make automation issues visible to the support or operations team.

---

## 📣 Teams and Email Notification Strategy

The solution uses both Outlook and Microsoft Teams for communication.

Communication and monitoring messages may include:

* Approval requests
* Approval outcomes
* Correction requests
* Error alerts
* Operational monitoring messages

Microsoft Teams is used as an internal visibility channel for automation-related messages, including alerts posted to dedicated channels such as **IT - Automation Alerts / General**.

Outlook is used for targeted notifications, approval-related communication, and service account monitoring through a shared mailbox accessible by authorized team members.

This strategy improves operational awareness and reduces dependency on manually checking the Power Automate run history.

---

## 🗂️ SharePoint Tracking and Audit Trail

SharePoint Lists are used as a lightweight process database to store request information and status.

Tracked information may include:

* Request type
* Submitter
* Submission date
* Manager or approver
* Current status
* Correction required flag
* Approval outcome
* Error message
* Last update date
* Attachment references

This provides business-level visibility into each request.

Instead of relying only on the technical Power Automate run history, users and administrators can monitor request progress through SharePoint views.

---

## 🧾 Naming Convention

A naming convention was adopted to improve readability, troubleshooting, and maintainability.

Examples of naming standards include:

```text
DEV_PA_WF_Request_Create
DEV_PA_WF_Request_Update
DEV_PA_WF_Request_Timesheet
DEV_PA_WF_Request_Expense

PA_WF_Request_Create
PA_WF_Request_Update
PA_WF_Request_Timesheet
PA_WF_Request_Expense

vFlowName
vEnvironment (DEV or PRD)
vArea (Sales, HR, Operations)

vAttachmentsEmail
vAttachmentsApproval
vResult
vMessage

Scope - Main
Scope - Error Handling
```

The naming convention clearly distinguishes development and production environments while maintaining consistency across solutions.

The goal was to make each flow component easier to understand, maintain, and reuse.

---

## 🧰 Power Platform Solution Structure

The automation was designed with future maintainability in mind.

The framework considers Power Platform solution concepts such as:

* Solution-aware flows
* Reusable child flows
* Connection references
* Environment readiness
* Future export/import possibilities
* Better lifecycle management
* Preparation for ALM practices

This helps move the development approach away from isolated flows and toward a more structured solution design.

---

## 🔐 Service Account Strategy

A service account strategy was considered to reduce dependency on personal user connections and improve operational continuity.

This approach helps ensure that:

* Flows are not dependent on a single employee account
* Connections are easier to manage
* Notifications come from a predictable identity
* Ownership is more centralized
* Long-term maintenance is more reliable
* The solution is less vulnerable to staff changes or account deactivation

Additionally, a shared mailbox associated with the service account can be used for monitoring automation alerts and maintaining visibility across the support team.

This was an important design consideration for building a more production-ready automation framework.

---

## 🔒 Security and Access Considerations

The framework considers security and access control as part of the automation design.

Key considerations include:

* SharePoint List permissions
* Access to submitted attachments
* Visibility of approval records
* Service account permissions
* Teams channel access
* Email notification recipients
* Protection of sensitive request data
* Controlled ownership of flows and connections

The objective is to balance usability, transparency, and data protection.

---

## 🧠 Lessons Learned

This project provided significant practical learning in Power Platform and Microsoft 365 automation.

Key lessons learned include:

* Low-code platforms still require strong architectural thinking.
* Attachment handling can vary significantly across Microsoft connectors.
* SharePoint, Outlook, Teams, Forms, and Approvals each handle file content differently.
* Error handling should be designed from the beginning, not added at the end.
* Parent-child flow architecture improves modularity and maintainability.
* Service accounts and naming conventions are important for production-grade automation.
* SharePoint can work as a lightweight process tracking layer for internal workflows.
* Power Automate run history is useful for technical troubleshooting, but business users need process-level tracking.
* Business users may only see the approval screen, but the real value of the solution is in the reliability, traceability, and maintainability behind the scenes.

---

## 🚀 Future Improvements

Potential future enhancements include:

* Creating a Power Apps front-end for request submission and tracking
* Building a request timeline view
* Creating a SharePoint-based flow log list
* Adding Power BI dashboards for monitoring requests and flow performance
* Improving request status visibility for submitters and managers
* Expanding the framework to other internal approval processes
* Introducing Dataverse for more structured process data management
* Implementing environment variables
* Strengthening ALM practices
* Creating reusable templates for future automation projects
* Documenting standard operating procedures for flow maintenance
* Improving exception handling and retry strategies

---

## 🛠️ Skills & Tools

* Power Platform
* Power Automate Cloud Flows
* Parent-child flow architecture
* Microsoft Forms
* SharePoint Lists
* SharePoint attachments
* Power Automate Approvals
* Outlook automation
* Microsoft Teams notifications
* Error handling and monitoring
* Service account strategy
* Naming conventions
* Workflow design
* Business process automation
* Process tracking
* Approval workflow design
* Microsoft 365 automation
* Low-code development
* Business process analysis
* Solution documentation
* Governance and maintainability
* Process improvement
* Digital transformation

---

## 📎 Confidentiality Note

This repository does not include real company data, production flow exports, internal URLs, service account details, approval records, employee information, or confidential business documents.

The content is presented as a professional case study focused on architecture, design patterns, lessons learned, and reusable automation concepts.
