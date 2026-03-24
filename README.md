# AI Workflow Templates for n8n + Ollama

> **Production-ready n8n workflow templates powered by local AI. No API keys, no cloud costs, no data leaving your machine.**

[![Get Full Pack](https://img.shields.io/badge/Get_All_11-$29-667eea?style=for-the-badge)](https://bonskari.github.io/n8n-workflow-pack/)
[![Free Sample](https://img.shields.io/badge/Free_Sample-Download-4caf50?style=for-the-badge)](https://github.com/bonskari/n8n-ai-workflows/raw/main/samples/ai-blog-writer.json)

## Free Sample: AI Blog Writer Pipeline

Download the **AI Blog Writer** template to test before buying:

**[Download Free Sample](https://github.com/bonskari/n8n-ai-workflows/raw/main/samples/ai-blog-writer.json)**

Import into n8n → Enter a topic → Get a polished 1500-word blog post. Uses a 4-stage AI pipeline: Research → Outline → Draft → Edit.

### How to Import

1. Download the JSON file above
2. Open n8n → Workflows → Import from File
3. Select the downloaded JSON
4. Make sure Ollama is running: `ollama pull llama3:8b`
5. Click "Execute Workflow" and enter your topic

## Full Pack: 11 Workflows ($29)

The complete pack includes 11 production-ready workflows:

### Content & Marketing
- **AI Blog Writer Pipeline** — 4-stage content pipeline *(free sample above)*
- **AI Social Media Content Generator** — One topic → Twitter, LinkedIn, Reddit, Instagram
- **AI YouTube-to-Newsletter Pipeline** — YouTube URL → email newsletter in 60 seconds
- **AI Multi-Platform Content Repurposer** — One post → 6 platform-optimized versions

### Sales & Business
- **AI Lead Scoring & Enrichment** — Webhook-triggered, score 1-100, auto-route hot leads
- **AI Competitor Intelligence Monitor** — Weekly autopilot competitive analysis
- **AI Email Auto-Responder** — Classify, filter spam, draft replies every 5 minutes
- **AI Support Ticket Router** — Classify, prioritize, auto-reply, escalate

### Productivity
- **AI Document Summarizer** — Webhook API for document summaries + Q&A pairs
- **AI Meeting Notes Summarizer** — Transcripts → structured summaries + action items
- **AI Data Extractor** — Unstructured text → clean JSON with AI verification

**[Get the Full Pack →](https://bonskari.github.io/n8n-workflow-pack/)**

## Why Self-Hosted AI?

| | Cloud AI (OpenAI) | Self-Hosted (Ollama) |
|---|---|---|
| Monthly cost | $20-200+ | $0 |
| Data privacy | Sent to cloud | Stays on your machine |
| Rate limits | Yes | None |
| Works offline | No | Yes |
| GDPR compliant | Complicated | By default |

## Requirements

- [n8n](https://n8n.io) self-hosted (v1.0+)
- [Ollama](https://ollama.ai) running on localhost:11434
- Any Ollama model (`llama3:8b` recommended, any model works)
- 8GB+ RAM recommended

## Support

Questions? Open an issue or email admin@unstableentity.com
