Setup Guide: Re-Engagement System v1.0

This guide covers everything needed to import, configure, and run the Re-Engagement System in your own n8n instance.

***

Prerequisites

An n8n instance, either n8n Cloud or self-hosted
An OpenAI API key from platform.openai.com
An Airtable account with a base set up for contacts (demonstration configuration)
A Gmail account to receive Immediate tier follow-up alerts

***

Step 1: Import the Workflow

Download the file named workflow.json from this folder.
Open your n8n instance and click New Workflow.
Click the three-dot menu in the top right corner of the canvas.
Click Import from file and select the workflow.json file.
The full workflow will load with all nodes, connections, and settings intact.

***

Step 2: Configure Credentials

Open each node listed below and connect the correct credential.

Priority Scoring Agent node: Connect an OpenAI API credential using your key from platform.openai.com.
Re-Engagement Copy Agent node: Connect the same OpenAI API credential.
Read Contacts node: Connect an Airtable Personal Access Token credential. Your token is available at airtable.com under your account settings.
Write to Priority Queue node: Connect the same Airtable credential.
Immediate Alert Gmail node: Connect a Gmail OAuth2 credential by authorising your Gmail account in n8n.

***

Step 3: Set Up Your Airtable Base

In your Airtable account, create a new base or use an existing one. The contacts table used as the input source requires the following fields:

Contact Name (Single line text)
Company (Single line text)
ICP Score (Number)
Last Activity Date (Date)
Last Interaction Type (Single line text, for example: Meeting, Email opened, Link clicked, Call)

The output table where the priority queue is written requires the following fields:

Contact Name (Single line text)
Company (Single line text)
ICP Score (Number)
Days Dormant (Number)
Priority Tier (Single line text)
Re-Engagement Message (Long text)
Last Interaction Type (Single line text)
Date Processed (Single line text)

***

Step 4: Connect Your Airtable Tables to the Workflow

Open the Read Contacts node.
In the Base field, select or paste your Airtable base ID. The base ID is the alphanumeric string in your Airtable URL after airtable.com/app.
In the Table field, select your contacts table.

Open the Write to Priority Queue node.
Connect the same Airtable base.
Select the output table where processed records will be written.

***

Step 5: Configure the Schedule Trigger

Open the Schedule Trigger node.
The default configuration runs the workflow once daily at 07:00.
To change the time, update the Trigger at Hour field to your preferred hour in 24-hour format.
To run more frequently, switch the interval from Days to Hours and set the value accordingly.

***

Step 6: Update the Gmail Alert Recipient

Open the Immediate Alert Gmail node.
Find the Send To field.
Replace the existing email address with the address where you want Immediate tier alerts delivered.

***

Step 7: Activate the Workflow

Click the toggle in the top right corner of the canvas to set the workflow status to Active.
The schedule trigger will now fire automatically at the configured time each day.
To run the workflow immediately without waiting for the schedule, click Execute Workflow from the canvas.

***

Step 8: Review the Output

After the workflow runs, open your Airtable priority queue table. Every flagged contact will have a new record with their priority tier, days dormant, ICP score, last interaction type, and generated re-engagement message.

Any contact classified as Immediate will also have triggered a Gmail alert at the time they were processed.

The queue is ordered by the order in which contacts were processed. To sort by priority tier for a clean follow-up view, add a filter or sort in Airtable by the Priority Tier field.

***

Troubleshooting

If no contacts are being flagged, confirm that the Last Activity Date field in Airtable is formatted as a date value and not plain text. The dormancy calculation node reads this field and converts it to a day count. Plain text values will cause the calculation to return zero.

If the priority scoring agent is returning inconsistent tier classifications, check that the ICP Score field in Airtable is a Number field type and not text. The agent prompt includes the score as a numeric input and will behave unpredictably if it receives a text string.

If Gmail alerts are not arriving for Immediate contacts, open the Immediate Alert Gmail node and confirm the credential is authorised and the Send To address is correct.

***

Live CRM Deployment

To replace Airtable with a production CRM, replace the Read Contacts node with an n8n HubSpot, Salesforce, or Pipedrive node configured to pull contact records filtered by last activity date. Replace the Write to Priority Queue node with the equivalent CRM update or task creation node. The priority scoring agent, copy generation agent, dormancy thresholds, and alert logic remain completely unchanged.
