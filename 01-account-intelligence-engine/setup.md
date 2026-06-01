Setup Guide: Account Intelligence Engine v1.2

This guide covers everything needed to import, configure, and run the Account Intelligence Engine in your own n8n instance.

***

Prerequisites

Before importing the workflow you must have the following active:

An n8n instance, either n8n Cloud or self-hosted
An OpenAI API key from platform.openai.com
A SerpAPI account and API key from serpapi.com
A Google account with access to Google Sheets
A Gmail account to receive hot lead alert emails

***

Step 1: Import the Workflow

Download the file named workflow.json from this folder.
Open your n8n instance and click New Workflow.
Click the three-dot menu in the top right corner of the canvas.
Click Import from file and select the workflow.json file.
The full workflow will load with all nodes, connections, and settings intact.

***

Step 2: Configure Credentials

Open each node listed below and connect the correct credential. If the credential does not exist yet in your n8n instance, click Create New Credential and follow the prompts.

GPT-4o Research LLM node: Connect an OpenAI API credential using your key from platform.openai.com.
GPT-4o ICP Scoring LLM node: Connect the same OpenAI API credential.
GPT-4o Personalization LLM node: Connect the same OpenAI API credential.
GPT-4o Quality Critic LLM node: Connect the same OpenAI API credential.
Read Target Account List node: Connect a Google Sheets OAuth2 credential by authorising your Google account.
Write to AVIAN Pipeline Sheet node: Connect the same Google Sheets OAuth2 credential.
Hot Lead Alert Gmail node: Connect a Gmail OAuth2 credential by authorising your Gmail account in n8n.

***

Step 3: Create the Input Google Sheet

Create a new Google Sheet and add the following column headers exactly as written in row 1:

Company Name | Website | Industry

Add your target companies as rows below the headers. The Website column should contain the base domain only, for example honeywell.com, not the full URL with https or www.

***

Step 4: Create the Output Google Sheet

Create a second Google Sheet and add the following column headers exactly as written in row 1:

Company | Website | Industry | ICP Score | ICP Tier | Pain Signals Detected | Email Opener | LinkedIn Message | Company Size | Date Processed

***

Step 5: Connect Your Sheets to the Workflow

Open the Read Target Account List node. Click the Document field and paste the URL of your input sheet.
Open the Write to AVIAN Pipeline Sheet node. Click the Document field and paste the URL of your output sheet.
In both nodes, confirm the Sheet Name field is pointing to Sheet1 or whichever tab contains your data.

***

Step 6: Add Your SerpAPI Key

Open the Web Research node.
In the URL field, locate the section that reads api_key= near the end of the URL.
Replace the existing key value with your own SerpAPI key from your account at serpapi.com.

***

Step 7: Update the Gmail Alert Recipient

Open the Hot Lead Alert Gmail node.
Find the Send To field.
Replace the existing email address with the address where you want hot lead alerts delivered.

***

Step 8: Run the Workflow

Click Execute Workflow from the canvas.
The loop will process each company in your input sheet one at a time.
When all companies have been processed, open your output sheet. Every company will have a completed row with ICP score, tier, pain signals, and all three outreach assets.
Any company that scored 70 or above will have also triggered a Gmail alert at the point it was processed.

***

Troubleshooting

If only the first company is processed, verify that the output of the Write to AVIAN Pipeline Sheet node is connected back to the input of the Loop Over Items node. This return connection is required for the loop to advance to the next company.

If the ICP tier condition in the Hot Tier Filter node is not matching, open the node and update the left-hand condition value from $json.icp_tier_clean to $('Parse ICP Output').item.json.icp_tier_clean to ensure it is reading from the correct node.

If quality scores are returning as zero, check that the Quality Critic Agent is receiving the output from the Carry Content Forward node and that the Parse Quality Scores code node is connected correctly downstream.

***

Live CRM Deployment

To replace Google Sheets with a production CRM:

Replace the Read Target Account List node with an n8n HubSpot, Salesforce, or Pipedrive node configured to pull contact or company records.
Replace the Write to AVIAN Pipeline Sheet node with the equivalent CRM update or create record node.
All AI nodes, scoring logic, and routing remain completely unchanged.
