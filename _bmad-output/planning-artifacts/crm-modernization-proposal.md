# CRM v2 Modernization & Process Automation Proposal

**Prepared by:** Atomic Actions Group inc.
**Contact:** hello@atomicactions.io
**Prepared for:** FBA

---

## Project Summary

CRM Modernization & Process Automation improves CRM usability, reduces manual effort, and increases transparency and control across key client and internal processes. The project addresses gaps in user onboarding, support, cost visibility, KPI measurement, and operational onboarding by moving these workflows and tracking into the CRM.

---

## Proposed Solution

### 1. Virtual Assistant (Progress-Bar Guidance)

A guided, step-by-step experience inside the CRM (progress bar) that helps users complete key actions. The system prompts the user, tracks completion, and automatically advances to the next step as tasks are completed.

### 2. CRM-Native Live Chat Module

CRM-native live chat module with lightweight rule-based pre-support. The system creates a Conversation on the first user message/trigger and stores all inbound/outbound Messages in the CRM (with author type). Before a live operator is engaged, users are guided via a hierarchical FAQ and simple keyword checks. If escalation is needed, the chat is assigned to a manager by rules and a task/notification is created. The CRM UI provides a message timeline and supports manager reassignment.

### 3. Client Balance Transparency

Client Balance Transparency feature inside the CRM. This feature will provide users with a clear and simple overview of their financial activity, including detailed expense breakdowns and related invoices. The system will also introduce automated notifications to inform clients when their contract balance is running low.

The detailed financial data can be implemented in two ways:

- **Option 1:** By integrating with the client's existing financial application.
- **Option 2 (recommended):** By migrating financial calculation logic into the CRM for deeper long-term integration.

### 4. KPI Tracking

KPI tracking in the CRM. The system will calculate the agreed KPI metrics based on defined data sources and display them in role-based dashboards (Manager / Controlling / Admin). Users can switch between weekly and monthly views. Admin can see period breakdowns, access KPI history (table + chart) for comparisons.

### 5. Onboarding Case Workflow

Onboarding Case with clear stages, owners, and deadlines. The CRM will guide managers through checklists and tasks, enforce the 24-hour SLA for acceptance and the welcome email, and use templates for standard communications. Different onboarding scenarios will be handled via simple flags that activate the required steps, and handoff to accounting will be allowed only after mandatory items are completed.

---

## Time & Cost Estimate

| Stage / Version | Description | Timeline |
|---|---|---|
| Virtual Assistant (Progress-Bar Guidance) and Rule-Based In-CRM Support (Live Chat + FAQ Rules) | Progress-bar guidance inside CRM; Step-by-step prompts; Tracks completion and advances steps; In-CRM live chat with rule-based FAQ/scenario guidance; Escalation to a manager for complex cases; Conversation history stored in the CRM | 293h |
| Client Balance Transparency + Notifications and KPI Tracking & Reporting | Balance view + expense breakdown + related invoices; Alerts: low balance; Data: finance app integration (MVP) / CRM calculations (long-term); Automated KPI calculation (managers + controlling); Role-based dashboards + weekly/monthly reports; KPI history (by month) with period comparison | Option 1: 299h / Option 2: 317h |
| Onboarding Case Workflow | Onboarding case with stages, owners, deadlines; Checklists/tasks + 24h SLA (acceptance & welcome email); Handoff to accounting only after required steps | 412h |
| **Total** | Dev / staging / production; Error handling + QA coverage; Documentation handover; Guided adoption + in-CRM support; Financial transparency + automated alerts; KPI visibility + standardized onboarding workflow | **1004h** (Option 1) / **1022h** (Option 2) |

---

## Team

- 2 Software Engineers
- 1 QA Engineer
- 1 Project Manager
- 1 BA

---

## Key Results

- Reduction of Manual Effort and Processing Time
- Improved Data Accuracy and Consistency
- Increased Scalability and Process Efficiency
- Enhanced Transparency, Control, and Reporting
- Reduced number of cost-related support requests
- Automated KPI visibility and SLA control
- Improved onboarding SLA compliance and visibility

---

## Notes That May Affect Estimation

- **Data migration scope** (cost detailed breakdown, onboarding history, document links) may increase effort depending on the quality and structure of existing data.
- **Historical KPI reporting** may require backfilling data for past periods if the client wants trends and comparisons starting before the new CRM launch.
- **Custom UI requirements** (keeping the current CRM design style, new list views, client-safe views, progress-bar Virtual Assistant UI) may add additional design and front-end work.

---

## Existing (Manual) Process Overview

**Summary:** Mostly manual; limited automation across key processes. User enablement, support, financial transparency, KPI tracking, and client onboarding are handled through manual communication and separate tools (email/Telegram/Google Drive/spreadsheets), with limited visibility and no structured history inside the CRM.

### Business Logic (Current State)

