---
name: intempt-docs
description: >
  Full documentation writing skill for Intempt. Contains complete product context for
  all 17 modules (with real SDK method signatures, API endpoints, use cases, gotchas, and
  inter-module connections), structural patterns scraped from Stripe/Twilio/Algolia/Mixpanel/
  Notion/Intercom/Linear, writing rules, article title formulas, parameter documentation
  standards, and a prioritised list of every article that needs to be written or updated.
  Use this skill for any help-doc, developer-doc, conceptual, tutorial, how-to, reference,
  or API article for Intempt.
---

# Intempt Docs — Writing Skill

Self-contained. Everything needed to write, review, or commission Intempt documentation.

---

## 1. WHAT INTEMPT IS

Intempt is a unified Growth OS for product-led SaaS and DTC ecommerce companies.
It replaces a disconnected stack of CDP, marketing automation, analytics, A/B testing,
CRM, and creative tools with one platform.

**Three core use cases (from the product login screen):**
- **Lifecycle Orchestration** — Maximize customer lifetime value
- **AI Native Sales** — Signal-driven pipeline system
- **On-Site Conversion** — Optimize testing, personalization & discovery

**Core philosophy:** Automatic events give context. Manual APIs give meaning.
Inbound, outbound, and product are not three different data silos — they share
the same behavioral customer layer.

**Two doc surfaces:**
- `docs.intempt.com` — developer docs: SDK, API, event tracking, technical setup
- `help.intempt.com` — help docs: product walkthroughs, how-tos, customer success

**Current help.intempt.com structure (~120 articles across 15 categories):**

| Category | Articles |
|---|---|
| Getting Started | 39 |
| Orgs & Projects | 5 |
| How Intempt Works | 8 |
| Customer Data Platform | 2 |
| Audiences | 16 |
| Journeys | 6 |
| Messages | 8 |
| Experiments | 10 |
| Recommendations | 4 |
| Personalizations | 5 |
| Agents | 10 |
| Analyze | 8 |
| Privacy | 6 |
| Billing | 1 |
| Changelog | 22 |

---

## 2. THE FIVE PILLARS — COMPLETE MODULE CONTEXT

Each module entry includes: what it does, what the user actually does first, key
settings and options, how it connects to adjacent modules, and critical gotchas.

---

### CONTEXT PILLAR — Know your customer

---

#### DATA HUB

**What it is:** Unified behavioral CDP. Every customer touchpoint from every source
in one place. Merges anonymous and identified user events into a single profile.

**What a user does first:**
1. Install the JS SDK (copy snippet, paste in `<head>`)
2. Events start flowing automatically via autocapture
3. Call `intempt.identify()` when a user logs in to link the session to a known user
4. View incoming events in Live Data Feed to validate
5. Build segments and analytics on top of the unified profile

**Key settings and options:**
- Project separation: always create separate dev, staging, and production projects
- Sources: JS SDK, iOS SDK, Android SDK, Node.js, REST API, GTM, HubSpot, Shopify, Stripe
- Identity resolution: `userId` (primary), `anotherUserId` (secondary merge), `profileId` (internal)
- `accountId` for B2B company-level grouping
- Live Data Feed shows events in real time powered by ClickHouse

**How it connects:**
- Data Hub is the foundation everything else reads from — Segments, Journeys, Analytics,
  Experiments, Personalize, Recommendations, Workflows all require clean Data Hub data
- Autocapture covers context (page views, clicks, sessions); custom events add meaning (purchase, signup)
- Identity resolution feeds Journeys with correct user targeting

**Gotcha:** Never send product catalog data with a `userId` — it creates anonymous user profiles.
Use `productId` in the payload instead. Revenue tracking uses a `Charge` event (not `Purchase`),
must originate from backend systems only, never from the browser.

**Reserved event titles that cannot be used:**
`'auto-track'`, `'view page'`, `'leave page'`, `'change on'`, `'click on'`, `'submit on'`,
`'identify'`, `'consent'`

---

#### INTEGRATIONS (SDK & Sources)

**What it is:** The technical layer connecting data sources to Intempt. JS snippet for web,
native SDKs for mobile, REST API for backend, GTM for no-code deployment.

**What a user does first:**
1. Go to Integrations → Sources → Create Source
2. Choose platform (Web, iOS, Android, Node, GTM, cloud app)
3. Copy the generated snippet or API key
4. Install and validate using Live Data Feed

**Key SDK methods (JavaScript):**

| Method | Purpose |
|---|---|
| `new IntemptJs(config)` | Initialize with org, sourceId, project, writeKey |
| `intempt.identify({userId, userAttributes})` | Link session to known user |
| `intempt.alias({userId, anotherUserId})` | Merge two identifiers (one-time) |
| `intempt.group({accountId, accountAttributes})` | Associate user with company/account |
| `intempt.track({eventTitle, data})` | Record user action with event data |
| `intempt.record({eventTitle, userId, data, userAttributes})` | Structured business moment |
| `intempt.consent({action, validUntil, category})` | Record accept/reject consent |
| `intempt.optIn()` / `intempt.optOut()` | Consent control |
| `intempt.productView(productId)` | Track product view (recommendations) |
| `intempt.productAdd({productId, quantity})` | Track add to cart |
| `intempt.productOrdered([{productId, quantity}])` | Track purchase |
| `intempt.recommendation({id, quantity, fields})` | Fetch personalized products |
| `intempt.logOut()` | Clear session data on user logout |

**Cloud app integrations:**
- HubSpot: sync contacts, companies, deals (one-way or two-way)
- Shopify: sync orders, customers, products
- Stripe: sync customers, subscriptions, billing

**Gotcha:** GTM deployment is for teams without direct code access. For production apps
with code access, use the JS SDK directly for more control. Always validate installation
in Live Data Feed before building on top of it.

---

#### CONSENT

**What it is:** GDPR/CCPA consent collection, preference center, and data subject
requests (delete, export, right-to-be-forgotten).

**What a user does first:**
1. Configure a consent banner using Intempt's cookie consent builder
2. Connect it to `intempt.consent({action: 'accept'|'reject', validUntil, category})`
3. Set up a Preference Center for users to manage their choices
4. Handle Data Subject Requests (DSR) from the Privacy settings panel

**Key settings:**
- `action`: `'accept'` or `'reject'`
- `validUntil`: timestamp (milliseconds) or `'unlimited'`
- `source`: `'desktop'` or `'mobile'`
- `category`: e.g., `'analytics'`, `'marketing'`
- DSR operations: delete, export, right-to-be-forgotten

**Consent API endpoint:**
`POST /{orgName}/projects/{projectName}/consents/data`

**How it connects:** Consent state is evaluated in real time and affects all downstream
tracking. Call `intempt.isUserOptIn()` before tracking in consent-first flows. Journeys
and Webhooks are consent-aware by design.

**Gotcha:** Consent must be called before any tracking in GDPR-regulated markets. Never
skip the consent check by wrapping tracking in try/catch and swallowing errors.

---

### INTELLIGENCE PILLAR — Understand your customer

---

#### AI SEGMENTATION

**What it is:** Dynamic audience builder. Behavioral segments, RFM scoring, and predictive
AI audiences that update in real time as user behavior changes.

**What a user does first:**
1. Go to Audiences
2. Click "Create Segment"
3. Define conditions: event-based (did X), attribute-based (property = value),
   sequence-based (did X then Y within 7 days), or use RFM scoring
4. Preview the matching audience size
5. Use the segment in Journeys, Experiments, or Personalizations

