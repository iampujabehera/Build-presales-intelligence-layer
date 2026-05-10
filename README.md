# Presales Intelligence Hub

An AI-powered presales companion for solutions consultants — built for **Zycus**.
The Hub turns customer call transcripts and CRM signals into ready-to-share documentation, mapped solution responses, value narratives, and a live opportunity pipeline.

> **Status:** UI prototype with mock data. Production integrations (Avoma · Salesforce · Zycus KB 360 · RFP Repository · zycus.com Customer Stories) are stubbed and ready to be wired through MCPs.

---

## 1. Quick start

The prototype is a single self-contained HTML file — no build step, no install.

### Option A: open directly
Double-click `index.html`. Works in any modern browser (Chrome, Edge, Firefox, Safari).

### Option B: run a local server (recommended for stakeholder demos)
```powershell
# from the project folder
npx serve -p 5173 .
# then open http://localhost:5173
```

---

## 2. Who this is for

| Role | What they get |
|---|---|
| **Solutions Consultant (primary user)** | One-click documentation from every customer call · always-current opportunity view · pre-call account intelligence brief |
| **Account Executive** | Pipeline visibility · auto-generated value documents to send post-call · stakeholder map |
| **Sales Leadership / Executives** | Live exec dashboard across all accounts · weighted pipeline · stage-by-stage view · case-study library |

---

## 3. The two tabs

The left sidebar exposes exactly two surfaces, plus the connected knowledge sources.

### 3.1 Documentation Agent
A single tab consolidating accounts, calls, and auto-generated documents.

**Dashboard:**
- 4 KPIs: Active accounts · Pipeline value · Documents generated · Late-stage deals
- Filters: account search, industry, region, Salesforce stage, last-call date range
- Account table with stage pills, deal size, calls count, document-readiness, health score

**Account detail page** (click any account row) — five tabs:
1. **Overview** — most recent call snippet, top pain points, document hub, action items
2. **Calls & Transcripts** — calls pulled from Avoma with transcript snippets
3. **Minutes of Meeting** — date · attendees (org-grouped) · key discussion · open points · next steps with owner & due date
4. **Solutioning** — Q&A mapping with status badges (Answered live / Zycus KB / RFP Repo / Pending) + source breakdown
5. **Value Document** — pain points · current process · industry benchmarks vs. best-in-class · filtered case studies (industry + region match) · projected $ impact

**Generate from new call** flow simulates the AI ingestion: pulls Avoma transcript → drafts MOM → maps unanswered questions to Zycus KB & RFP repo → drafts solutioning doc → matches case studies → drafts value document.

**Downloads:** every document is downloadable as `.doc` (opens in Word) and printable to PDF via the browser. Pipeline view exports as CSV.

### 3.2 Opportunity Tracker
Live mirror of Salesforce opportunities — purpose-built as an executive pipeline view.

**KPIs:** Total pipeline · Weighted pipeline (stage-probability adjusted) · Closed won YTD · Open opportunities (with avg days-to-close)

**Pipeline by stage strip:** seven cards covering the full Salesforce stage taxonomy:
1. Initial Interest (10% probability)
2. Qualified (25%)
3. Formal Evaluation (40%)
4. Shortlisted (60%)
5. Vendor Selected (80%)
6. Contract in Progress (95%)
7. Contract Signed (100%)

Click any stage card to filter the table to that stage.

**Wide table columns:**
- Account / Module (clickable → opens Account Intelligence)
- **Opportunity ID** (Salesforce 18-char ID + internal reference shown below; clickable → opens the Opportunity record in Salesforce in a new tab; pencil icon → opens the "wrong opp ID" editor)
- Stage · Type (New Business / Cross-sell / Upsell / Pilot)
- Amount · Services · Total
- Owner · Presales (from Salesforce fields)
- Last connect date · Close date
- Open in SF button

**One opportunity per module pitched.** When a customer call covers multiple Zycus modules, each module shows up as its own opportunity. Pfizer for example has three: Source-to-Pay Suite, Merlin Supplier Risk, Merlin Sourcing AI.

**Wrong opportunity ID?** Click the pencil next to any Opp ID, paste the correct one, and the row's amount, services, type, owner, presales, close date, and last-activity fields are pulled fresh — only that record is touched.

**Sync now** triggers a full re-pull from Salesforce. **Export CSV** dumps the entire filtered pipeline.

### 3.3 Account Intelligence (deep-link from Opportunity Tracker)
Click any account name in the Opportunity Tracker to land on the Account Intelligence view.

**Hero banner** with public-record metadata: ticker (e.g. NYSE:PFE), headquarters, headcount, latest FY revenue, current stage, opportunity count, total pipeline, intel last sync time.

**Procurement decision-makers panel** — for each account, 4 stakeholders relevant to a procurement-SaaS purchase decision:
- CFO · CEO · CPO/VP Procurement · Director-level procurement role
- Each profile carries a procurement-relevant note ("Approves capex >$10M", "Hard gate on Scope-3 traceability", "POC sponsor")
- Source link to LinkedIn or the company's official leadership page

