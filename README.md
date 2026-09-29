# Employee Leave Request Approval System

## Overview

This project models an automated Employee Leave Request Approval System for XYZ Corp using **Camunda 8**, **BPMN 2.0**, and **DMN**.

The process handles Casual, Sick, and Earned leave requests and determines whether a request should be automatically approved, routed to the employee's manager, or rejected due to insufficient leave balance.

## BPMN Process

The process follows these steps:

1. The employee submits a leave request containing the leave type and number of days.
2. The request is validated.
3. The employee's available leave balance is checked.
4. A DMN Business Rule Task evaluates the leave request.
5. If the balance is insufficient, the request is immediately rejected with an "Insufficient Balance" notification.
6. Sick leave of 3 days or fewer with sufficient balance is automatically approved.
7. Casual leave, Earned leave, and Sick leave exceeding 3 days are routed to the employee's direct manager.
8. If the manager rejects the request, the employee is notified and the process ends.
9. If approved, the request is forwarded to HR.
10. The employee's leave balance is updated.
11. The employee is notified of the approval.

## DMN Decision Table

The DMN decision table is named **Leave Approval Decision**.

### Input Variables

* `leaveType`
* `leaveDays`
* `availableBalance`

### Output Variable

* `decision`

Possible decision results are:

* `INSUFFICIENT_BALANCE`
* `AUTO_APPROVE`
* `MANAGER_REVIEW`

The DMN uses the **First Hit Policy** so that the insufficient-balance rule takes priority over the other approval rules.

## Service Tasks and User Tasks

### Service Tasks

The following activities are automated:

* Validate Leave Request
* Check Leave Balance
* Auto Approve Leave
* Reject – Insufficient Balance
* Notify Employee – Manager Rejected
* Forward Request to HR
* Update Leave Balance
* Notify Employee – Leave Approved

### Business Rule Task

**Evaluate Leave Decision** is implemented as a Business Rule Task that invokes the Leave Approval Decision DMN.

### User Task

**Manager Approval** is a User Task because the direct manager must manually review and approve or reject the leave request.

## Process Variables

The main process variables are:

```text
employeeId
employeeName
leaveType
leaveDays
availableBalance
decision
managerApproved
managerComments
leaveStatus
```

The DMN receives `leaveType`, `leaveDays`, and `availableBalance` as inputs and returns the `decision` result.

## DMN Design Justification

A single DMN decision table is used because the business rules are closely related and produce one routing decision.

The decision table evaluates the leave type, requested number of days, and available leave balance together. Its output determines whether the request should be rejected for insufficient balance, automatically approved, or sent for manager review.

The **First Hit Policy** ensures that an insufficient-balance condition is evaluated before the auto-approval or manager-review rules.

Using a single decision table keeps the business logic centralized and makes the BPMN process simpler and easier to maintain. Multiple DMN tables could be used for separate balance and routing decisions, but they would add unnecessary complexity for this workflow.

## Repository Structure

```text
Employee-Leave-Approval/
│
├── README.md
│
├── bpmn/
│   └── employee-leave-approval.bpmn
│
└── dmn/
    └── leave-approval-decision.dmn
```

## Tools Used

* Camunda 8 Web Modeler
* BPMN 2.0
* DMN
* GitHub