**Key capabilities:**
- Event-based conditions: "performed purchase at least 3 times in last 30 days"
- Attribute conditions: "plan = enterprise AND country = US"
- Sequence conditions: "signed up then completed onboarding within 7 days"
- RFM scoring: Recency, Frequency, Monetary — scored tiers (e.g., Champions, At Risk)
- Predictive: AI-generated churn risk and LTV audiences
- Segments are dynamic — always reflect current behavior, not a snapshot

**How it connects:** Segments are the entry condition for Journeys, the targeting
layer for Experiments and Personalize, and the audience selector for Agents.

**Gotcha:** Segment conditions evaluate against the unified profile, so they require
clean event tracking first. A segment for "completed onboarding" only works if the
"onboarding_completed" event is being tracked.

---

#### ANALYTICS

**What it is:** Dashboards, funnel analysis, retention curves, cohort analysis, and
custom reports. Reads from the unified profile in Data Hub.

**What a user does first:**
1. Go to Analyze
2. Open or create a dashboard
3. Add a chart: funnel, retention, event count, property breakdown
4. Set a time range and filter by segment
5. Save the view and share with the team

**Key report types:**
- Funnel analysis: Define steps (e.g., signup → activation → paid), measure drop-off
- Retention curves: Day-0, Day-7, Day-30 return rates by cohort
- Cohort analysis: Group users by signup date or event, track behavior over time
- Event analytics: Volume, trend, breakdown by property
- Custom dashboards: Save filter combinations as named views

**How it connects:** Analytics reads from Data Hub events. Segments can be applied as
filters. Experiment results appear in Analytics as A/B test outcome reports.

**Gotcha:** Reports only reflect events that were tracked. If `purchase` was tracked
under two different event names in different SDKs (e.g., `Purchase` vs `purchase`),
they appear as separate events. Event naming conventions matter.

---

#### RECOMMEND

**What it is:** AI-powered product recommendation engine. Generates ranked product
lists using collaborative filtering, content-based, or hybrid algorithms.

**What a user does first:**
1. Connect a product catalog (via Catalog Source URL or API)
2. Map product events to recommendation events (Product Viewed, Added to Cart,
   Removed from Cart, Product Ordered)
3. Create a Recommendation Feed with a strategy (Most Popular, Recently Viewed,
   Purchased Together, Image Similarity, etc.)
4. Fetch recommendations via JS SDK or Recommendations Feed API
5. Optionally A/B test feed strategies

**Key strategies:**
- Most Popular: highest view/purchase count in time window
- Recently Viewed: user's own history
- Purchased Together: collaborative filtering on co-purchase data
- Image Similarity: visual similarity model (requires image_link in catalog)
- User Affinity: personalized based on user's behavioral history

**Recommendations Feed API:**
`POST /{orgName}/projects/{projectName}/feeds/{id}/data`
Returns `{ "products": [{id, price, link, ...}] }` — request specific fields with `fields[]`

**Event mapping — required for recommendations to work:**
| Your event name | Maps to | Required attribute |
|---|---|---|
| Whatever you call "view" | Product Viewed | productId |
| Whatever you call "add to cart" | Added to Cart | productId |
| Whatever you call "remove from cart" | Removed from Cart | productId |
| Whatever you call "purchase" | Product Ordered | productId |

**Gotcha:** Product catalog ingestion uses `productId` as the key, NOT `userId`.
If you accidentally send product data with a `userId`, Intempt creates anonymous
user profiles for each product. Always send catalog data via catalog source or
with `productId` in the Track API payload (no userId needed).

---

### STUDIO PILLAR — Create for your customer

---

#### STUDIO (Builder)

**What it is:** Drag-and-drop visual builder for landing pages, emails, SMS messages,
and push notifications. All outputs are stored in the Asset Library.

**What a user does first:**
1. Go to Studio
2. Choose asset type: Landing Page, Email, SMS, or Push
3. Start from blank or pick a template
4. Drag in blocks (hero, CTA, image, text, button, countdown)
5. Use Blu AI assistant to generate copy, suggest layouts, or match brand voice
6. Publish to CDN (landing pages) or send via Messages (email/SMS)

**Key settings:**
- Brand Kit auto-applies org-level colors, fonts, and logos
- Liquid tags `{{first_name}}` and `{{event.property}}` for personalization
- Snippets: reusable content blocks for headers, footers, legal text
- A/B testing: create variants directly from the builder and connect to Experiment

**How it connects:** Studio → Asset Library → Messages (for delivery) or
Landing Page URL (for Journey CTAs or ad destinations). Experiment uses Studio
variants for A/B tests. Personalize injects Studio content on-site.

---

#### MESSAGES

**What it is:** Multi-channel message composer and delivery layer. Email, SMS, Slack,
and push notifications. Called from Journey blocks or sent as one-off campaigns.

**What a user does first:**
1. Go to Messages → Create Message
2. Choose channel: Email, SMS, Push, Slack
3. Write content using the Studio email editor or SMS composer
4. Add Liquid tags for personalization: `{{user.first_name}}`
5. Set sender name, reply-to, subject line (email)
6. Preview on desktop/mobile
7. Send as a campaign or save as a Journey block

**Key settings:**
- Liquid tags: `{{user.first_name}}`, `{{user.email}}`, `{{event.property_name}}`
- Snippets: saved content blocks (headers, footers, legal)
- Deliverability: SPF/DKIM/DMARC authentication required for email
- SMS: character limits apply; opt-out compliance mandatory

**Gotcha:** Deliverability requires proper SPF, DKIM, and DMARC setup. Skipping
authentication causes email to land in spam. Document this as a prerequisite, not
an optional step.

---

#### BRAND KIT

**What it is:** Org-level visual identity settings. Colors, fonts, and logos
stored once and auto-applied across all Studio creative outputs.

**What a user does first:**
1. Go to Settings → Brand Kit (or Studio → Brand Kit)
2. Upload logos (light and dark versions)
3. Set primary, secondary, and accent colors (hex values)
4. Configure typography: primary and secondary font families
5. Save — Brand Kit auto-applies to all new Studio designs

**How it connects:** Brand Kit feeds Design Systems (which lock layouts too), and
both feed Blu AI so generated content stays on-brand. One change in Brand Kit
propagates to all assets that reference it.

---

#### BLU (AI Brand Assistant)

**What it is:** Intempt's AI assistant powering all Studio creation. Uses company
knowledge, brand identity, ICP, voice and tone, and customer profiles to generate
on-brand content.

**What a user does first:**
1. Set up Company Knowledge: upload brand guidelines, product descriptions, ICP definition
2. Configure voice and tone: choose adjectives and upload example copy
3. Open any Studio editor and invoke Blu via the AI assistant panel
4. Prompt naturally: "Write a welcome email for a trial user who just installed our SDK"
5. Blu generates copy using brand context — edit and publish

**Blu powers:**
- Copy generation in Studio (email, landing page, SMS)
- Marketing automation Recipes (builds full campaign infrastructure from a prompt)
- Creative Recipes (product shots, photoshoots, ad builders)

---

#### AVATARS

**What it is:** 18 built-in AI avatars with visual and vocal identity. Used in
video creation and image generation within Studio.

**Key features:**
- 18 base avatars, each with a visual appearance and default voice
- Customizable emotions: "explosive rage", "manic laughter", "serene calm", etc.
- Emotion tags can be embedded in video prompts to control tone mid-scene
- Used in Video Editor for talking-head and presenter-style content

---

#### POSES & SCENES

**Poses:** 30+ library of body positions for product and model photography.
Used in AI image generation (product shots, photoshoots). Remixable.

