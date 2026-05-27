# Lab 01: Data, context, and tools at scale

In this lab you are going to experiment how to use Work IQ to consume data, context, and tools at scale.

## Understanding Work IQ

All of us have data spread across Microsoft 365, Dataverse, and many line-of-business (LOB) systems.
LLMs can use that data to generate answers and reasoning, but by themselves they do not understand who you are, how your organization works, or what matters most to your projects.

In Microsoft 365, your day-to-day activity creates valuable context: meetings, emails, Teams conversations, and interactions with Microsoft 365 and Microsoft 365 Copilot.
Work IQ can use that context to understand your preferences, your working style, and how you want responses to be delivered.

On top of this foundation, skills and tools enable agents to provide more relevant answers and perform actions in ways that match your habits and expectations.
This is Work IQ: your organization’s intelligence, unlocked for every agent.

![The architecture of Work IQ with Data, Context, and Skills & Tools](../../../images/work-iq/WorkIQ-Architecture.png)

From an architectural perspective, Work IQ consists of:
- **Data**: Combines everyday work signals with external enterprise data, including Microsoft 365 activity and LOB systems, to give Work IQ real-time awareness.
- **Context**: Uses memory to learn your style, preferences, habits, and workflows, so it can understand your patterns over time.
- **Skills & Tools**: Provides specialized capabilities and instructions that tailor agents for specific tasks and outcomes.

## Understanding Work IQ API

Work IQ API is the common API layer that exposes Microsoft organizational intelligence to developers, partners, and enterprise applications.

At a high level, the platform follows three core principles:

- **Unified Surface**: One API contract supports all use cases, whether they are driven by people or by agents.
- **Response Fidelity**: API responses mirror the interactive experience, including reasoning quality, data grounding, formatting, and capabilities.
- **Multi-Protocol Runtime**: REST, A2A, and MCP are different protocol heads on the same runtime orchestration layer.

From a security and compliance perspective, Work IQ is designed to operate safely by default:

- Information is always security-trimmed.
- Access is delegated and always scoped to user context.
- Requests always honor tenant boundaries, sensitivity labels, and governance policies.

When choosing a protocol, use the pattern that matches your scenario:

- **A2A (Agent-to-Agent)**: Use when one agent delegates tasks to another agent, for example when an external HR agent requests Microsoft 365 context from a BizChat agent.
- **MCP (Agent-to-Tool)**: Use when an orchestrator agent, such as GitHub Copilot, needs organization context and calls BizChat through Work IQ as a tool.
- **REST (Human-to-Agent)**: Use when building intelligence into a web or mobile application via API, where an app asks questions and receives responses from an agent.

In practice, these options are complementary. You can combine them in larger multi-agent orchestrations while preserving a consistent intelligence layer and policy model.

## Consuming Work IQ

### Start Work IQ CLI

You can run the following scripts directly from a terminal session, for example using the PowerShell terminal or the Bash terminal, depending on the platform where you are running this cookbook.

Run the following command, to accept the end user license agreement, if you haven't done it yet:

```
workiq accept-eula
```

Now run the following command:

```
workiq ask -q "Who am I? What is my role in the company?"
```

It will give you back information about who you are in your own Microsoft 365 tenant.
Now let's try with the following prompt:

```
workiq ask -q "When is my next meeting?"
```

Work IQ CLI will give you back detailed information about your next and upcoming meeting, based on your personal agenda in Microsoft 365.

---

### Use Work IQ in GitHub Copilot CLI

You can now run Work IQ inside GitHub Copilot CLI via MCP protocol.

Run the following command, to start GitHub Copilot

```
copilot --banner
```

Enjoy the beauty of the GitHub Copilot welcome banner. If prompted, trust GitHub Copilot to access the files in your current folder.

Now, run the following prompt:

```
Who am I? What is my role in the company?
```

GitHub Copilot will analyze your prompt and determine that the **ask_work_iq** MCP tool is needed to provide you with an answer. As such, it will ask your consent to use the tool. You can say **1. Yes** to consent it just once, or **2. Yes, and don't ask again for tool "ask_work_iq" from "workiq" in this repo** if you want to consent it from now on. Of course, you can also choose **3. No** and avoid using Work IQ.

If you consented, GitHub Copilot will start processing your request and will talk with the Work IQ MCP server to extract to requested information.

Now let's try with the following prompt:

```
When is my next meeting?
```

GitHub Copilot CLI will still rely on Work IQ MCP to give you back detailed information about your next and upcoming meeting, based on your personal agenda in Microsoft 365.

---

### Use Work IQ via REST

Another option you have, is to consume Work IQ via REST API. In order to try this option, you are going to use the Microsoft Graph Explorer web interface.

#### Create a new Copilot Chat Conversation

- Open [Graph Explorer](https://aka.ms/ge)  
- Sign into your target tenant
- Open the **Modify Permissions** tab
- Grant the following permissions to the Graph Explorer application: *Content-Type: application/json*, *Mail.Read*, *People.Read.All*, *OnlineMeetingTranscript.Read.All*, *Chat.Read*, *ChannelMessage.Read.All*, *ExternalItem.Read.All*
- Paste the query below 
- Click **Run query**

```http
POST https://graph.microsoft.com/beta/copilot/conversations
Content-Type: application/json

{}
```

- The response you get, should look like the following one:

```
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": "0d110e7e-2b7e-4270-a899-fd2af6fde333",
  "createdDateTime": "2025-09-30T15:28:46.1560062Z",
  "displayName": "",
  "status": "active",
  "turnCount": 0
}
```

Copy the value of the **id** attribute. It represents the unique **conversationId** of your Copilot Chat conversation.

#### Send a Chat Message within the current Conversation

- Still in the user experience of [Graph Explorer](https://aka.ms/ge) 
- Paste the query below and replace the {conversationId} placeholder with the value you just copied
- Click **Run query**

```http
POST https://graph.microsoft.com/beta/copilot/conversations/{conversationId}/chat
Content-Type: application/json

{
  "message": {
    "text": "Who am I? What is my role in the company?"
  },
  "locationHint": {
    "timeZone": "America/New_York"
  }
}
``` 

- The response should be a complex JSON object with the information about who you are and what your role is in your company.

---

## Wrap up

As you can see, regardless if you use the Work IQ CLI, Work IQ A2A, Work IQ MCP tools, or low level REST API request you will always get back a consistent response.

## Where to go next

- **Explore the IQ Series** — Continue learning with [Episode 2: A2A for Context‑Aware, Agentic Experiences](../../2-Work-IQ-A2A-for-Context‑Aware-Agentic-Experiences/).