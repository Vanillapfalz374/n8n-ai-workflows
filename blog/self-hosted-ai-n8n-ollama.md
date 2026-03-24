# How to Build Self-Hosted AI Workflows with n8n + Ollama

*A practical guide to running AI automation on your own hardware -- no API keys, no monthly bills, no data leaving your machine.*

---

## Why Self-Hosted AI Matters

Every time you send data to OpenAI, Anthropic, or Google's APIs, three things happen:

1. **Your data leaves your control.** Customer emails, internal documents, support tickets -- all sent to third-party servers.
2. **You pay per token.** A single workflow processing 100 emails/day can cost $50-200/month on GPT-4.
3. **You depend on uptime you don't control.** Rate limits, outages, and API deprecations can break your automation without warning.

Self-hosted AI with Ollama eliminates all three problems:

- **Privacy**: Data never leaves your machine. GDPR compliance is the default, not a configuration exercise.
- **Cost**: $0/month after hardware. Run unlimited requests against models like Llama 3, Mistral, or Phi-3.
- **Reliability**: No rate limits, no API keys to rotate, works offline.

The tradeoff is that local models are smaller than GPT-4. But for structured tasks like classification, summarization, data extraction, and drafting -- the ones you actually want to automate -- a well-prompted 8B parameter model running locally is more than sufficient.

---

## How n8n + Ollama Work Together