**Scenes:** Structured categories for AI image/video generation:
film stock, style, settings, props, shot type, camera angle, lighting, surface, backdrop.
Can be mixed by blocking categories or using free-form prompts.

---

#### DESIGN SYSTEMS

**What it is:** Org-level lock on fonts, colors, and layouts. Applied automatically
to emails and creative assets when activated.

**Difference from Brand Kit:** Brand Kit stores identity (colors, fonts, logos).
Design Systems enforce structure — locking layouts, spacing, and component styles
so no creative goes off-template.

---

#### VIDEO EDITOR

**What it is:** AI shot-based video editor. Takes a source video (up to 30 seconds),
auto-splits it into 8-12 shots, and lets you edit each shot individually.

**What a user does first:**
1. Upload or record a 30-second source video
2. Intempt auto-splits into shots (8-12)
3. Select a shot to edit: use sketch tool, upload a custom frame, or type a prompt
4. Apply motion instructions: "dolly in", "zoom out", "pan left"
5. Use Multi-Edit to apply one change across multiple shots simultaneously
6. Use Extend to generate more content from any shot
7. Export the final video

**LLM tiers:**
| Setting | Model | Use case |
|---|---|---|
| Auto / Standard | Cling | Fast iteration, rough cuts |
| Premium | Cling Pro | Polished output |
| Max | VO3 | Highest quality, final production |

**Built-in video recipes:**
- Video Reel: Script + Avatar + product = post-ready reel
- Video Remix: 4 reference videos → branded output reel
- Product Shot Video: animated product showcase
- Image to Dialogue: static image + script → talking video
- Multi-Shot Video: storyboard to full video
- Upscale Video: increase resolution of existing video

---

#### ASSET LIBRARY

**What it is:** Central store for all generated images, videos, emails, and
landing pages. Draft/publish system with CDN propagation.

**Draft/Publish system:**
- **Draft:** asset is local, not yet live
- **Publish:** sends asset to CDN, changes propagate everywhere it is used
- **Revert to Draft:** unpublishes the asset, removes it from CDN

**How it connects:** Every Studio output lands in the Asset Library. Journey blocks
that reference an email or landing page URL pull from the published CDN version.
When you update an asset and republish, all references update automatically.

---

#### RECIPES

**Two types:**

**Marketing automation recipes:** Multi-object creation triggered from Blu.
One prompt creates: attributes + segments + journeys + dashboards together.
Example: "Set up a 7-day trial activation journey" → Blu creates the segment,
the journey with 3 emails, and a dashboard to track activation rate.

**Creative recipes:** Product shots, ad builders, photoshoots.
Example: "Pack shots on model" → takes your product catalog + avatar → generates
product model photography at scale.

**Community recipes:** Any user can publish a recipe to the library and earn $20 per recipe.

---

### EXECUTION PILLAR — Reach your customer

---

#### EXPERIMENT

**What it is:** A/B, multivariate, mobile, and web experimentation. Visual editor
for no-code test creation. Statistical significance with CUPED variance reduction.

**What a user does first:**
1. Go to Experiments → Create Experiment
2. Choose type: A/B (web), multivariate (web), mobile, or server-side
3. For web/mobile: open Visual Editor, make changes to variant B
4. Set the success metric (primary goal event)
5. Define traffic split (e.g., 50/50)
6. Set statistical confidence threshold (default: 95%)
7. Launch and monitor in Experiment Analytics

**Key capabilities:**
- Visual Editor: point-and-click UI changes without code
- Server-side experiments: use Choose API for backend rendering
- CUPED: variance reduction using pre-experiment data for faster significance
- Sequential testing: check results without inflating false positive rate
- Mobile experiments: separate from web visual editor

**Choose API (server-side experiments):**
`POST /{orgName}/projects/{projectName}/optimization/choose-api`
Returns: `{ "choices": [{ "name": "experiment-name", "group": "group", "body": {...} }] }`

**How it connects:** Experiment results feed into Analytics dashboards.
Winning variants can be promoted to Personalize for permanent personalization.
Studio builds the variant content.

---

#### PERSONALIZE

**What it is:** Rule-based and AI-driven on-site personalization. Visual editor
for creating content variations shown to specific segments.

**Difference from Experiment:** Experiment tests to find a winner. Personalize
delivers the right experience permanently, with no winner/loser framing.

