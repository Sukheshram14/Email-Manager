# Email Manager

AI-powered Gmail email classification and automatic labeling workflow built with **n8n**, an LLM through **OpenRouter**, and Gmail.

The workflow monitors incoming emails, retrieves the complete message, uses an AI Agent to classify the email into one category, and automatically applies the corresponding Gmail label.

## What it does

```text
New Gmail Email
       ↓
Gmail Trigger
       ↓
Loop Over Items
       ↓
Get Full Email
       ↓
AI Agent
   ┌───┴───────────────┐
   │                   │
Gmail history      Sent history
context             context
   │                   │
   └───────┬───────────┘
           ↓
    Structured Output
           ↓
     Gmail Label
           ↓
      Loop / Continue
```

## Categories

The AI Agent classifies each email into exactly one of these 14 Gmail categories:

| Label | Category | Purpose |
|---|---|---|
| `Label_1` | JOB - ACTION | Job-related email requiring an action |
| `Label_2` | JOB - APPLICATION | Application confirmation or status |
| `Label_3` | JOB - OPPORTUNITY | Job listings and job alerts |
| `Label_4` | JOB - INTERVIEW | Interview scheduling, invitation, feedback |
| `Label_5` | CAMPUS - PLACEMENT | College placement and campus recruitment |
| `Label_6` | INTERNSHIP | Internship opportunities and communication |
| `Label_7` | PROJECT | Project collaboration and GitHub-related communication |
| `Label_8` | HACKATHON | Hackathons, coding competitions and build challenges |
| `Label_9` | LEARNING | Courses, certifications and educational content |
| `Label_10` | SECURITY | Login, OTP and account-security notifications |
| `Label_11` | SERVICE | Billing, account and routine service notifications |
| `Label_12` | PERSONAL | Personal human-to-human communication |
| `Label_13` | NEWSLETTER | Recurring informational digests/newsletters |
| `Label_14` | MARKETING | Promotional and sales emails |

The workflow uses a priority order when an email could match multiple categories. Security and action-oriented job emails receive higher priority than general informational or promotional messages.

## Key features

### 1. Automatic Gmail monitoring

The `Gmail Trigger` checks for new messages every minute.

### 2. Full email retrieval

The `Gmail` node retrieves the complete email rather than relying only on trigger metadata.

The classifier receives information such as:

- Sender
- Sender name
- Recipients
- Subject
- Body
- Existing Gmail labels
- Auto-submitted header
- Sender header
- Reply information
- References
- List-Unsubscribe header
- Precedence header

### 3. AI-powered classification

The `AI Agent` analyzes the email against a detailed classification policy and returns the corresponding Gmail label ID.

The prompt explicitly prevents the model from inventing label IDs and restricts the final classification to the supported label values.

### 4. Email history as context

The AI Agent has two Gmail tools:

- `Get Email` — searches previous messages from the sender.
- `Check Sent` — checks sent messages addressed to the sender.

This allows previous correspondence to be used as supporting context when distinguishing categories such as job communication, newsletters, service notifications, and personal conversations.

### 5. Structured output

A `Structured Output Parser` is connected to the AI Agent to keep the model output in a predictable structure before the Gmail labeling step.

### 6. Automatic Gmail labeling

The `Gmail1` node adds the selected label to the current email.

## Classification logic

The workflow uses an evidence-based classification approach.

For example:

- A job listing → `JOB - OPPORTUNITY`
- An application requiring action → `JOB - ACTION`
- An application status update → `JOB - APPLICATION`
- Interview communication → `JOB - INTERVIEW`
- A college placement email → `CAMPUS - PLACEMENT`
- An internship email → `INTERNSHIP`
- A hackathon → `HACKATHON`
- A course or certification → `LEARNING`
- An OTP or login alert → `SECURITY`
- A billing/service notification → `SERVICE`
- A personal conversation → `PERSONAL`
- A recurring digest → `NEWSLETTER`
- A promotional email → `MARKETING`

The workflow also avoids simplistic rules such as:

- Cold email ≠ automatically marketing
- Known sender ≠ automatically service
- Unsubscribe link ≠ automatically marketing

## Technology stack

- **n8n** — workflow orchestration
- **Gmail** — email trigger, retrieval, history search and labeling
- **OpenRouter** — LLM access
- **AI Agent** — contextual email classification
- **Structured Output Parser** — structured model output

## Repository structure

```text
email-manager/
│
├── README.md
│
├── workflow/
│   └── email-manager-workflow.json
│
└── .gitignore
```

## Setup

### Requirements

You need:

1. An n8n instance
2. A Gmail account connected through Gmail OAuth2
3. An OpenRouter account/API credential
4. Gmail labels corresponding to the workflow's classification categories

### Import the workflow

1. Open n8n.
2. Go to **Workflows**.
3. Select **Import from File**.
4. Import:

```text
workflow/email-manager-workflow.json
```

5. Reconnect the Gmail credential.
6. Reconnect the OpenRouter credential.
7. Verify the Gmail label IDs used by your account.
8. Update the user email in the AI Agent prompt if required.
9. Test with a small set of emails.
10. Activate the workflow.

## Important: public repository safety

The workflow in this repository is a sanitized export.

Account-specific n8n credential references and environment-specific identifiers have been removed. Credentials must be configured again after importing the workflow.

Do **not** commit:

- Gmail OAuth tokens
- OpenRouter API keys
- Passwords
- Private email addresses
- Private email content
- OAuth client secrets
- n8n instance identifiers
- Private webhook URLs

## Limitations

This is an AI-assisted classification workflow, so classification quality depends on the email content and the selected language model.

The workflow should be tested before being used as a fully trusted mailbox automation system, particularly for security-sensitive and job-related emails.

The Gmail labels must exist and their IDs must match the values configured in the workflow.

## Project purpose

Email Manager was built to reduce manual email organization by combining:

- event-driven automation
- Gmail APIs
- LLM-based classification
- conversation history
- structured outputs
- automatic label application

It demonstrates how an LLM can be integrated into a practical workflow automation system rather than being used only as a standalone chatbot.

## License

No license is currently specified.
