# statemachinewithagents
Description of a poc of a state machine with Power Automate and Copilot Studio agent

## State Machine in Power Automate

State machines are difficult to implement in Power Automate because the platform does not natively support the concept of **state**. Additionally, there is no equivalent to a `goto` mechanism, which makes handling complex transitions and loops challenging.

To illustrate a state machine, consider the following real-world example.

### Use Case (2018)

A government organization was responsible for publishing medical documentation for citizens. Due to the sensitive nature of the content, each document had to pass through a strict validation workflow.

### Workflow Description

For a better understanding, take a look at the state diagram in the next section.

1. A user submits a new document.
2. The content is reviewed by a group of experts called **Advisors**:
   - If **rejected**, the document is returned to the user for updates.
   - If **approved**, the process continues.
3. The document style is reviewed by the **Secretariat**:
   - If **rejected**, the user must update the style and resubmit.
   - If **approved**, the document moves to final validation.
4. The **Boss** performs the final review:
   - If **approved**, the document is published.
   - If **rejected**, the document is not published.
  
### Design Consideration

The approval logic may evolve over time. Therefore, the implementation should be designed to **minimize the impact of such changes**.


### State Machine Diagram

```mermaid
flowchart TD
    A[User submits new content] --> B[Advisors validate content]

    B -->|Approved| C[Secretariat validates style]
    B -->|Rejected| D[User updates content]
    D --> B

    C -->|Approved| E[Boss authorizes publication]
    C -->|Rejected| F[User updates style]
    F --> C

    E -->|Approved| G[Content is published]
    E -->|Rejected| H[Content is not published]

