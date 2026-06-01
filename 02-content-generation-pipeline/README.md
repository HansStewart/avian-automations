Content Generation Pipeline v1.0

Owner: Hans Stewart
Status: Production Active
Trigger: Form submission
Processing time: Under 2 minutes from form submission to approval email
Last Updated: June 2026

***

What It Does

Takes a structured content brief submitted through a web form, scrapes AVIAN's live website on every run to extract the current brand voice, routes the brief to a specialist AI content agent based on the content type selected, generates a complete content asset, quality-grades it across four dimensions using a separate critic agent, and sends the draft to a human reviewer for approval before writing it to the content library. Nothing reaches the library without explicit human approval.

***

Pipeline Architecture

The workflow runs in the following sequence on every form submission.

Step 1: Content Brief Input (Form Trigger)
A structured web form collects five fields from the user: Content Type (LinkedIn Post, Cold Email, or Blog Introduction), Topic, Target Audience, Key Message, and Tone (Authoritative, Educational, Urgent, or Technical).

Step 2: Scrape AVIAN Website
Immediately after form submission, the Jina Reader node scrapes the live AVIAN website at avian-iot.com. This ensures brand voice is always current and never relies on a static guide that can become outdated.

Step 3: Brand Voice Agent (GPT-4o)
Analyses the scraped website content and extracts a structured brand voice profile including tone description, vocabulary style, sentence structure, core themes, elements to avoid, target audience signals, and brand positioning. This profile is passed to every content agent downstream.

Step 4: Content Type Router
A Switch node reads the Content Type field from the form submission and routes the execution to one of three specialist agents: LinkedIn Content Agent, Email Content Agent, or Blog Content Agent. Only one agent runs per execution.

Step 5: Specialist Content Agent (GPT-4o)
The appropriate agent generates a complete content asset using the brand voice profile and all five brief fields. Each agent has format-specific rules:

The LinkedIn Content Agent produces a hook, a full post between 900 and 1,200 characters, and up to three hashtags.
The Email Content Agent produces three subject line variants, preview text under 90 characters, a body of four to six sentences, and a single specific call to action.
The Blog Content Agent produces an SEO-optimised headline, a meta description under 155 characters, and an introduction of 150 to 200 words opening with an industry statistic or alarming fact.

Step 6: Carry Content Forward
A code node preserves the content agent's raw output in a named field so it remains accessible to downstream nodes regardless of which agent produced it.

Step 7: Quality Critic Agent (GPT-4o)
A separate agent reviews the content draft and scores it across four dimensions from 1 to 10: Clarity, Brand Voice Match, Specificity, and CTA Strength. It also provides specific reviewer notes and recommended changes. Scores of 9 to 10 are reserved for exceptional work.

Step 8: Parse Quality Scores
A code node extracts all four dimension scores, the overall score, the passes quality gate boolean, reviewer notes, and recommended changes into clean structured fields.

Step 9: Quality Gate Check
If all four dimension scores are 7 or above, the output passes and moves to the Bundle Final Output node. If any score is below 7, the output is flagged with a quality failure summary and the failure reason is recorded.

Step 10: Bundle Final Output
A code node assembles the complete final object combining all brief fields, all content output fields, all quality scores, and the generated timestamp into a single clean record.

Step 11: Human Approval Gate (Gmail, Send and Wait)
The workflow pauses here. An email is sent to the designated reviewer containing the full content draft, all four quality dimension scores, reviewer notes, and recommended changes. The workflow does not proceed until the reviewer clicks Approve or Reject in the email. This is an intentional human-in-the-loop control point.

Step 12: Content Library
Only after explicit human approval does the workflow write the complete record to the content library sheet. The record includes all brief fields, the content output, all quality scores, approval status, and the generation timestamp.

***

Quality Scoring Dimensions

Clarity (scored 1 to 10)
Is it immediately clear what the content is about and who it is for?

Brand Voice Match (scored 1 to 10)
Does it match AVIAN's tone, vocabulary, and style as extracted from the live website?

Specificity (scored 1 to 10)
Does it reference specific details about the audience, industry, or pain point, or is it generic filler?

CTA Strength (scored 1 to 10)
Is the call to action specific, compelling, and singular rather than vague or multiple?

Quality gate threshold: All four scores must be 7 or above for the output to pass. If any dimension scores below 7, the record is flagged before it reaches the human approval step.

***

Content Library Output

Every approved asset written to the content library includes the following fields:

Content type, topic, target audience, key message, and tone from the original brief
Full content output text
Subject lines (for cold emails)
Headline (for blog introductions)
Individual scores for Clarity, Brand Voice Match, Specificity, and CTA Strength
Overall quality score
Whether it passed the quality gate
Reviewer notes and recommended changes
Approval status
Generation timestamp

***

CRM and Infrastructure Note

This demonstration uses Google Sheets as the content library storage layer. In a live deployment, the content library write node is replaced with the client's CRM or content management system. The form trigger, all AI agents, and the quality scoring logic remain unchanged.

***

Setup instructions: See setup.md in this folder.
