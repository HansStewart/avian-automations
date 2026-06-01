AVIAN AI Automation Suite

Owner: Hans Stewart
Role: Agentic AI Marketing Lead, AVIAN
Stack: n8n, OpenAI GPT-4o, SerpAPI, Jina Reader, Gmail API
Status: Production Active
Last Updated: June 2026
Portfolio: hansstewart.dev

***

Three agentic AI systems built to automate AVIAN's industrial sales and marketing motion. Each system was architected, built, and deployed to remove a specific manual bottleneck from the commercial workflow. Together they form a connected GTM automation layer running from target identification through content generation to behavioral re-engagement.

Note on data infrastructure: All demonstration runs use Google Sheets and Airtable as lightweight mock CRM layers. In a live client deployment, these nodes are replaced with direct integrations to the client's CRM (HubSpot, Salesforce, or equivalent) via n8n's native connectors. The pipeline logic, AI agents, and scoring systems remain identical.

***

The Three Automations

Automation 01: Account Intelligence Engine
Reads a list of target industrial accounts, runs dual-source web research on each company, scores them against AVIAN's ICP across four dimensions, generates personalised outreach copy, and fires an immediate Gmail alert when a company scores 70 or above.
Folder: 01-account-intelligence-engine

Automation 02: Content Generation Pipeline
Takes a structured content brief submitted via form, scrapes AVIAN's live website to extract brand voice, routes the brief to a specialist content agent, quality-grades the output across four dimensions, and sends the draft for human approval before writing it to the content library.
Folder: 02-content-generation-pipeline

Automation 03: Re-Engagement System
Monitors a database of dormant prospects daily, calculates days since last contact, flags contacts by inactivity window and original ICP fit score, generates contextual re-engagement copy for each, and routes them into a priority-ranked follow-up queue.
Folder: 03-reengagement-system

***

Demo Results

These figures are from demonstration runs using curated test datasets. They illustrate system capability and output quality. They do not represent live production volume.

Accounts scored per cycle: 15
Top ICP score recorded: 88 out of 100
Hot leads identified (score 70 or above): 7
Content assets generated and approved per run: 3
Average content quality score: 7.8 out of 10
Dormant contacts flagged for follow-up: 6
Manual time replaced per cycle: approximately 4 hours reduced to under 4 minutes

***

Tech Stack

n8n: Workflow orchestration and automation engine
OpenAI GPT-4o and GPT-4o-mini: Research analysis, ICP scoring, content generation, quality grading
SerpAPI: Live web research including news signals, OSHA mentions, and company activity
Jina Reader: Website scraping for brand voice extraction and company research
Gmail API: Hot lead alerts and human-in-the-loop approval gate
Google Sheets: Mock CRM input and output layer (demonstration only)
Airtable: Mock CRM storage layer (demonstration only)

***

Repository Structure

01-account-intelligence-engine
  README.md
  setup.md
  results.md
  workflow.json

02-content-generation-pipeline
  README.md
  setup.md
  results.md
  workflow.json

03-reengagement-system
  README.md
  setup.md
  results.md
  workflow.json