[n8n](https://n8n.io) is an open-source workflow automation tool (self-hosted alternative to Zapier). [Ollama](https://ollama.ai) is a local LLM runner that exposes models via a REST API on `localhost:11434`.

The integration is straightforward: n8n's HTTP Request node calls Ollama's `/api/generate` endpoint. No special plugins needed.

**Architecture:**

```
[Trigger] --> [n8n Workflow] --> [HTTP Request to Ollama] --> [Process Response] --> [Output]
                                        |
                                  localhost:11434
                                   (Llama 3, etc.)
```

### Setup (5 minutes)

**1. Install Ollama:**

```bash
curl -fsSL https://ollama.ai/install.sh | sh
ollama pull llama3:8b
```

**2. Install n8n:**

```bash
docker run -d --name n8n -p 5678:5678 \
  --add-host=host.docker.internal:host-gateway \
  -v n8n_data:/home/node/.n8n \
  n8nio/n8n
```

The `--add-host` flag is critical if n8n runs in Docker -- it lets n8n reach Ollama on the host machine. Inside your workflows, use `http://host.docker.internal:11434` instead of `localhost`.

**3. Verify Ollama is reachable:**

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3:8b",
  "prompt": "Say hello in one sentence.",
  "stream": false
}'
```

You should get a JSON response with a `response` field. If that works, you're ready.

---

## Free Example: Email Classifier Workflow

Here's a complete, working n8n workflow that classifies incoming emails using Ollama. Import the JSON below directly into n8n.

**What it does:**

1. Checks for unread emails every 5 minutes (IMAP)
2. Sends each email to Ollama for classification (URGENT, QUESTION, FEEDBACK, SPAM, OTHER)
3. Filters out spam
4. Drafts a context-aware reply using Ollama
5. Outputs the draft for your review

### The Workflow JSON

Copy this entire block and import it via **n8n > Workflows > Import from JSON**:

```json
{
  "name": "AI Email Classifier + Auto-Responder (Ollama)",
  "nodes": [
    {
      "parameters": {
        "rule": {
          "interval": [{ "field": "minutes", "minutesInterval": 5 }]
        }
      },
      "id": "schedule",
      "name": "Check Every 5 Minutes",
      "type": "n8n-nodes-base.scheduleTrigger",
      "typeVersion": 1.2,
      "position": [240, 300]
    },
    {
      "parameters": {
        "operation": "getAll",
        "returnAll": false,
        "limit": 10,
        "filters": {
          "readStatus": "unread"
        }
      },
      "id": "get-emails",
      "name": "Get Unread Emails (IMAP)",
      "type": "n8n-nodes-base.emailReadImap",
      "typeVersion": 2,
      "position": [460, 300],
      "credentials": {
        "imap": {
          "id": "REPLACE_WITH_YOUR_IMAP_CREDENTIAL_ID",
          "name": "Your IMAP Account"
        }
      }
    },
    {
      "parameters": {
        "url": "http://localhost:11434/api/generate",
        "sendBody": true,
        "specifyBody": "json",
        "jsonBody": "={{ JSON.stringify({ model: 'llama3:8b', prompt: 'Classify this email into one of these categories: URGENT, QUESTION, FEEDBACK, SPAM, OTHER.\\n\\nFrom: ' + $json.from + '\\nSubject: ' + $json.subject + '\\nBody: ' + ($json.text || '').substring(0, 1000) + '\\n\\nRespond with ONLY the category name, nothing else.', stream: false, options: { temperature: 0.1, num_predict: 20 } }) }}",
        "options": { "timeout": 60000 }
      },
      "id": "classify",
      "name": "Classify Email (Ollama)",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.2,
      "position": [680, 300]
    },
    {
      "parameters": {
        "conditions": {
          "options": { "caseSensitive": false },
          "combinator": "or",
          "conditions": [
            {
              "leftValue": "={{ JSON.parse($json.data).response.trim() }}",
              "rightValue": "SPAM",
              "operator": { "type": "string", "operation": "equals" }
            }
          ]
        }
      },
      "id": "filter-spam",
      "name": "Filter Spam",
      "type": "n8n-nodes-base.filter",
      "typeVersion": 2,
      "position": [900, 300]
    },
    {
      "parameters": {
        "url": "http://localhost:11434/api/generate",
        "sendBody": true,
        "specifyBody": "json",
        "jsonBody": "={{ JSON.stringify({ model: 'llama3:8b', prompt: 'Write a professional, helpful email reply to this message. Be concise and friendly.\\n\\nFrom: ' + $('Get Unread Emails (IMAP)').item.json.from + '\\nSubject: ' + $('Get Unread Emails (IMAP)').item.json.subject + '\\nBody: ' + ($('Get Unread Emails (IMAP)').item.json.text || '').substring(0, 2000) + '\\n\\nWrite ONLY the reply body (no subject line, no greeting format instructions). Sign off as the team.', stream: false, options: { temperature: 0.5, num_predict: 1000 } }) }}",
        "options": { "timeout": 120000 }
      },
      "id": "draft-reply",
      "name": "Draft Reply (Ollama)",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.2,
      "position": [1120, 300]
    },
    {
      "parameters": {
        "assignments": {
          "assignments": [
            {
              "id": "draft",
              "name": "draft_reply",
              "value": "={{ JSON.parse($json.data).response }}",
              "type": "string"
            },
            {
              "id": "original_from",
              "name": "original_from",
              "value": "={{ $('Get Unread Emails (IMAP)').item.json.from }}",
              "type": "string"
            },
            {
              "id": "original_subject",
              "name": "original_subject",
              "value": "={{ 'Re: ' + $('Get Unread Emails (IMAP)').item.json.subject }}",
              "type": "string"
            },
            {
              "id": "category",
              "name": "category",
              "value": "={{ JSON.parse($('Classify Email (Ollama)').item.json.data).response.trim() }}",
              "type": "string"
            }
          ]
        }
      },
      "id": "prepare-output",
      "name": "Prepare Draft for Review",
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.4,
      "position": [1340, 300]
    }
  ],
  "connections": {
    "Check Every 5 Minutes": {
      "main": [[{ "node": "Get Unread Emails (IMAP)", "type": "main", "index": 0 }]]
    },
    "Get Unread Emails (IMAP)": {
      "main": [[{ "node": "Classify Email (Ollama)", "type": "main", "index": 0 }]]
    },
    "Classify Email (Ollama)": {
      "main": [[{ "node": "Filter Spam", "type": "main", "index": 0 }]]
    },
    "Filter Spam": {
      "main": [[{ "node": "Draft Reply (Ollama)", "type": "main", "index": 0 }]]
    },
    "Draft Reply (Ollama)": {
      "main": [[{ "node": "Prepare Draft for Review", "type": "main", "index": 0 }]]
    }
  },
  "settings": { "executionOrder": "v1" },
  "tags": [{ "name": "AI" }, { "name": "Ollama" }, { "name": "Email" }]
}
```

### How to Use It

1. Import the JSON into n8n
2. Set up your IMAP credentials (Settings > Credentials > Add IMAP)
3. If n8n runs in Docker, replace `localhost` with `host.docker.internal` in the Ollama URLs
4. Activate the workflow

### Key Implementation Details

**Why `temperature: 0.1` for classification?** Low temperature makes the model deterministic -- you want consistent category labels, not creative ones. For drafting replies, `temperature: 0.5` adds enough variation to sound natural.

**Why `num_predict: 20` for classification?** The model only needs to output one word (the category). Limiting output tokens prevents it from rambling and speeds up response time.

**Why substring the email body?** Ollama models have context windows (typically 4096-8192 tokens). Truncating to 1000-2000 characters keeps you safely within limits while capturing the important content.

**Parsing the response:** Ollama returns `{"response": "URGENT", ...}`. The workflow uses `JSON.parse($json.data).response.trim()` to extract the clean category label.

---

## Going Further: 10 More Workflows You Can Build

The email classifier is one pattern. The same n8n + Ollama architecture supports dozens of use cases:

- **Content generation**: Blog posts, social media, newsletters
- **Lead processing**: Scoring, enrichment, routing
- **Document processing**: Summarization, data extraction, Q&A generation
- **Support automation**: Ticket classification, sentiment analysis, auto-replies
- **Competitive intelligence**: Automated monitoring and analysis

Building each of these from scratch takes hours of prompt engineering, error handling, and testing edge cases.

---

## What's in the Full Pack

The **Self-Hosted AI Workflow Pack** includes 11 production-ready n8n workflows, all powered by Ollama:

### Content & Marketing
1. **AI Blog Writer Pipeline** -- Enter a topic, get a polished 1500-word post. 4-stage pipeline: research, outline, draft, edit.
2. **AI Social Media Content Generator** -- One topic becomes optimized posts for Twitter, LinkedIn, Reddit, and Instagram.
3. **AI YouTube-to-Newsletter Pipeline** -- Paste a YouTube URL, get a formatted email newsletter in 60 seconds.
4. **AI Multi-Platform Content Repurposer** -- Feed in one blog post, get back six platform-optimized versions.

### Sales & Business
5. **AI Lead Scoring & Enrichment** -- Webhook-triggered. Scores leads 1-100, enriches with AI, auto-routes hot leads.
6. **AI Competitor Intelligence Monitor** -- Weekly autopilot. Scrapes competitors, AI-analyzes changes, delivers strategic briefings.
7. **AI Email Auto-Responder** -- Classifies emails, filters junk, drafts context-aware replies. Runs every 5 minutes.
8. **AI Support Ticket Router** -- Classifies tickets by category/priority, detects sentiment, auto-replies to simple questions.

### Productivity
9. **AI Document Summarizer** -- Webhook API. Send any document, get a summary and auto-generated Q&A pairs.
10. **AI Meeting Notes Summarizer** -- Paste transcripts, get structured summaries with action items and follow-up emails.
11. **AI Data Extractor** -- Extract structured JSON from unstructured text. Define your schema, send text, get clean data.

Every workflow includes:
- Complete n8n JSON (import and go)
- In-workflow documentation via sticky notes
- Tested with Llama 3 8B (works with any Ollama model)
- Prompts tuned for reliable, structured output

### Get the Full Pack

**$39 -- one-time purchase. No subscriptions. No API costs. Ever.**

[Get the Self-Hosted AI Workflow Pack](https://bonskari.github.io/n8n-ai-workflows/)

30-day money-back guarantee. Instant download after purchase.

---

## Wrapping Up

Self-hosted AI with n8n + Ollama is the practical path for anyone who wants AI automation without vendor lock-in, recurring costs, or data privacy headaches. The email classifier above is a working starting point -- import it, tweak it, and see what local AI can do.

If you want to skip the hours of prompt engineering and workflow building, the full pack of 11 templates is ready to import and run.
