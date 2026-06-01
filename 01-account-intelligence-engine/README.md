Account Intelligence Engine v1.2

Owner: Hans Stewart
Status: Production Active
Trigger: Manual execution and daily schedule at 07:00
Cycle time: Under 4 minutes for 15 accounts
Last Updated: June 2026

***

What It Does

Reads a list of target industrial accounts from a data source, runs dual-source web research on each company using live search data and website scraping, scores them against AVIAN's Ideal Customer Profile across four weighted dimensions, generates personalised outreach copy for every hot account, and sends an immediate Gmail alert to the sales contact when a company scores 70 or above.

Without this system, manually researching, scoring, and writing outreach for 15 accounts takes approximately 3 to 4 hours per cycle. This system completes the same work in under 4 minutes.

***

Pipeline Architecture

The workflow runs in the following sequence for every company in the input list.

Step 1: Trigger
The workflow is initiated either manually via the Execute Workflow button or automatically via a daily schedule trigger set to 07:00.

Step 2: Read Account List
All target companies are pulled from the input data source. Each row contains Company Name, Website, and Industry.

Step 3: Loop Over Items
The SplitInBatches node processes one company at a time and loops back after each company is fully processed before moving to the next.

Step 4: Dual-Source Research
Two research nodes run simultaneously for each company. The SerpAPI Web Research node pulls live search results filtered for fire safety, OSHA activity, and company news. The Jina Reader node scrapes the company's homepage for additional context. Both outputs are merged before being passed to the AI agents.

Step 5: Research Analyst (GPT-4o-mini)
Formats the raw research data into a structured company profile including description, estimated headcount, company size classification, pain signals, and pain rating.

Step 6: ICP Scoring Agent (GPT-4o-mini)
Scores the company across four dimensions with a maximum of 25 points each. Returns a structured output including individual dimension scores, total score, ICP tier, and a two-sentence summary.

Step 7: Parse ICP Output
A code node extracts the numeric score, tier classification, and summary from the agent's text output into clean structured fields.

Step 8: Personalization Agent (GPT-4o-mini)
Generates three outreach assets for a Plant Manager or EHS Director at the company: a cold email opener, a LinkedIn connection request message, and three phone call talking points. All assets are role-aware and reference the company's specific pain signals and industry context.

Step 9: Parse Personalization Output
A code node extracts each outreach asset into its own clean field.

Step 10: Quality Critic Agent (GPT-4o)
Reviews the generated outreach assets and scores them across three dimensions: specificity, pain relevance, and how non-generic the copy is. Returns a pass or fail result with a failure reason if the average score is below 7.

Step 11: Quality Gate
If the quality critic passes the output, it moves to the Hot Tier Filter. If it fails and the retry count is below 2, the Personalization Agent reruns with the failure reason injected into the prompt. After 2 failed attempts, the output is passed through regardless.

Step 12: Hot Tier Filter
Any company with an ICP tier of Hot (score 70 or above) is routed to the Gmail alert node before writing to the output sheet. Warm and Cold companies are written to the output sheet directly.

Step 13: Gmail Alert (Hot leads only)
An email is sent immediately to the designated sales contact containing the company name, industry, ICP score, tier, and the generated email opener.

Step 14: Write to Output Sheet
All processed companies are written to the output data source with their complete profile including ICP score, tier, pain signals, outreach copy, company size, and processing timestamp.

Step 15: Loop Back
After writing, the workflow loops back to the Loop Over Items node to process the next company. This continues until all companies in the input list have been processed.

***

ICP Scoring Dimensions

Company Fit (25 points)
Evaluates industry match against AVIAN's target verticals (Manufacturing, Chemical Processing, Food and Beverage, Automotive, Energy), company size match against the 200 to 5,000 employee range, and North American or European geography.

Role Fit (25 points)
Assesses the likelihood that the company employs the right buyer personas: Plant Manager, EHS Director, Maintenance Manager, Safety Officer, or Operations VP.

Pain Fit (25 points)
Measures the strength of fire risk, thermal safety, equipment failure, and OSHA compliance signals based on the research output.

Timing Fit (25 points)
Identifies urgency signals including recent safety incidents, announced expansions, new facility openings, insurance audits, or regulatory activity.

Tier Classification: Hot is 70 and above. Warm is 40 to 69. Cold is below 40.

***

Output Per Company

Every processed company produces the following:

ICP score from 0 to 100 and tier classification
Pain signals summary from live research
Cold email opener (two sentences, personalised to role and company context)
LinkedIn connection request message (under 300 characters)
Three phone call talking points (one sentence each)
Company size classification
Processing timestamp

Hot accounts additionally trigger an immediate Gmail alert to the sales contact.

***

CRM Note

This demonstration uses Google Sheets as a lightweight input and output layer. In a live deployment, the Google Sheets nodes are replaced with the client's CRM using n8n's native integrations. The AI agents, scoring logic, and all other nodes remain identical. No changes to the pipeline architecture are required for CRM integration.

***

Demo Results

These figures are from a demonstration run on a 15-company curated test dataset.

3M: 88 out of 100, Hot
BASF: 85 out of 100, Hot
Celanese: 82 out of 100, Hot
Dow Chemical: 79 out of 100, Hot
Honeywell: 68 out of 100, Warm
Siemens: 65 out of 100, Warm
ConAgra: 47 out of 100, Cold

Setup instructions: See setup.md in this folder.
