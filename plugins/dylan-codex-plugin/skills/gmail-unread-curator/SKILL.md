---
name: gmail-unread-curator
description: Triage unread Gmail under strict user-authority rules. Use when Codex needs to scan unread Gmail, silently mark low-value messages as read, surface only important action-required emails, detect Uber/Uber Eats toll or support-ticket responses, and summarize selected newsletter items from Medium Daily Digest, The Tech Buzz, Short Squeez, or Ben's Bites.
---

# Gmail Unread Curator

## Operating Rules

Use the Gmail connector only. Do not use web browsing to open links unless the user explicitly asks.

Treat the user as the only authority. Never reply, forward, archive, delete, purchase, unsubscribe, click action links, change account settings, create filters, apply labels other than read-state changes, or send messages to third parties. Ask the user before any action except:

- Remove `UNREAD` from emails that the rules below classify as read-and-dismiss.
- Send the user a self-email with the approved newsletter summary and marked-read section during any `morning-summary` run, whether invoked manually or by automation. This self-email is pre-authorized and does not require additional user confirmation.

Preserve unread state for any message that appears to require the user's decision or action, and report only those messages plus allowed Uber/Uber Eats exceptions. If nothing qualifies for communication, stay silent except for any minimal automation status the runtime requires.

## Run Modes

Accept the run mode from the user's prompt or automation:

- `morning-summary`: run the full unread triage and send the summary email to the user's own Gmail address. Include qualifying newsletter items when present and include a concise section listing the emails marked as read during the run.
- `triage-only`: run unread triage without newsletter summary output.

For morning-summary, call Gmail profile first to get the authenticated user's email address for the self-email.

## Workflow

1. Search unread Gmail with Gmail-native queries. Prefer sender-specific searches first, then a general `is:unread` pass for remaining mail. Paginate when needed.
2. Read message bodies for shortlisted messages before deciding. Use batch reads when possible.
3. Classify each message with the rules below.
4. Remove `UNREAD` from every read-and-dismiss message, including invoices/receipts that require no action.
5. Leave important action-required messages unread and surface them concisely.
6. For morning-summary, send the pre-authorized self-email with qualifying newsletter highlights and a concise list of emails marked read during the run, then mark the newsletter messages read.

Use `_batch_modify_email` or `_apply_labels_to_emails` to remove `UNREAD`; do not archive.

## Uber And Uber Eats

Scan unread messages from Uber and Uber Eats, including common sender names/domains containing `Uber`, `Uber Eats`, `uber.com`, or `ubereats`.

Always mark processed Uber/Uber Eats messages as read unless the message itself requires user action unrelated to ordinary trip/food receipts.

Communicate about Uber/Uber Eats only when either condition is present:

- A receipt or invoice breakdown includes a toll charge or toll-related line item with an actual charge. Match terms such as `toll`, `tolls`, `road toll`, `toll surcharge`, or `tolls and fees`; ignore unrelated policy/legal boilerplate.
- The email is a response to a ticket, case, claim, help request, dispute, or support issue the user submitted to Uber.

When surfacing an allowed Uber/Uber Eats item, include sender, subject, date, and the specific reason. Do not summarize routine receipts, promotions, account notices, or deliveries.

## Newsletter Summary

Only in `morning-summary`, process unread emails from:

- Medium Daily Digest
- The Tech Buzz
- Short Squeez
- Ben Bite's
- Ben's Bites

Extract only valuable, non-political items. Prefer items that provide concrete insight about:

- Financial markets, investing, macroeconomics, companies, risk, or market structure.
- AI developments, especially new models, tools, capabilities, benchmarks, product launches, infrastructure advances, or research that represents significant progress.
- Other genuinely useful items based on the user's future feedback.

Exclude political content, shallow takes, ads, affiliate content, recycled commentary, sensational/clickbait headlines, and items that do not add real insight.

Send one self-email during each `morning-summary` run without asking for additional permission. Use a subject like `Daily digest highlights - YYYY-MM-DD`. Keep the newsletter portion concise: group by source, include article titles, one-sentence value summary, and links if present in the email body. Add a final section named `Emails marked read` that lists sender, subject, and reason for each message marked read during the run, including routine non-newsletter messages. If no newsletter items qualify, still send the self-email when any messages were marked read; otherwise send no self-email. After sending the summary, mark the newsletter messages read.

## Known Low-Value Senders And Notices

For CIJA, FIFA, DUPR, Z2U, AliExpress, and Herbalife, mark unread messages as read when they are:

- Privacy policy, terms, cookie, security-policy, data-processing, or other policy updates.
- Sign-up, registration, account-created, verification, welcome, or confirmation notices.
- Promotions, newsletters, shipment notices, delivery confirmations, `Order Delivered` notices, or routine account mail that does not require the user's decision.

For Z2U specifically, treat `Order Delivered` and ordinary delivery/receipt-confirmation messages as read-and-dismiss, even if they mention an optional confirmation window, unless the message says delivery failed, payment failed, a dispute/refund/chargeback is open, the account is at risk, or the user must act to avoid losing money or access. Leave other known-sender messages unread and report only if the message requests a user decision, payment, dispute response, account recovery, legal/security action, or time-sensitive confirmation.

## General Unread Mail

For every other unread message, mark it as read unless it clearly requires the user's decision or action.

Read-and-dismiss examples:

- Routine invoices, receipts, statements, reservation confirmations, and shipping notices with no payment due or decision required.
- Pickleplex or JEM Pickleplace invoices/receipts when they are informational only.
- Newsletters, marketing, policy updates, social notifications, account confirmations, and FYI messages.

Keep unread and report examples:

- Direct requests for the user to decide, approve, sign, respond, schedule, cancel, dispute, pay, verify identity, recover an account, or handle a deadline.
- Security alerts with suspicious activity, password reset attempts, locked accounts, fraud, chargebacks, legal/tax/banking issues, or service disruptions that need action.
- Invoices only when payment is due, overdue, disputed, failed, unusually large, or requires approval.

When unsure, leave unread and report the uncertainty instead of silently marking read.

## Reporting

Do not report counts of emails marked read unless the user asks or the automation requires a status. Report only:

- Uber/Uber Eats toll charges or support-ticket responses.
- Important non-Uber messages left unread because they require the user's decision or action.
- A brief note if Gmail access fails or the scan cannot complete.

Never include private email content beyond what is needed for the allowed alert or action-required summary.