- **Manual:** A new user/client starts using the CRM without guided navigation; learning happens via self-discovery or instructions from managers in chats.
- **Manual:** When questions appear (CRM usage, balance, invoices, payouts), the user contacts a manager via Telegram/internal chats.
- **Manual:** The manager manually reviews the request, searches for information in internal records, and explains the steps to the user.
- **Manual:** Communication history remains outside the CRM, so issues are not structured, and response/SLA tracking is limited.
- **Manual:** Clients request clarification about contract balance, expenses, and invoice usage because the CRM does not provide a clear financial overview or automated alerts.
- **Manual:** Manager performance and service levels are not tracked automatically; KPI checks rely on manual feedback (client survey every few months + team lead feedback), without CRM dashboards or regular KPI history.
- **Manual:** Client onboarding is coordinated through email handover from sales, document checks in Google Drive, and progress tracking in a separate matrix; key actions (welcome email, access setup, invoicing/payment checks, legalisation steps) are completed manually and handoff to accounting is done via email.

---

## Proposed (Automated) Process Overview

**Summary:** 70-85% automated; 15-30% manual. Most user guidance, navigation, standard support requests, KPI tracking, and onboarding execution are managed inside the CRM through automation, role-based dashboards, and workflow cases. Manual effort remains mainly for non-standard support situations, expert checks (e.g., document validation/legalisation decisions), and exception handling.

### Feature Breakdown: Manual vs. Automated

| Feature | Manual (setup + exceptions) | Automated (in CRM) | Output |
|---|---|---|---|
| **1. Virtual Assistant (Progress-Bar Guidance)** | Configure progress-bar scenarios, step definitions, completion rules (based on CRM capabilities). | The assistant activates in the relevant context, guides the user step-by-step, highlights UI elements, tracks completion, and automatically moves to the next step. | Faster user adoption, fewer "how-to" questions, more consistent CRM usage. |
| **2. Rule-Based In-CRM Support (Live Chat + FAQ Rules)** | Configure dialogue scenarios/FAQ structure, keyword checks, escalation rules, and manager role (based on CRM capabilities); managers handle non-standard cases. | The CRM auto-creates conversations on the first user message/trigger, provides first-line guidance via predefined flows, and escalates when needed (assignment + task/notification). Conversation history is stored in the CRM. | Improved support consistency, full traceability in CRM, reduced manual processing. |
| **3. Client Balance Transparency + Notifications** | Configure finance data source and display logic; set notification thresholds and rules; managers support exceptional finance cases. | CRM shows a clear balance and financial activity view (expense breakdown + related invoices) and sends notification to inform clients when their contract balance is running low. | Higher financial transparency, fewer finance-related support requests, proactive risk prevention via alerts. |
| **4. Automated KPI Tracking** | Configure KPI definitions and thresholds, SLA timers, role-based access, dashboards, and weekly/monthly reports (based on CRM capabilities); team leads review outliers and follow up on breaches when needed. | The CRM captures required events with timestamps and automatically calculates KPIs, updating dashboards with period breakdowns and KPI history (table + chart) for comparisons, with drill-down to underlying lists. | Automated KPI visibility and SLA control, consistent reporting, and trend tracking with period comparisons. |
| **5. Onboarding Case Workflow** | Configure onboarding stages, checklists, templates, scenario flags, SLA rules (24h acceptance + welcome email), and mandatory handoff validations (based on CRM capabilities); expert checks remain manual (e.g., document validation/legalisation decisions). | CRM starts an onboarding case on handover, assigns owner, sets deadlines, guides via tasks/checklists, activates steps via scenario flags, and allows handoff to accounting only when mandatory items are completed. | Transparent onboarding tracking, improved SLA compliance, fewer missed steps, controlled handoff to accounting. |

---

## About Atomic Actions

Atomic Actions helps companies gain full visibility into how their business actually runs. We design Central Hubs that bring data, workflows, and operations into one clear system — replacing fragmented tools, manual work, and blind spots. The result is faster decisions, cleaner processes, and systems that scale without chaos.

Our work combines modern technology with low-code, no-code, AI agents, and automation. From internal dashboards and custom apps to AI-powered operations, invoicing, onboarding, and reporting, we build systems that run quietly and reliably in the background.

### Real Examples

| Project | Result |
|---|---|
| Operations & finance hub for a services company | Centralized contracts, payroll, invoicing, and compliance into one role-based system, cutting operational time by 42% and making it 100x cheaper to scale per client. |
| Construction business Central Hub | Built a single dashboard that unifies sales, marketing, production, and operations, giving leadership real-time visibility into revenue, project performance, and operational health. |

---

## Project Delivery and Support Terms

### Post-Deployment Support & Maintenance

Once the solution is deployed, we offer ongoing support on an hourly basis, billed according to our rates. During the acceptance phase (the first 7 calendar days after the system goes live), we handle all bug fixes, error corrections, and functionality checks as part of the original project scope. This ensures that everything works as intended in a live environment.

Any support or maintenance after the acceptance phase will be covered under a separate proposal or hourly retainer, depending on the client's needs and preferred engagement model.

### Change Management & Scope Adjustments

If new requirements, functionality, or conditions arise that go beyond the approved project scope, we will:

- Assess the impact on budget and timeline.
- Provide a revised estimate for review.
- Begin work only after written approval of the scope and costs.

We ensure full transparency: no surprise costs or unapproved changes. Any adjustments are openly discussed and agreed upon before implementation begins.

### Project Timeline & Due Date Commitment

We are committed to delivering the project on time, provided that both parties engage in proactive and timely communication, and any blockers, dependencies, or required feedback are addressed within 24 hours of being raised.

If a timeline change becomes necessary (e.g., due to scope changes, external delays, or unforeseen circumstances), we will communicate this promptly and propose a mutually acceptable revised schedule.
