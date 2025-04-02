# ACP Multi Agent Software Quickstart

This guide will help you transform a simple [LangGraph application](https://langchain-ai.github.io/langgraph/tutorials/introduction/#part-1-build-a-basic-chatbot) into a robust multi-agent software, which uses the [Agent Connect Protocol (ACP)](https://spec.acp.agntcy.org/#/docs/README?id=start-a-run-of-an-agent-and-poll-for-completion) to allow communication between distributed agents. These agents run on a [Workflow Server](https://spec.acp.agntcy.org/#/docs/README?id=start-a-run-of-an-agent-and-poll-for-completion), where they are deployed and executed remotely. To make the explanation clearer, we provide a [marketing campaign manager example](https://github.com/agntcy/acp-sdk/tree/main/examples/marketing-campaign) that demonstrates how to integrate the capabilities of different agents into a unified application.


## Overview

In this tutorial, we assume you **already know the agents you wish to integrate into your system**. For our example, these are the [Mail Composer](https://github.com/agntcy/acp-sdk/tree/main/examples/mailcomposer) and [Email Reviewer](https://github.com/agntcy/acp-sdk/tree/main/examples/email_reviewer) agents. You should have access to their manifests, which are fundamental for generating the necessary data types and structures for integration.

This tutorial is structured to guide you through the following key steps:

1. [Create a Basic LangGraph Skeleton Application](#step-1-create-a-basic-langgraph-skeleton-application): Set up a LangGraph application structure to serve as the base of your multi-agent software.

2. [Generate Models from Agent Manifests](#step-2-generate-models-from-agent-manifests): Use agent manifests to generate models defining data structures and interfaces.

3. [State Definition](#step-3-state-definition): Create states to manage the flow of your multi-agent software (MAS).

4. [Multi-Agent Application Development](#step-4-multi-agent-application-development): Use ACP SDK to integrate ACP nodes into your LangGraph application.

5. [I/O Mapper Integration](#step-6-io-mapper-integration): Adjust inputs and outputs between different agents to ensure they match in format and meaning.

6. [API Bridge Integration](#step-5-api-bridge-integration): Connect natural language outputs to structured API requests.


We will use two agents whose manifests are provided within this repository:
- [Mail Composer Manifest](https://github.com/agntcy/acp-sdk/blob/main/examples/mailcomposer/deploy/mailcomposer.json)
- [Email Reviewer Manifest](https://github.com/agntcy/acp-sdk/blob/main/examples/email_reviewer/deploy/email_reviewer.json)

### What is an Agent Manifest?

An Agent Manifest is a detailed document outlining an agent's capabilities, deployment methods, data structure specifications and dependencies on other agents. It provides essential information for ensuring agents can communicate and work together effectively within the Agent Connect Protocol and Workflow Server ecosystem. [Learn more](https://docs.agntcy.org/pages/manifest.html)


## Prerequisites

- A working installation of Python 3.9 or higher
- Poetry v2 or greater
- ACP SDK: Includes a CLI for generating models, OpenAPI specifications, and validating agent manifests.



## Step 1: Create a Basic LangGraph Skeleton Application

Begin by setting up a simple LangGraph skeleton application with the following nodes:

*Start, Process Input, Mail Composer, Email Reviewer, Send Mail, and End.*


![Skeleton LangGraph Application](./marketing_campaign_skeleton.png)

This setup is a basic framework with **placeholders for each task** in the workflow. It sets the stage for transforming these nodes into remote ACP nodes, allowing the interaction with real remote agents.

The ultimate goal of this application is to compose and review emails that will be sent to a mail recipient. Each node in the workflow represents a key step in this process, from processing user input to composing the email, reviewing it, and finally sending it. This modular design ensures that each task can be independently developed, tested, and later integrated with remote agents for enhanced functionality.

### Skeleton Code Example

```python
# marketing_campaign_skeleton.py

from langgraph.graph import StateGraph, START, END
from langgraph.graph.state import CompiledStateGraph
from langchain_core.runnables.graph import MermaidDrawMethod
from pydantic import BaseModel, Field
from typing import Optional, List

# Define the overall state with placeholders for future use
class OverallState(BaseModel):
    messages: List[str] = Field([], description="Chat messages")
    has_composer_completed: Optional[bool] = Field(None, description="Flag indicating if the mail composer has successfully completed its task")
    mailcomposer_output: Optional[str] = Field(None, description="Output from Mail Composer")
    email_reviewer_output: Optional[str] = Field(None, description="Output from Email Reviewer")
    sendgrid_result: Optional[str] = Field(None, description="Result from SendGrid API call")

# Define placeholder functions for each node in the workflow
def process_input(state: OverallState) -> OverallState:
    # Placeholder logic for user inputs to mailcomposer inputs
    print("Processing input...")
    return state

def mail_composer(state: OverallState) -> OverallState:
    # Placeholder logic for composing mail
    print("Composing mail...")
    state.mailcomposer_output = "Draft email content"
    state.has_composer_completed = True
    return state

def email_reviewer(state: OverallState) -> OverallState:
    # Placeholder logic for reviewing email
    print("Reviewing email...")
    state.email_reviewer_output = "Reviewed email content"
    return state

def send_mail(state: OverallState) -> OverallState:
    # Placeholder logic for sending email
    print("Sending email...")
    state.sendgrid_result = "Email sent successfully"
    return state

# Build the state graph with placeholder nodes
def build_skeleton_graph() -> CompiledStateGraph:
    sg = StateGraph(OverallState)

    # Add placeholder nodes
    sg.add_node(process_input)
    sg.add_node(mail_composer)
    sg.add_node(email_reviewer)
    sg.add_node(send_mail)

    # Define the flow of the graph
    sg.add_edge(START, process_input.__name__)
    sg.add_edge(process_input.__name__, mail_composer.__name__)
    sg.add_edge(mail_composer.__name__, email_reviewer.__name__)
    sg.add_edge(email_reviewer.__name__, send_mail.__name__)
    sg.add_edge(send_mail.__name__, END)

    graph = sg.compile()

    # Draw the graph as a PNG in file
    graph.name = "Marketing Campaign Manager"
    with open("marketing_campaign_skeleton.png", "wb") as f:
        f.write(graph.get_graph().draw_mermaid_png(
            draw_method=MermaidDrawMethod.API,
        ))
    return graph

if __name__ == "__main__":
    graph = build_skeleton_graph()
    print("Skeleton graph compiled successfully.")
```

Follow the guide [here](https://langchain-ai.github.io/langgraph/tutorials/introduction/#part-1-build-a-basic-chatbot) to know how build a langgraph application.


## Step 2: Generate Models from Agent Manifests

In this step, you will generate models based on the agent manifests to define the **input, output and config schemas** for each agent involved in MAS. The models are created using the `acp generate-agent-models` command, which reads the agent manifest files and produces Python files that encapsulate the agent's data structures and interfaces necessary for integration.


```bash
# Install dependencies
export POETRY_VIRTUALENVS_IN_PROJECT=true
poetry install

# Activate the virtual environment
source .venv/bin/activate

# Generate models for Mail Composer
poetry run acp generate-agent-models examples/mailcomposer/deploy/mailcomposer.json --output-dir examples/marketing-campaign/src/marketing_campaign --model-file-name mailcomposer.py

# Generate models for Email Reviewer
poetry run acp generate-agent-models examples/email_reviewer/deploy/email_reviewer.json --output-dir examples/marketing-campaign/src/marketing_campaign --model-file-name email_reviewer.py
```

Generated model files:
- [Mail Composer Model](https://github.com/agntcy/acp-sdk/blob/main/examples/marketing-campaign/src/marketing_campaign/mailcomposer.py)
- [Email Reviewer Model](https://github.com/agntcy/acp-sdk/blob/main/examples/marketing-campaign/src/marketing_campaign/email_reviewer.py)

General structure:
- **Pydantic Models**: Each file includes Pydantic models that represent the **configuration**, **input**, and **output** schemas,  enforcing type validation.
- **Input, Output and Config Schemas**: These schemas handle incoming and outgoing data and the configuration of the agent.


## Step 3: State Definition

State management is fundamental to track progress and outcomes so that each agent can interact effectively with others following the right workflow. In this step, we will define the states necessary to manage the flow of your multi-agent software

### Understanding State in Multi-Agent Systems

State in multi-agent systems refers to the structured data that represents the current status of the application. It includes information about the inputs, outputs, and **intermediate results** of each agent's operations. Effective state management allows for the coordination and synchronization of agent activities.

### State Definition in the Marketing Campaign Example

In the marketing campaign example, the state is defined in [state.py](https://github.com/agntcy/acp-sdk/blob/main/examples/marketing-campaign/src/marketing_campaign/state.py). This file outlines the data structures that will be used to track the flow and results of the campaign tasks.

#### Key Components:

- **OverallState**: This is the main class that encapsulates various aspects of the state, including messages, operation logs, and completion flags for different tasks. It serves as the central repository for tracking the application's progress.

    ```python
    class OverallState(BaseModel):
        messages: List[mailcomposer.Message] = Field([], description="Chat messages")
        operation_logs: List[str] = Field([],
                                            description="An array containing all the operations performed and their result. Each operation is appended to this array with a timestamp.",
                                            examples=[["Day DD HH:MM:SS Operation performed: email sent Result: OK",
                                                    "Day DD HH:MM:SS Operation X failed"]])
        has_composer_completed: Optional[bool] = Field(None, description="Flag indicating if the mail composer has successfully completed its task")
        has_reviewer_completed: Optional[bool] = None
        has_sender_completed: Optional[bool] = None
        mailcomposer_state: Optional[MailComposerState] = None
        email_reviewer_state: Optional[MailReviewerState] = None
        target_audience: Optional[email_reviewer.TargetAudience] = None
        sendgrid_state: Optional[SendGridState] = None
    ```

- **MailComposerState, MailReviewerState, SendGridState**: These classes represent the specific states of individual components within the application. Each state class manages its own input and output data to take track of each task's progress and results.

    ```python

    from agntcy_acp.langgraph.api_bridge import APIBridgeOutput, APIBridgeInput
    from marketing_campaign import mailcomposer
    from marketing_campaign import email_reviewer

    class MailComposerState(BaseModel):
        input: Optional[mailcomposer.InputSchema] = None
        output: Optional[mailcomposer.OutputSchema] = None

    class MailReviewerState(BaseModel):
        input: Optional[email_reviewer.InputSchema] = None
        output: Optional[email_reviewer.OutputSchema] = None

    class SendGridState(BaseModel):
        input: Optional[APIBridgeInput] = None
        output: Optional[APIBridgeOutput] = None
    ```

> **Note**: The `input` and `output` fields of the `MailComposerState` and `MailReviewerState` are directly derived from the schemas generated in Step 2 using the agent manifests. This means these states are automatically created based on the manifests, and there is no need to manually define them.



## Step 4: Multi-Agent Application Development

Now, let's enhance the skeleton setup by **transforming** LangGraph nodes **into ACP nodes** using `agntcy_acp` **sdk**. ACP nodes allow network communication between agents by using the [Agent Connect Protocol (ACP)](https://spec.acp.agntcy.org/#/docs/README?id=start-a-run-of-an-agent-and-poll-for-completion).
This enables remote invocation, configuration, and output retrieval with the goal of allowing heterogeneous and distributed agents to interoperate.


### Why Use ACP Nodes?

1. **Remote Execution**: ACP nodes run on a Workflow Server, making it possible to execute tasks remotely. This is crucial for scalability and efficiency in handling complex workflows.
2. **Technology Independence**: ACP allows agents to be implemented in various technologies, such as LangGraph, LlamaIndex, etc., without compatibility issues. This gives the flexibility to choose the best agent for the specific task.
3. **Interoperability**: ACP ensures that agents can communicate and work together, regardless of the underlying technology, by adhering to a standardized protocol. [Learn more](https://spec.acp.agntcy.org/#/docs/README?id=start-a-run-of-an-agent-and-poll-for-completion)


### Add Mail Composer and Email Reviewer ACP Nodes

To integrate the Mail Composer and Email Reviewer as ACP nodes, we first need to fill in the client configuration for the remote agents.

In this example, the `ApiClientConfiguration.fromEnvPrefix` method uses the provided `PREFIX_` (e.g., `MAILCOMPOSER_` or `EMAIL_REVIEWER_`) to automatically create configuration values like `PREFIX_ID` and `PREFIX_API_KEY` by reading them from the environment variables. This simplifies the setup process.

```python
# Fill in client configuration for the remote agent
MAILCOMPOSER_AGENT_ID = os.environ.get("MAILCOMPOSER_ID", "")
EMAIL_REVIEWER_AGENT_ID = os.environ.get("EMAIL_REVIEWER_ID", "")
SENDGRID_HOST = os.environ.get("SENDGRID_HOST", "http://localhost:8080")
MAILCOMPOSER_CLIENT_CONFIG = ApiClientConfiguration.fromEnvPrefix("MAILCOMPOSER_")
EMAIL_REVIEWER_CONFIG = ApiClientConfiguration.fromEnvPrefix("EMAIL_REVIEWER_")
```

Next, we define the ACP nodes for the Mail Composer and Email Reviewer. The `_path` fields indicate where to find the input and output in the `OverallState`, while the `_type` fields specify the type of the input and output schemas.

### Mail Composer ACP Node

```python
acp_mailcomposer = ACPNode(
    name="mailcomposer",
    agent_id=MAILCOMPOSER_AGENT_ID,
    client_config=MAILCOMPOSER_CLIENT_CONFIG,
    input_path="mailcomposer_state.input",
    input_type=mailcomposer.InputSchema,
    output_path="mailcomposer_state.output",
    output_type=mailcomposer.OutputSchema,
)
```

### Email Reviewer ACP Node

```python
acp_email_reviewer = ACPNode(
    name="email_reviewer",
    agent_id=EMAIL_REVIEWER_AGENT_ID,
    client_config=EMAIL_REVIEWER_CONFIG,
    input_path="email_reviewer_state.input",
    input_type=email_reviewer.InputSchema,
    output_path="email_reviewer_state.output",
    output_type=email_reviewer.OutputSchema,
)
```

Finally, replace the placeholder nodes in the state graph with the newly defined ACP nodes:

```python
sg.add_node(acp_mailcomposer)
sg.add_node(acp_email_reviewer)
```

