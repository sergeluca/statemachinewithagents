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

<img width="824" height="525" alt="image" src="https://github.com/user-attachments/assets/9cc5791e-60d4-4cce-bdb8-44bd8f3f5b0e" />


### Pattern and implementation


Our intuition is that an LLM should be able to orchestrate which state should be executed, and that an automous agent would be the host for that intuition.
The idea is to create an autonomous agent with Copilot Studio and Power Automate.
The pattern we are proposing and testing is very simple : each state of the state machine is mapped to a topic in a Copilot Studio agent as illustrated here:

<img width="1099" height="629" alt="image" src="https://github.com/user-attachments/assets/808e3d36-e7ad-45e9-b5a6-45b31691a465" />

Since we a creating an agent, we must feed this agent with some instructions describing the state machine:

User has submitted new content:

- Transition to: "Waiting for Advisors to validate the content"
- The identification of the record is passed as a parameter named 'RecordID' that will be used in many topics. This RecordID must be carefully saved to be reused across the agent.

Advisors validate content:
- If approved by Advisors: Transition to "Waiting for Secretariat to validate the style"
- If rejected by Advisors: Transition to "Waiting for User to update the content"

User updates the content:
- Transition back to "Waiting for Advisors to validate the content"

Secretariat validates style:
- If approved by Secretariat: Transition to "Waiting for Boss to validate publication"
- If rejected by Secretariat: Transition to "Waiting for User to update the style"

User updates the style:
- Transition back to "Waiting for Secretariat to validate the style"

Boss validates the publication:
- If approved by Boss: Transition to Content is published
- If rejected by Boss: Transition to "Waiting for User to update the content"; in this specific case only the previous approvals by Advisors, Secretariat and Boss are not valid anymore.



Do not transition directly from Advisors to "Boss authorizes publication"  

### Architecture

As illustrated below, trigger flows send messages to the orchestrator.

These messages can represent the following events:

- New content has been created
- Advisors have approved or rejected the content
- Secretariat has approved or rejected the style
- Boss has approved or rejected the publication
- The user has updated the content or style


<img width="1520" height="1026" alt="image" src="https://github.com/user-attachments/assets/0bd1d8f8-d963-409b-978a-ed9cf530775c" />

Based on these messages, the orchestrator decides which topic to execute:

<img width="1878" height="1059" alt="image" src="https://github.com/user-attachments/assets/42350e01-fd18-4825-a66c-24125384d384" />



Some topics will start an approval, and for this we have created an generic approval flow:

<img width="1887" height="1064" alt="image" src="https://github.com/user-attachments/assets/3006919b-121f-4e0e-9979-58d4a8213b3d" />







