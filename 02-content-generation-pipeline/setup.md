Setup Guide: Content Generation Pipeline v1.0

This guide covers everything needed to import, configure, and run the Content Generation Pipeline in your own n8n instance.

***

Prerequisites

An n8n instance, either n8n Cloud or self-hosted
An OpenAI API key from platform.openai.com
A Gmail account to send and receive approval emails
A Google account with access to Google Sheets for the content library

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

Brand Voice Agent node: Connect an OpenAI API credential using your key from platform.openai.com.
LinkedIn Content Agent node: Connect the same OpenAI API credential.
Email Content Agent node: Connect the same OpenAI API credential.
Blog Content Agent node: Connect the same OpenAI API credential.
Quality Critic Agent node: Connect the same OpenAI API credential.
Human Approval Gate node: Connect a Gmail OAuth2 credential by authorising your Gmail account in n8n.
Content Library node: Connect a Google Sheets OAuth2 credential by authorising your Google account.

***

Step 3: Get Your Form Trigger URL

Open the Content Brief Input node.
Click the Test Step button. n8n will display a Production URL and a Test URL for the form.
Copy the Production URL. This is the web address where the form lives and where you will submit content briefs.
The form is live as soon as the workflow is activated. No additional hosting is required.

***

Step 4: Create the Content Library Google Sheet

Create a new Google Sheet and add the following column headers exactly as written in row 1:

Content_Type | Topic | Target_Audience | Key_Message | Tone | Content_Output | Subject_Lines | Headline | Clarity_Score | Brand_Voice_Score | Specificity_Score | CTA_Score | Overall_Quality_Score | Passes_Quality_Gate | Reviewer_Notes | Recommended_Changes | Approval_Status | Generated_At

***

Step 5: Connect the Content Library Sheet

Open the Content Library node.
Click the Document field and paste the URL of your content library sheet.
Confirm the Sheet Name field is pointing to Sheet1 or whichever tab contains your column headers.

***

Step 6: Update the Approval Email Recipient

Open the Human Approval Gate node.
Find the Send To field.
Replace the existing email address with the address where you want approval request emails delivered.
This is the address that will receive the draft content, quality scores, and the Approve or Reject buttons.

***

Step 7: Activate the Workflow

Click the toggle in the top right corner of the canvas to set the workflow status to Active.
The form trigger URL is now live and ready to accept submissions.

***

Step 8: Submit a Content Brief

Navigate to the form URL you copied in Step 3.
Fill in all five fields: Content Type, Topic, Target Audience, Key Message, and Tone.
Click Submit.
The workflow will run automatically. Within approximately 2 minutes, an approval email will arrive at the address configured in Step 6 containing the full content draft and all quality scores.
Click Approve in the email to write the record to the content library, or Reject to discard it.

***

Troubleshooting

If the Brand Voice Agent is returning empty or malformed output, check that the Scrape AVIAN Website node is successfully returning content from avian-iot.com. The Jina Reader node occasionally times out on the first request. Running the workflow again typically resolves this.

If the Quality Critic is returning scores of zero, confirm that the Carry Content Forward code node is correctly passing the content_agent_output field and that the Quality Critic Agent node is receiving it in the prompt via $json.output.

If the approval email is not arriving, confirm the Gmail OAuth2 credential is correctly authorised and that the Send To address in the Human Approval Gate node is accurate.

***

Live Deployment Note

To replace Google Sheets with a production content management system or CRM, replace the Content Library node with the appropriate n8n integration node for your platform. All AI agents, the quality gate, and the human approval step remain completely unchanged.