**What a user does first:**
1. Go to Personalizations → Create Personalization
2. Open the Visual Editor (different from Experiment's visual editor)
3. Make changes to the content shown to a specific segment
4. Define the audience: choose from existing segments
5. Set priority if multiple personalizations overlap
6. Activate

**AI-driven option:** Instead of setting rules, let Intempt AI choose the best
variant per user based on predicted engagement.

---

#### JOURNEYS

**What it is:** Omnichannel lifecycle automation. Email + SMS + push + in-app
in one canvas. Trigger on events, segments, schedules, or API calls.

**What a user does first:**
1. Go to Journeys → Create Journey
2. Set an entry trigger: event occurred, user enters segment, schedule, or API
3. Drag in blocks: Send Email, Send SMS, Wait (time delay), Condition (if/else),
   A/B Split, Goal (exit on conversion)
4. Connect blocks to form the flow
5. Activate and monitor in Journey Analytics

**Trigger types:**
- Event trigger: "user fired purchase event"
- Segment trigger: "user entered 'At Risk' segment"
- Schedule trigger: "every Monday at 9am"
- API trigger: external system calls the Journey entry endpoint

**Block types:** Send Email, Send SMS, Push Notification, Slack Message, Wait,
Condition (true/false branch), A/B Split, Goal, Exit

**How it connects:** Journeys pull from Segments (entry conditions), Messages
(content blocks), and Webhooks (outbound actions). Journey analytics show
step-by-step drop-off and conversion rates.

**Gotcha:** Journeys are consent-aware — users who opt out will not receive
messages from Journey blocks. Test consent flows before going live.

---

#### WORKFLOWS

**What it is:** GTM and RevOps automation engine. Broader than Journeys —
handles CRM records, AI tasks, and integrations in addition to messaging.

**What a user does first:**
1. Go to Workflows → Create Workflow
2. Set a trigger (event / segment / schedule / record change / API)
3. Add steps from four categories: Records, AI, Flow Control, Integrations
4. Connect steps into a flow
5. Test with a sample record
6. Activate and monitor run history

**Trigger types:**
- Event trigger: behavioral event fires
- Segment trigger: user enters or leaves a segment
- Schedule trigger: cron-like schedule
- Record change trigger: CRM field updates
- API trigger: external call

**Step categories:**
| Category | Steps |
|---|---|
| Records | Find records, Update attribute, Assign owner, Create task, Create deal, Link records, Enrich |
| AI | Write with AI, AI Research, Run Recipe, Web Scrape, Display Agent |
| Flow Control | Delay, Wait until, True-false branch, Multi-split branch, Loop |
| Integrations | Slack, HubSpot, Webhook-Send |

**Difference from Journeys:** Journeys = customer lifecycle messaging (email, SMS, push).
Workflows = internal GTM operations (lead routing, CRM updates, sales automation).
A Workflow might route a new trial user to an SDR in HubSpot; a Journey sends that
user their onboarding emails.

---

#### PIPELINE

**What it is:** CRM layer for deal and account management. Bi-directional sync
with Salesforce and HubSpot.

**What a user does first:**
1. Go to Pipeline → Setup Stages
2. Create pipeline stages (e.g., Discovery → Demo → Proposal → Closed Won)
3. Import or create accounts and deals
4. Connect Salesforce or HubSpot for bi-directional sync
5. Manage deals from the Kanban or list view

**Key settings:**
- Stages: fully customizable names and order
- Salesforce sync: field mapping, sync frequency
- HubSpot sync: contact and deal mapping
- Deal properties: value, close date, owner, custom fields
- Account view: all deals and contacts for a company

**How it connects:** Pipeline is the CRM backbone for the Meetings and Scheduling
modules. Meetings auto-log to the associated deal. Workflows can create/update
deals based on behavioral triggers (e.g., trial → paid converts to a deal).

---

#### MEETINGS

**What it is:** Call recording, AI summaries, key moment extraction, and live
meeting assist. Available on the Desktop app.

**What a user does first:**
1. Install the Desktop app (macOS or Windows)
2. Join a call — Intempt automatically starts recording (with consent)
3. After the call: AI generates a summary and tags key moments
4. Edit the summary and share clips to Slack or CRM
5. Use shared clips to trigger a Journey or Workflow follow-up

**Key capabilities:**
- AI call summaries: action items, sentiment, key quotes
- Key moments: automatically flagged decision points, pricing discussions, objections
- Share clips: send a 30-second clip to Slack, log to a CRM deal
- Live meeting assist: real-time AI coaching during an active call (Desktop app only)
- Journey triggers: "call ended" can trigger a follow-up email sequence

---

#### SCHEDULING

**What it is:** One-click booking with calendar sync. Google and Outlook supported.

**What a user does first:**
1. Go to Scheduling → Connect Calendar (Google or Outlook)
2. Set availability: working hours, buffer times, meeting types
3. Publish booking link (public URL)
4. Embed booking link in emails, journeys, or landing pages
5. Booked meetings auto-sync to calendar and Pipeline

**Key settings:**
- Meeting types: 15 min / 30 min / 60 min (customizable)
- Buffer times: before and after each meeting
- Working hours: per-day availability
- Booking confirmation: automatic confirmation email

---

### PLATFORM — Manage everything

---

#### AI AGENTS

**What it is:** Configurable intelligent agents that trigger on customer behavioral
signals. Autonomous decision-makers that act without manual journey setup.

**Common use cases:**
- Churn prevention: user inactivity for 14 days → trigger re-engagement
- Upsell: user hits plan limit → trigger upgrade prompt
- Win-back: churned user visits pricing page → trigger win-back offer
- NPS trigger: user reaches 90-day tenure → send NPS survey

**What a user does first:**
1. Go to Agents → Create Agent
2. Set the trigger signal: event, segment entry, attribute threshold
3. Define the agent's decision logic: conditions and branches
4. Set the action: send message, create deal, assign task, fire webhook
5. Monitor agent performance in Agent Analytics

---

#### DEVELOPER (APIs)

**Complete API inventory from docs.intempt.com:**

| API | Endpoint | Purpose |
|---|---|---|
| Track Data | `POST /{org}/projects/{proj}/sources/{sourceId}/track` | Ingest events from any source |
| Consent | `POST /{org}/projects/{proj}/consents/data` | Record user consent decisions |
| Choose | `POST /{org}/projects/{proj}/optimization/choose-api` | Resolve server-side experiments |
| Recommendations Feed | `POST /{org}/projects/{proj}/feeds/{id}/data` | Fetch ranked product lists |

**Authentication:** All endpoints use `?apiKey=identifier.secret` as a query parameter.

**Base URL:** `https://api.intempt.com/v1`

**DOM events emitted by autocapture:**
| Event | When | Key properties |
|---|---|---|
| `intempt:html` | User interacts with DOM element | `eventName`, `target` |
| `intempt:page` | Page view or page leave | `eventName`, `fullUrl`, `title`, `pageId`, `duration` |
| `intempt:session` | Session start or end | `type`, `eventCounter`, `duration`, `region`, `country` |

---

#### MOBILE APP

**What it is:** iOS and Android app for monitoring and executing from anywhere.

**Key capabilities:**
- Record meetings on the go
- Monitor journey status and step performance
- Track experiment status and results
- Browse user accounts and profile data
- View analytics dashboards and key metrics
- Check workflow run history

**Currently on waitlist — positioning: "The web app in your pocket."**

---

#### DESKTOP APP

**What it is:** macOS and Windows app built for sales reps who live in calls.
Live meeting assist, AI coaching, account browsing, sales execution.

**Key capabilities:**
- Live meeting assist: real-time AI prompts during an active call
- AI coaching: post-call talk ratio analysis, sentiment flags
- Account browsing: pull up user context mid-call
- Meeting recording and summary generation
- Scheduling and calendar integration

**What separates Desktop from web:** Live meeting assist only runs from the Desktop
app. Web cannot intercept system audio in real time.

---

## 3. DOC TYPES AND WHEN TO USE THEM

Every article is exactly one of five types. Decide before writing.

| Type | Purpose | Question it answers | Length |
|---|---|---|---|
| **Conceptual** | Explains what something is and why it exists. Not how to use it. | "What is this and why does it matter?" | 300–600 words |
| **Tutorial** | Guided walkthrough from zero to a specific first result. Holds the reader's hand. | "How do I get started?" | 600–1200 words |
| **How-to** | Task-focused. Assumes reader knows what they want to do. | "How do I do X?" | 300–800 words |
| **Reference** | Look-up tables, schema, option lists. No narrative. Scannable. | "What are the options/values/parameters?" | Any length, must be exhaustive |
| **API** | One endpoint or endpoint group. Method, URL, auth, params, examples. | "How do I call this?" | One article per endpoint group |

**Rule:** One article = one type. Never mix a tutorial with a reference table. Link between them.

---

## 4. ARTICLE STRUCTURE PATTERNS (from real scraped docs)

---

### CONCEPTUAL — Notion/Mixpanel pattern

Open with what the feature IS, not what to click. Then 3-4 core concepts with analogies.

```
# [Feature Name]

[One paragraph: what it is and the problem it solves. Lead with the benefit.
Never start with "In this article, you will learn." Just state the thing.]

## The core concepts

### [Concept 1]
[Plain-language definition — one sentence.]
[Analogy: "Think of it like..."]

### [Concept 2]
[Same pattern]

### [Concept 3]
[Same pattern]

## Next steps

- [Exploratory user: "Understand X in more depth"] → link
- [Ready-to-build user: "Set up your first X"] → link
```

---

### TUTORIAL — Stripe + Intercom pattern

Use labeled H2 steps ("Step 1: [Title]") with role badges and complexity ratings.

```
# [Task-oriented title — imperative verb + goal]

> **Integration effort:** [1-3/5]

**Before you begin:**
- [Prerequisite 1]
- [Prerequisite 2]

> **Note:** [Any critical constraint upfront]

## Step 1: [Specific action verb + object] [Client-side]

[1-2 sentences explaining what this step does and why.]

```javascript
// Context comment: what is happening here
intempt.identify({ userId: 'user@example.com', userAttributes: { plan: 'trial' } })
```

> **Security:** Never put API keys in client-side code. Use environment variables.

## Step 2: [Next specific action]

[...]

## Test your setup

1. [Trigger the action in your product]
2. Open **Live Data Feed** in your Intempt project
3. Confirm events appear within seconds

## See also
- [Related conceptual doc]
- [API reference for this feature]
- [How-to for the next logical task]
```

---

### HOW-TO — Notion + Linear pattern

No intro fluff. Action-oriented title. Steps labeled "Step 1:". FAQ at the bottom.

```
# How to [specific task]

[One sentence max — only if the title needs clarification. Skip if self-explanatory.]

## Step 1: [Action]

1. Navigate to **[Section]** → **[Subsection]**.
2. Click **[Button]**.
3. [Complete the action.]

> **Tip:** [Shortcut or efficiency note]

## Step 2: [Next action]

[...]

> **Note:** [Edge case, limit, or constraint]

## Frequently asked questions

**What happens if [common concern]?**
[Direct answer — 1-2 sentences. Write from the user's fear, not from a generic FAQ template.]

**Can I [common variation]?**
[Answer.]

---

**Up next:** [Related how-to or next logical step] →
```

---

### REFERENCE — Mixpanel SDK + Stripe parameter pattern

Tabbed installation. Method-by-method with parameter tables. Each parameter documented fully.

```
# [Module/Feature] Reference

[One sentence: what this covers and who it is for.]

## Installation

**HTML snippet**
```html
<script>
  (function(a,b,c){...})(...)
  const intempt = new IntemptJs({
    organization: 'YOUR_ORG',
    sourceId: 'YOUR_SOURCE_ID',
    project: 'YOUR_PROJECT',
    writeKey: 'YOUR_WRITE_KEY'
  })
</script>
```

**npm**
```bash
npm install @intempt/js-sdk
```

## Methods

### `methodName(params)`

[One sentence description.]

**Parameters**

| Parameter | Type | Required | Description | Default |
|---|---|---|---|---|
| `param1` | string | Yes | [What it is. Constraints as prose.] | — |
| `param2` | object | No | [What it does. Valid values.] | `{}` |

**Enum values** (for enum parameters):
- `'value_one'` — [what this does]
- `'value_two'` — [what this does]

**Example**

```javascript
// Context: user just completed onboarding
intempt.record({
  eventTitle: 'onboarding_completed',
  userId: 'user@example.com',
  data: {
    steps_completed: 5,
    time_to_complete_seconds: 240
  }
})
```

> **Note:** [Any non-obvious constraint]

### `nextMethod()`

[...]
```

---

### API ENDPOINT — Stripe + Twilio pattern

```
# [Endpoint Name] API

[One sentence: what this endpoint does and when to use it.]

## Endpoint

```
POST /{orgName}/projects/{projectName}/sources/{sourceId}/track
```

## Authentication

```bash
curl -X POST "https://api.intempt.com/v1/demo-org/projects/demo-project/sources/1001/track?apiKey=identifier.secret" \
  -H "Content-Type: application/json" \
  -d '{ ... }'
```

> **Security:** Never expose API keys in client-side code. Use environment variables.

## Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `orgName` | string | Yes | Your organization slug |
| `projectName` | string | Yes | Your project name |
| `sourceId` | integer | Yes | Source identifier |

## Query parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `apiKey` | string | Yes | `identifier.secret` format |

## Request body

[Full JSON schema with field-by-field descriptions. Required vs optional clearly marked.]

## Request examples

**Example 1: [Most common use case]**

```json
{
  "track": [{
    "name": "Purchase",
    "payload": [{
      "userId": "user@example.com",
      "timestamp": 1714631904000,
      "data": { "amount": 99.99, "currency": "USD" }
    }]
  }]
}
```

## Response

```json
{}
```

## Error codes

| Code | Error | Resolution |
|---|---|---|
| `400` | `missing_required_field` | Include all required parameters |
| `401` | `invalid_api_key` | Check your API key in project Settings → API Keys |

## See also
- [SDK method that wraps this endpoint]
- [Validation guide]
- [Event schema reference]
```

---

## 5. ARTICLE TITLE FORMULAS

Scraped from Twilio, Algolia, Stripe, GitHub, Notion, Linear, Intercom.

| Doc type | Formula | Examples |
|---|---|---|
| Quickstart | "Build your first [X]" or "[Get/Set up] [X] in [Y] minutes" | "Build your first Journey" |
| Tutorial | "[Goal] — step-by-step guide" or "Your first [X]" | "Accept your first payment — step-by-step" |
| Conceptual overview | "[Feature] overview" or "About [X]" or "What is [X]?" | "Journeys overview", "About autocapture" |
| Resource reference | "[Noun] reference" or "The [Noun] object" | "JS SDK reference", "Event schema reference" |
| CRUD operation | "[Verb] a [Noun]" | "Create a Journey", "Delete a Segment" |
| API method | Imperative verb phrase | "Track an event", "Fetch recommendations" |
| How-to | "How to [specific task]" | "How to set up Brand Kit", "How to map recommendation events" |
| Troubleshooting | "Troubleshooting [feature]" | "Troubleshooting event tracking" |
| Migration | "Migrate from [X] to Intempt" | "Migrate from Segment to Intempt" |

**Title rules:**
- Never use "Introduction to X" — use "X overview" or "What is X?"
- Never use "A guide to X" — use "How to X"
- API method articles use imperative verbs, never noun phrases
- Quickstarts are task-first — the user's goal, not the product feature name

---

## 6. WRITING RULES

### Voice and person
- Write in second person: "you", not "the user", not "customers"
- Active voice always: "Click **Save**" not "**Save** should be clicked"
- Present tense: "The API returns" not "the API will return"
- One idea per sentence. Two ideas = two sentences

### Opening sentences (Notion rule)
- Never start with "In this article..." or "This guide will explain..."
- Open with what the feature IS: "Journeys are omnichannel lifecycle automations..."
- Then add the problem it solves: "...that send the right message at the right moment."
- Only THEN describe how to use it

### Language
- No marketing language in docs: "powerful", "seamless", "robust", "best-in-class"
- No filler words: "simply", "just", "easily", "quickly", "straightforward"
- Define every Intempt-specific term on first use, then link to the Glossary
- Spell out abbreviations first time: "Customer Data Platform (CDP)"
- US English throughout

### Structure
- Every article: one-sentence description at the top, H1, Next Steps or Related section
- H2 for major sections, H3 for sub-sections. Never deeper than H3
- Numbered lists for sequential steps
- Bullet lists for non-sequential items
- Tables for three or more items that share the same attributes
- Code blocks for every command, snippet, API call, JSON payload

### Step labeling (Intercom rule)
Use "## Step 1: [Title]" as an H2, not just numbered list items.
This makes steps scannable in the sidebar table of contents.

### FAQ sections (Intercom rule)
Every article gets 3-5 FAQs at the bottom. Write them from user fear:
- "What happens if I delete a source with active journeys?"
- "Can I merge profiles after the fact?"
NOT: "Frequently Asked Questions about the Track API"

### "Up next" link (Notion rule)
End every tutorial and how-to with a single "Up next:" link to the logical next article.
This turns docs into a curriculum.

### Callout syntax
```
> **Note:** Supplementary context or a non-obvious edge case.
> **Tip:** Shortcut, best practice, or efficiency recommendation.
> **Warning:** Irreversible action or something that could break an integration.
> **Security:** Always appears on any page showing credentials or API keys.
> **Prerequisite:** What must be done before starting this guide.
```

**Callout hierarchy (from Twilio/Stripe/Intercom):**
1. Security — appears on EVERY page that shows an API key or credential
2. Warning — destructive operations, irreversible actions, trial/plan limits
3. Note — non-obvious behavior, limits, edge cases
4. Tip — efficiency shortcuts
5. Recommendation (top of page) — "use X instead of Y for this use case"

### Limitations stated upfront (Intercom rule)
If a feature has plan-based limits, rate limits, or beta constraints — say so
before the how-to steps in a > **Note:** callout. Never bury it in step 7.

### Code examples (Stripe standard)
- Use realistic values. Not `foo`, `bar`, `test123`, `YOUR_VALUE_HERE`
- Use `YOUR_API_KEY` for API key placeholders
- Add a comment on the line before any non-obvious code
- Separate frontend from backend code — never mix in same block
- Show install commands before first code example
- Language selector on every integration guide (JS minimum; Python, Node where relevant)

### Screenshots
- Include for every step involving UI navigation or form interaction
- Annotate with numbered callouts when multiple elements are referenced
- Outdated screenshots are actively harmful — worse than no screenshot
- Alt text required on every image

### Sparse emoji usage (Notion rule)
Maximum one emoji per article. Place it at the end of the H1 or intro sentence only.
Zero emoji = sterile. More than one = unprofessional.

---

## 7. PARAMETER DOCUMENTATION FORMAT

Scraped from Stripe and Twilio — the most detailed in the industry.

### Full parameter entry format:
```
`parameter_name` (type, required|optional[, nullable])
Description. Constraints stated as prose (minimum, maximum, character limits, format).
Default value stated. Links to related concepts as inline text.

Possible enum values:
- `value_one` — what this value does
- `value_two` — what this value does
```

### Nested parameters use dot notation:
```
`parent_object` (object, optional)
  Description of the parent.
  
  `parent_object.child` (string, required)
    Description of the child field.
```

### Type vocabulary (use these exact terms):
`string`, `integer`, `number`, `boolean`, `enum`, `object`, `array`, `timestamp`

### Rules:
1. Required parameters listed before optional
2. Default values always stated for optional parameters
3. Enums never list values alone — each value gets a description
4. Constraints (min/max, character limits, format) go in the description as prose
5. Nullable is explicitly stated: `(string, optional, nullable)`
6. PII fields flagged with retention period: `(string, PII — 30-day retention)`
7. Cross-reference every mentioned concept with an inline link

---

## 8. MULTI-LANGUAGE CODE RULES

From Twilio, Algolia, Stripe analysis:

1. Show languages as tabs within one code block, not on separate pages
2. All language tabs show identical functionality (not different examples)
3. Follow language naming conventions: `snake_case` Python, `camelCase` JavaScript
4. Always include a `curl` / REST example as the language-agnostic baseline
5. Never mix frontend and backend code in the same block
6. Mobile SDKs (iOS Swift, Android Kotlin) are documented separately from web SDKs
7. Show install command before the first code example:
   ```bash
   npm install @intempt/js-sdk
   # or
   pip install intempt
   ```

---

## 9. BENCHMARK DOCS — WHAT THEY DO AND WHY IT WORKS

### Stripe (gold standard for developer docs)
- **Three-column layout:** prose + code panel side by side. Reader sees both without scrolling.
- **Role badges:** `[Client-side]` `[Server-side]` `[No code]` — one article serves all audiences.
- **Integration complexity rating** at top (1–5). Sets expectations before the reader starts.
- **`## Optional:` prefix** for non-essential sections. Reader can skip without missing the flow.
- **Testing section with real test values** in a table — reader can copy and use immediately.
- **Inline glossary** — terms defined on first use, no separate page required.
- **`## See also`** at the end linking to API reference, related guides, Dashboard links.

### Twilio (gold standard for API reference structure)
- **Resource page structure:** overview → properties table → Create → Fetch → Read → Update → Delete (each as its own H2).
- **PII flagging:** parameters marked `(PII, 30-day retention)`.
- **Status code table:** dedicated two-column table for enum statuses (status | description).
- **Trial mode callout:** separate warning for trial/sandbox limitations, never buried.
- **Sub-resource links** at the bottom: "Related: MessageFeedback sub-resource"
- **Title pattern:** `[Noun] resource`, `[Verb] a [Noun] resource` — consistent throughout.

### Mixpanel (gold standard for analytics product docs)
- **Funnel pattern for conceptuals:** hook → 3 concepts → each with analogy + diagram → two CTAs.
- **SDK reference:** tabbed installation (HTML / NPM / Yarn) + method-by-method with parameter tables.
- **"Edit this page"** GitHub link on every article — trust and transparency signal.
- **Two CTAs with different intent:** one for explorers ("Plan your events"), one for builders ("Install Mixpanel").

### Notion (gold standard for SaaS help docs)
- **Concept-first:** every article opens with "what it IS" before any steps.
- **"Up next:" link at article bottom** — turns docs into a curriculum.
- **Note vs Tip distinction:** Note = edge case/limit, Tip = efficiency shortcut.
- **One emoji max in H1** — warmth without unprofessionalism.
- **Short articles over long** — split anything requiring excessive context.
- **Breadcrumbs + in-page anchor nav** on every page.

### Intercom (gold standard for enterprise SaaS help)
- **"Step 1: [Title]" as H2** — not just `1.` bullet. Makes steps scannable in page outline.
- **FAQ from user fear:** "What if the customer leaves frustrated?" — not "FAQ about Feature X".
- **Collection card shows author and article count** — social proof for depth.
- **Fin AI Agent is its own category (128 articles)** — AI features need their own home, not scattered.

### Linear (clean, minimal help docs)
- **Card-based homepage** — every link has a 5-10 word verb-led description.
- **"Popular" section first** — respects that most users want 4 things, not 18 categories.
- **Role-based forks inside one article** — admin setup vs. new member setup in the same page, not duplicated.
- **Interactive demo** with "changes reset on refresh" — reduces friction to try first.

### Algolia (excellent SDK and quickstart docs)
- **"Before you begin" / "What's next" as structural bookends** on every guide.
- **Accordion for optional steps** — advanced configurations don't clutter the main flow.
- **Demo cards:** "View the demo", "Explore source code" at the end of every quickstart.
- **All 11 SDK languages in tabs** within one code block.

### Segment (closest CDP comparison)
- **Journey-based navigation:** Getting Started → Connections → Protocols → Personas.
  Mirrors the actual implementation journey.
- **"Source" and "Destination" framing** — readers immediately understand data flow direction.
- **Every integration page:** Overview → Quick Start → Settings Reference → Troubleshooting.

---

## 10. PRIORITISED DOC LIST

P1 = write now. P2 = write next. P3 = write later.
Status: ❌ Missing | ⚠️ Needs Update | ✅ Exists

### CONTEXT

**Data Hub**
| Title | Type | Status | Notes |
|---|---|---|---|
| Data Hub Overview | Conceptual | ❌ | Anchor doc — what it does, why it matters |
| Connecting Your First Data Source | Tutorial | ❌ | Zero to first connected source |
| Customer Profile Schema Reference | Reference | ❌ | All profile attributes per source |
| Data Hub API Reference | API | ❌ | Programmatic profile reads/writes |
| Data Model | Conceptual | ✅ | Review for accuracy |
| Identity Resolution & Profile Unification | Conceptual | ✅ | Review for current SDK |
| Complete Guide to Event Tracking | How-to | ✅ | Review for current SDK coverage |

**Integrations**
| Title | Type | Status | Notes |
|---|---|---|---|
| Integrations Overview | Conceptual | ❌ | Map of all integrations by category |
| Server-Side API Integration | How-to | ❌ | Node / Python / Ruby server events |
| Integration Catalog | Reference | ❌ | All integrations with setup notes. Living doc. |
| JS SDK Reference | Reference | ✅ | Expand to full method reference (all 14 methods) |
| iOS SDK Reference | Reference | ✅ | Review for completeness |
| Android SDK Reference | Reference | ✅ | Review for completeness |

**Consent**
| Title | Type | Status | Notes |
|---|---|---|---|
| Consent Management Overview | Conceptual | ❌ | What Intempt tracks, how consent is honored |
| Cookie Consent Banner Setup | How-to | ❌ | For non-technical marketers |
| GDPR & CCPA Compliance Guide | How-to | ⚠️ | Full rewrite for current product |

### INTELLIGENCE

**AI Segmentation**
| Title | Type | Status | Notes |
|---|---|---|---|
| Behavioral Segmentation Guide | How-to | ❌ | Event-based and sequence-based segments |
| RFM Scoring & Scored Audiences | How-to | ❌ | Recency / Frequency / Monetary |
| Predictive Segments with AI | How-to | ❌ | Churn prediction + LTV audiences |

**Analytics**
| Title | Type | Status | Notes |
|---|---|---|---|
| Building Your First Dashboard | How-to | ❌ | 3 starter templates walkthrough |
| Funnel Analysis Guide | How-to | ❌ | Define steps, measure drop-off |
| Retention & Cohort Analysis | How-to | ❌ | Cohort curves and interpretation |

**Recommend**
| Title | Type | Status | Notes |
|---|---|---|---|
| Recommendation Algorithms Reference | Reference | ❌ | All strategies with plain-English explanation |
| A/B Testing Your Recommendations | How-to | ❌ | Experiments on recommendation placements |

### STUDIO (entire pillar missing — write in this order)

| # | Title | Type |
|---|---|---|
| 1 | Studio Overview | Conceptual |
| 2 | Blu Brand Assistant Overview | Conceptual |
| 3 | Setting Up Company Knowledge in Blu | How-to |
| 4 | Brand Kit Overview | Conceptual |
| 5 | Setting Up Brand Colors & Fonts | How-to |
| 6 | Design Systems Overview | Conceptual |
| 7 | Setting Up a Design System | How-to |
| 8 | Avatars Overview | Conceptual |
| 9 | Managing Avatar Emotions | How-to |
| 10 | Scenes Library Overview | Reference |
| 11 | Remixing a Scene | How-to |
| 12 | Video Editor Overview | Conceptual |
| 13 | Shot-Based Video Editing | How-to |
| 14 | Video Recipes Reference | How-to |
| 15 | Video LLM & Credits Reference | Reference |
| 16 | Asset Library Overview | Conceptual |
| 17 | Asset Draft/Publish System | Conceptual |
| 18 | Recipes Overview | Conceptual |
| 19 | Using Featured Recipes | How-to |
| 20 | Landing Page Builder Tutorial | Tutorial |

### EXECUTION

**Workflows (entire module missing)**
| # | Title | Type |
|---|---|---|
| 1 | Workflows Overview | Conceptual |
| 2 | Building Your First Workflow | Tutorial |
| 3 | Workflow Triggers Reference | Reference |
| 4 | Record Steps Reference | Reference |
| 5 | AI Steps in Workflows | Reference |
| 6 | Flow Control Steps Reference | Reference |
| 7 | Integration Steps Reference | Reference |

**Pipeline (entire module missing)**
| Title | Type |
|---|---|
| Pipeline Overview | Conceptual |
| Setting Up Your Pipeline | Tutorial |
| CRM Integration (Salesforce & HubSpot) | How-to |

**Meetings (entire module missing)**
| Title | Type |
|---|---|
| Meetings Overview | Conceptual |
| Setting Up Call Recording | How-to |
| Live Meeting Assist | How-to |

**Scheduling (entire module missing)**
| Title | Type |
|---|---|
| Scheduling Overview | Conceptual |
| Setting Up Your Booking Page | Tutorial |
| Calendar Integration (Google & Outlook) | How-to |

**Journeys**
| Title | Type | Status |
|---|---|---|
| Journey Triggers & Entry Conditions | Reference | ❌ |
| Multi-Channel Journey Setup | How-to | ❌ |

### PLATFORM

| Title | Type | Status | Notes |
|---|---|---|---|
| API Overview & Authentication | Reference | ❌ | Base URL / auth / rate limits |
| REST API Full Reference | API | ❌ | All 4 endpoints, OpenAPI style |
| User Roles & Permissions | Reference | ❌ | All roles + what each can do |
| What's New / Release Notes | Reference | ❌ | Running changelog |
| Security & Trust Center | Reference | ❌ | SOC2, GDPR, encryption |
| Mobile App Overview | Conceptual | ❌ | "The web app in your pocket" |
| Installing the Mobile App (iOS) | Tutorial | ❌ | — |
| Installing the Mobile App (Android) | Tutorial | ❌ | — |
| Desktop App Overview | Conceptual | ❌ | Live meeting assist, AI coaching |
| Installing the Desktop App (macOS) | Tutorial | ❌ | — |
| Installing the Desktop App (Windows) | Tutorial | ❌ | — |
| Desktop Sales Workflow Guide | Tutorial | ❌ | Prep → call → log → follow-up |

---

## 11. INTEMPT GLOSSARY

| Term | Definition |
|---|---|
| **Profile** | A unified customer record. Merges anonymous and identified events across all sources and sessions. Created automatically when the first event arrives for a new visitor. |
| **Event** | A behavioral signal sent when a user does something — page view, purchase, click, signup. Sent via `intempt.record()`, `intempt.track()`, or autocapture. |
| **Attribute** | A property on a profile or account. Can be a user attribute (name, plan) or computed (last seen, LTV, RFM score). |
| **Segment** | A dynamic audience defined by behavioral rules, RFM scoring, or AI prediction. Updates in real time as behavior changes. Not a snapshot. |
| **Journey** | An omnichannel automation that sends messages or triggers actions based on customer behavior. Lives in the Execution pillar. |
| **Workflow** | A GTM/RevOps automation engine. Broader than Journey — handles CRM records, AI tasks, and integrations, not just messaging. |
| **Experiment** | An A/B or multivariate test on web or mobile. Measures impact on a defined goal metric using statistical significance. |
| **Personalization** | Content variation shown to specific segments based on rules or AI decision-making. Not a test — a permanent delivery. |
| **Recipe** | A reusable automation template. Two types: marketing automation (builds full campaign infrastructure via Blu) or creative (builds visual assets). |
| **Blu** | Intempt's AI brand assistant. Lives inside Studio. Uses company knowledge, ICP, and brand identity to generate on-brand output. |
| **Design System** | An org-level lock on fonts, colors, and layouts. Applied automatically to Studio outputs. |
| **Data Hub** | The Context pillar's core module. Behavioral CDP layer that all Intempt features read from. |
| **Growth OS** | Intempt's framing for itself: a unified system for marketing, sales, and product analytics. |
| **Pipeline** | Intempt's CRM module for deal and account management with Salesforce/HubSpot sync. |
| **Meetings** | Intempt's call recording, AI summary, and live meeting assist product. Available on the Desktop app. |
| **Autocapture** | Automatic tracking of browser interactions (clicks, scrolls, page views) without custom code. Does not block HTML rendering. Provides context. |
| `intempt.record()` | SDK method for structured business events with user/account attributes. More flexible than `track()`. |
| `intempt.track()` | SDK method for simple user events requiring `eventTitle` + `data`. |
| `intempt.identify()` | SDK method for linking anonymous sessions to a known user identity. |
| `intempt.alias()` | SDK method for merging two different user IDs into one profile. One-time operation. |
| **Charge event** | The canonical revenue event in Intempt. Both payments and refunds use `Charge` (not `Purchase`). Must be sent from backend only. |
| **Webhook** | An outbound HTTP request sent when a user reaches a Journey block. Journey-driven, not event-driven. |
| **Choose API** | Server-side experiment resolution endpoint. Call before rendering to get the correct variant for the current user. |
| **Source** | A connection point between a data origin (web app, iOS app, backend server, CRM) and Intempt. Each source has its own API key and event stream. |
| **Asset Library** | Central store for all Studio-generated content. Draft/publish system with CDN propagation. |

---

## 12. QUALITY CHECKLIST

Before marking any article done:

- [ ] One-sentence description at the very top (for help center search)
- [ ] Article type is correct (Conceptual / Tutorial / How-to / Reference / API)
- [ ] Opens with "what it IS" — not "in this article" or a numbered step
- [ ] Uses the correct structural pattern from Section 4
- [ ] Steps labeled "## Step 1: [Title]" not just `1.`
- [ ] Security callout present on any page showing API keys or credentials
- [ ] Limitations/plan restrictions stated before steps, not buried
- [ ] All Intempt terms defined on first use and linked to Glossary
- [ ] No marketing language ("powerful", "seamless", "robust")
- [ ] No filler words ("simply", "just", "easily")
- [ ] Code examples are complete, realistic (no foo/bar), and tested
- [ ] Language selector present on all integration code examples
- [ ] Screenshots match current UI
- [ ] All links resolve
- [ ] Callouts used correctly (Note / Tip / Warning / Security / Prerequisite)
- [ ] FAQ section at bottom (3-5 questions written from user fear)
- [ ] "Up next:" link at the end (tutorials and how-tos only)
- [ ] Next Steps or See Also section with 2–3 links

---

## 13. WORKED EXAMPLE — WHAT GOOD OUTPUT LOOKS LIKE

This is a complete, production-ready article written to the exact standards in this skill.
Use it as a calibration reference. When reviewing or writing articles, compare output
against this example for structure, depth, tone, and completeness.

---

**Article:** Connecting Your First Data Source
**Type:** Tutorial
**Pillar:** Context
**Module:** Data Hub
**Priority:** P1
**Surface:** docs.intempt.com

---

# Connecting Your First Data Source

A data source is the connection between your app and Intempt. Once a source is connected, Intempt starts capturing behavioral events automatically — page views, clicks, and sessions — without any custom code. This tutorial takes you from a new project to validated live data in four steps.

> **Integration effort:** 2/5

**Before you begin:**
- You have an Intempt account and at least one project created
- You have edit access to your app's codebase
- You are working in a development or staging project (recommended before touching production)

## Step 1: Create a Source

1. Go to **Integrations** in the left sidebar.
2. Click **Sources**, then **Create Source**.
3. Select your platform. For a web app, choose **JavaScript**.
4. Name the source. Use a name that reflects the environment: `Production Web`, `Staging Web`.
5. Click **Create**.

Intempt generates a unique `sourceId` and `writeKey` for this source. Keep this page open. You will need both values in the next step.

> **Security:** Your `writeKey` authenticates all data sent from this source. Never commit it to a public repository. Use environment variables.

## Step 2: Install the JavaScript Snippet

Paste the following snippet inside the `<head>` tag of every page in your app. Replace the placeholder values with the credentials from Step 1.

```html
<script>
  (function(a,b,c){var d=a.head||a.getElementsByTagName("head")[0];
  var e=a.createElement("script");e.async=1;e.src=b;e.onload=c;
  d.appendChild(e)})(document,"https://cdn.intempt.com/intempt.js",function(){
    window.intempt = new IntemptJs({
      organization: "acme-corp",
      sourceId: "1001",
      project: "production",
      writeKey: "pk_live_abc123.sk_live_xyz789"
    })
  })
</script>
```

| Placeholder | Where to find it |
|---|---|
| `organization` | Settings → Organization → Slug |
| `sourceId` | Shown on the source detail page after creation |
| `project` | Settings → Projects → Project Name |
| `writeKey` | Shown on the source detail page after creation |

**If you use npm:**

```bash
npm install @intempt/js-sdk
```

```javascript
import IntemptJs from "@intempt/js-sdk"

const intempt = new IntemptJs({
  organization: "acme-corp",
  sourceId: "1001",
  project: "production",
  writeKey: process.env.INTEMPT_WRITE_KEY
})
```

## Step 3: Identify Your Users

Autocapture fires immediately after the snippet loads, but events are anonymous until you call `intempt.identify()`. This links a session to a known user profile.

Call `identify()` immediately after a successful login:

```javascript
// Call this once, right after the user authenticates
intempt.identify({
  userId: "user@example.com",
  userAttributes: {
    name: "Sarah Chen",
    plan: "trial",
    signupDate: "2025-05-28"
  }
})
```

`userId` must be a stable, unique identifier. An email address or internal database ID both work. Do not use session IDs or temporary tokens.

> **Note:** If your app is server-rendered (Next.js, Rails, Django), call `intempt.identify()` on every page load, not only on the login page. The SDK does not persist identity across full page loads automatically.

## Step 4: Validate in Live Data Feed

1. Open your app in a browser.
2. Navigate to several pages and click a few elements.
3. In Intempt, go to **Data Hub** → **Live Data Feed**.
4. Events should appear within seconds: `view page`, `click on`, `intempt:session`.

If you completed Step 3, you will also see an `identify` event tied to the `userId` you passed.

> **Tip:** Open Live Data Feed before loading your app, so you can watch events arrive in real time as you test.

## Frequently asked questions

**Nothing is showing up in Live Data Feed. What is wrong?**
Open your browser console and look for JavaScript errors. The most common causes: wrong `sourceId` or `writeKey` values, the snippet placed in `<body>` instead of `<head>`, or a browser extension blocking the CDN request. Try in an incognito window with extensions disabled.

**Can I connect more than one source?**
Yes. Create a separate source for each environment (development, staging, production) and each platform (web, iOS, Android, backend). Each source gets its own credentials and its own event stream. This keeps test data out of production analytics.

**Does the snippet slow down my site?**
No. The snippet loads asynchronously and does not block page rendering. Autocapture uses passive event listeners with no impact on scroll or input performance.

**What does autocapture collect without any custom code?**
Page views (`view page`), page exits (`leave page`), element clicks (`click on`), form submissions (`submit on`), and session start and end (`intempt:session`). Custom business events such as purchases, signups, and feature usage require `intempt.track()` or `intempt.record()`.

**I have a single-page app (React, Vue, Angular). Do I need anything extra?**
Yes. SPAs do not trigger full page loads on route changes, so `view page` events do not fire automatically. Call `intempt.track({ eventTitle: 'view page', data: { url: window.location.href } })` on every route change. See [Tracking Events in Single-Page Apps] for the full setup.

---

**Up next:** [Track Your First Custom Event →]

---

## 14. HANDOFF TEMPLATE FOR COMMISSIONING ARTICLES

When handing off an article to a writer, include:

```
Article title: [exact title from Section 10]
Doc type: [Conceptual / Tutorial / How-to / Reference / API]
Structural pattern: [section number from Section 4]
Article title formula: [from Section 5]
Pillar: [Context / Intelligence / Studio / Execution / Platform]
Module: [Data Hub / Journeys / Workflows / etc.]
Role: [Product / Technical Writer / Engineering / Customer Success]
Priority: [P1 / P2 / P3]
Target surface: [docs.intempt.com = developer | help.intempt.com = help]

Product context (copy from Section 2):
[Paste the relevant module block]

SDK methods or API endpoints referenced:
[List from Section 2 and Section 11 glossary]

Glossary terms to define on first use:
[List from Section 11]

Existing related articles to link from:
[URLs]

Writing rules to flag:
- Open with what the feature IS (Section 6)
- Step 1: labeled H2 format
- Security callout if any credentials shown
- FAQ from user fear at bottom
- "Up next:" link at end
```
