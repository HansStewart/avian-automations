Re-Engagement System v1.0

Owner: Hans Stewart
Status: Production Active
Trigger: Daily schedule
Processing frequency: Every 24 hours
Last Updated: June 2026

***

What It Does

Monitors a database of dormant prospects on a daily cycle, calculates days since last contact for each record, flags contacts who have exceeded inactivity thresholds, assigns each flagged contact a priority tier using a composite scoring model, generates a contextual re-engagement message tailored to their original fit score and last interaction type, and routes all flagged contacts into a priority-ranked follow-up queue. Immediate-tier contacts trigger a same-day Gmail alert to ensure no high-value opportunity sits unnoticed.

The system ensures that no high-fit prospect can go cold without generating a visible, actionable follow-up signal.

***

Pipeline Architecture

The workflow runs on a daily schedule and processes the entire contacts database in sequence.

Step 1: Schedule Trigger
The workflow runs automatically once per day. It can also be triggered manually for testing or on-demand runs.

Step 2: Read Contacts Database
All contact records are pulled from the data source. Each record contains the contact name, company, original ICP score, last activity date, and last interaction type.

Step 3: Contact Monitoring and Date Calculation
For each contact, the workflow calculates the number of days elapsed since the last activity date. This value is used in the dormancy threshold checks downstream.

Step 4: Dormancy Threshold Check
Contacts are filtered against two inactivity thresholds. Contacts who have been dormant for 14 days or more, or 30 days or more, are passed to the next stage. Contacts who have been active within 14 days are skipped for that cycle.

Step 5: Priority Scoring Agent (GPT-4o)
A GPT-4o agent evaluates each flagged contact using a composite priority model that weighs three factors: the original ICP fit score from when the contact was first qualified, the number of days they have been dormant, and the type of their last interaction (meeting, email open, link click, etc.). Based on this composite assessment, the agent classifies each contact into one of three tiers.

Immediate: Contacts requiring same-day follow-up action. High original ICP score combined with extended dormancy.
This Week: Contacts who should be re-engaged within the current working week.
This Quarter: Contacts to keep warm before the quarter closes.

Step 6: Re-Engagement Copy Generation (GPT-4o)
For each flagged contact, a GPT-4o agent generates a contextual re-engagement message. The message references the previous interaction naturally, acknowledges the time gap without making it awkward, and offers a relevant next step. Messages are written to feel personal rather than automated.

Step 7: Write to Priority Queue
All flagged contacts are written to the output data source with their priority tier, days dormant, original ICP score, last interaction type, and generated re-engagement message. Records are ordered by priority tier so the follow-up queue is immediately actionable.

Step 8: Immediate Tier Alert (Gmail)
Any contact classified as Immediate triggers a same-day Gmail alert to the sales contact containing the contact name, company, ICP score, days dormant, and the generated re-engagement message. This ensures the highest-priority re-engagements receive a response on the day they are flagged.

***

Priority Scoring Model

The GPT-4o Priority Scoring Agent evaluates each dormant contact using the following composite factors:

Original ICP score: Higher-fit contacts are prioritised more aggressively. A contact who originally scored 82 out of 100 carries more urgency than one who scored 48.

Days dormant: Contacts approaching or exceeding 30 days of inactivity are weighted more heavily than those at 14 to 17 days.

Last interaction type: The nature of the previous touchpoint informs urgency. A meeting that went quiet carries more urgency than an email that went unread. A link click signals recent intent even if no direct response was received.

Tier output:
Immediate requires same-day action.
This Week requires action within the current week.
This Quarter requires action before the quarter closes.

***

Re-Engagement Message Standards

Every generated re-engagement message follows these standards:

References the specific previous interaction by type so it does not read as a cold outreach
Acknowledges the gap naturally without drawing excessive attention to it
Offers one specific, relevant next step rather than a vague open-ended ask
Does not use phrases such as just checking in, circling back, following up, or hope you are well
Written at a length appropriate for a re-engagement context: typically two to three sentences

***

CRM Note

This demonstration uses Airtable as the contacts storage and output layer. In a live deployment, the Airtable nodes are replaced with the client's CRM using n8n's native integrations. The priority scoring agent, copy generation agent, and all routing logic remain completely unchanged.

***

Demo Results

These figures are from a demonstration run on a curated test contacts dataset.

Marcus Reid, Celanese VP Operations: 31 days dormant, ICP score 82, Immediate
Emily Park, 3M Director of Operations: 28 days dormant, ICP score 88, Immediate
Priya Nair, Honeywell Director of Digital Strategy: 18 days dormant, ICP score 68, This Week
Anna Weber, Siemens SVP Operations: 16 days dormant, ICP score 65, This Week
Thomas Klein, BASF VP Sales: 15 days dormant, ICP score 85, This Week
James Holloway, ConAgra SVP Commercial: 14 days dormant, ICP score 47, This Quarter

Total contacts flagged: 6
Immediate tier: 2
This Week tier: 3
This Quarter tier: 1

Setup instructions: See setup.md in this folder.