**News feed** — last 90 days, sorted newest first, with five colour-coded categories:
- 🤝 **M&A** — mergers, acquisitions, divestitures
- 📈 **Funding** — earnings prints, IPOs, capex programmes, capital raises
- ✨ **AI / Digital** — agentic AI rollouts, digital transformation initiatives, technology partnerships
- 👤 **Leadership** — new C-suite or procurement appointments
- 🎯 **Strategic** — restructures, partnerships, regulatory, sustainability

Each news item shows the category pill, date, source publication, a "New" badge for items within 7 days, the headline (linked to the actual article), a 1–2 line snippet, and a "Read on [source] →" CTA.

Sources used in the prototype mirror real publications: Reuters, Bloomberg, Financial Times, Wall Street Journal, CNBC, FierceBiotech, Business Standard, The Economic Times, Mint, eFinancialNews, Computer Weekly, gCaptain, Journal of Commerce, Forbes India, Aviation Week, Der Spiegel, Oil & Gas Journal, Procurement Magazine, Walmart Newsroom, Pfizer Newsroom, Lufthansa Press, Maersk Press, LinkedIn.

**Refresh feeds** button simulates a re-pull from public sources.

---

## 4. Mock data shape

The prototype ships with a realistic data set so demos feel real:

| Object | Count | Where it lives |
|---|---|---|
| Accounts | 10 | `ACCOUNTS` constant |
| Calls per account | 1–3 | `CALL_DATA` constant |
| MOM / Solutioning / Value docs | Per account | `DOCS` constant + `ensureDocs()` fallback |
| Opportunities | 18 (one per module pitched) | `OPPORTUNITIES` constant |
| Stakeholders | 4 per account = 40 total | `ACCOUNT_INTEL[id].stakeholders` |
| News items | 5–6 per account = ~55 total | `ACCOUNT_INTEL[id].news` |

**Accounts in the demo set:** Pfizer · Tata Steel · Standard Chartered · Unilever · Maersk · Reliance Industries · ExxonMobil · Nestlé · Walmart · Lufthansa Group.

Pfizer, Tata Steel, Standard Chartered, and Unilever are fully fleshed out (full Q&A mappings, case studies, benchmarks). The remaining accounts have lighter scaffolding — enough for the demo to feel real, easy to extend.

---

## 5. MCP / integration plan

The prototype is structured so each integration drops in at a single, named seam.

| Integration | Replaces | Source of truth |
|---|---|---|
| **Avoma MCP** | `CALL_DATA[<accountId>]` (call list + transcript snippets) | Avoma calls API (filtered by account email domain or CRM linkage) |
| **Salesforce MCP** | `OPPORTUNITIES` array + `ACCOUNTS[].stage` field + presales/owner/close-date/amount/services-amount fields | Salesforce REST API — `Opportunity` object filtered by `OwnerId` or `Presales_Consultant__c` |
| **Zycus KB 360 MCP** | `DOCS[<id>].solutioning.qa[]` items with `status: 'kb'` | Zycus Knowledge Hub search API |
| **RFP Repository MCP** | `DOCS[<id>].solutioning.qa[]` items with `status: 'rfp'` | Zycus RFP Repository search API |
| **Customer Stories scraper** | `DOCS[<id>].value.caseStudies` | `https://www.zycus.com/customers/success-story` filtered by region · sector · application · benefits |
| **Public news / stakeholder MCP** | `ACCOUNT_INTEL[<id>].stakeholders` and `.news` | NewsAPI / GDELT / Bloomberg feed by ticker + LinkedIn / Crunchbase company API |

Each integration is a JSON shape — no UI changes required when MCPs are wired in.

---

## 6. Project structure

```
.
├── index.html              # the entire prototype: HTML + Tailwind (CDN) + Lucide icons + vanilla JS
├── ai_studio_code.html     # original Vite stub (unused)
└── README.md               # this file
```

External dependencies (loaded from CDN at runtime):
- Tailwind CSS · Lucide icons · Inter font (Google Fonts)

---

## 7. Salesforce stage taxonomy

The prototype uses the seven-stage taxonomy you asked for, with these stage probabilities applied for the weighted-pipeline calculation:

| Stage | Probability | Pill colour |
|---|---|---|
| Initial Interest | 10% | slate |
| Qualified | 25% | sky |
| Formal Evaluation | 40% | violet |
| Shortlisted | 60% | amber |
| Vendor Selected | 80% | orange |
| Contract in Progress | 95% | emerald |
| Contract Signed | 100% | green |

---

## 8. Roadmap

**Near-term (next iteration):**
- Wire Avoma MCP for live transcript ingestion
- Wire Salesforce MCP — replace mock `OPPORTUNITIES` with live opp pull
- Auth (SSO via Okta / Azure AD)
- Save edited opp IDs back to user-level config

**Mid-term:**
- Multi-user — each Solutions Consultant sees their own pipeline
- Document version history (regenerate without losing prior versions)
- "Compare against last call" — diff what's new vs. last MOM for the same account
- Email-out from inside the Hub (send the value doc straight to the customer)

**Long-term:**
- Voice query: "What's open in Pfizer this week?" → narrative answer
- Predictive close-date model based on call cadence + sentiment
- Auto-generated executive briefing one hour before each scheduled call

---

## 9. Author

**Anuradha Mondal** · Solutions Consultant, Zycus
