# P5 – Simple Support System

**Important:** **Copy your workflow form P3** and **paste it into the workflow with the Q&A Chatbot (P4)**. 

---

## Workflow Overview

**Purpose**
This workflow demonstrates a simple version of an internal IT support chat system that is able to solve simple questions automatically while being able to escalate issues when necessary.

Incoming chat messages are automatically classified and either:

* handled directly by an AI chatbot, or
* escalated into a structured IT support ticket for human follow-up.


---

### Q&A Chatbot Agent

**Add to the System Prompt**

Add the following part to the system prompt of your Q&A Chatbot (for example after the Response guidelines and examples)

```
### **Escalation Rule:**
If the request includes **Keywords or topics** such as:
- *hardware*, *broken*, *damaged*, *replacement*, *repair*
- *network down*, *server*, *outage*, *system crash*
- *admin rights*, *installation*, *permissions*, *access denied*
- *security*, *breach*, *virus*, *phishing*, *data loss*
- *urgent*, *critical*, *can’t work*, *system not starting*
- *new equipment*, *device setup*, *hardware request*
- *custom software* like NovaCRM
- *user demands human assistance*
DO NOT ANSWER the question. 

Instead, respond with "CODE: ESCALATION"
```

### If Node

`{{ $json.output }}` contains `CODE: ESCALATION`

### AI Agent

**Prompt:**

```
Summarize the user issue and return JSON with the user name and a short issue description.
```

- Require Specific Output Format: `True`
- Memory: *Connect to existing memory*
- Model: *Connect to existing chat model*

**Structured Output Parser – Generate from Example**
```
{
  "Issue": "My laptop broke",
  "Name": "Tobias"
}
```

- Rename to: **Summary Agent**

### Set ID Node (Update)

- `ID`
- Type: String
- `{{ $now.toDateTime().ts.toString(36).toUpperCase() }}`


- `submittedAt`
- Type: String
- `{{$now}}`


- `Issue`
- Type: String
- `{{ $json.output.Issue }}`


- `Your Name`
- Type: String
- `{{ $json.output['Name'] }}`


### Edit Fields Node (New)

- `output`
- String
```
{{ $('Q&A Chatbot').item.json.output }}

Your Ticket ID is {{ $('Set ID').item.json.ID }}
```
