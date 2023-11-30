---
type: management
keywords:
- Software Engineering
- Process
---
# Software Engineering processes

## Simple process

```mermaid
---
title: Simple process
---
flowchart LR
    BL(Backlog)
    TODO(To do)
    IP(In progress)
    DONE(Done or Cancelled)

    BL --Select for development--> TODO
    TODO --Start development--> IP
    IP --Resolve/Complete--> DONE
    IP --Reject/Cancel-->DONE
    IP --Postpond-->BL
    IP --Postpond-->TODO
    BL --Reject/Cancel-->DONE
    TODO --Reject/Cancel-->DONE
```

| Backlog                               | To do                                                      | In progress | Done                                              |
| ------------------------------------- | ---------------------------------------------------------- | ----------- | ------------------------------------------------- |
| All the requests, or the requirements | Tasks are decided to do<br/>in this iteration or this time | Doing tasks | Resolved tasks as Done, or Rejected, or Cancelled |

## Product-Engineering process

### Flow for each team

```mermaid
---
title: Product-Engineering process
---
flowchart LR
    BL(Backlog)
    TODO(To do)
    IP(In progress)
    REVIEW(In Review)
    DONE(Done or Cancelled)

    BL --Select for development--> TODO
    TODO --Start development--> IP
    IP --Start review--> REVIEW
    IP --Reject/Cancel-->DONE
    IP --Postpond-->BL
    IP --Postpond-->TODO
    REVIEW --Review rejected--> IP
    REVIEW --Accept--> DONE
    BL --Reject/Cancel-->DONE
    TODO --Reject/Cancel-->DONE
```

| Backlog                               | To do                                                      | In progress | Done                                              |
| ------------------------------------- | ---------------------------------------------------------- | ----------- | ------------------------------------------------- |
| All the requests, or the requirements | Tasks are decided to do<br/>in this iteration or this time | Doing tasks | Resolved tasks as Done, or Rejected, or Cancelled |

### Full flow

```mermaid
---
title: Product-Engineering process (Full flow)
---
flowchart LR
    P(Team Product)
    D(Team Software developer)
    QC(Team QC)

    P --(1a) Provide PRD--> D
    P --(1b) Provide PRD--> QC
    D --(2) Provide Builds--> QC
    QC --(3a) Test reports--> D
    QC --(3b) Test reports--> P
    D --(4) Demo --> P
    P --(5) Decide to release --> D
    D --(6) Release --> D
```

| Team       | Input                      | Work               | Ouput      |
| ---------- | -------------------------- | ------------------ | ---------- |
| Product    | Stakeholder's requirements | Analyze & Document | PRD        |
| Developers | PRD                        | Implement          | Builds     |
| QC         | PRD                        | Write test cases   | Test cases |
| QC         | Builds                     | Test               | Reports    |
