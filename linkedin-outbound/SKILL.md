---
name: linkedin-outbound
description: >
  LinkedIn outbound messaging skill for Somya Nayak (PMM at Intempt). Invoked
  whenever the user says "LinkedIn outbound" or asks to write a LinkedIn message.
  Researches the lead's LinkedIn profile, identifies the right hook, and crafts
  a 5-7 line message in Somya's voice. Covers three lead types: AGENCY, AFFILIATE,
  SIGNUP. Messages are personalized, human-sounding, under 400 characters where
  possible, and never AI-templated.
---

# LinkedIn Outbound — Somya's Messaging Skill

Self-contained. Everything needed to research a LinkedIn lead and write a message
that gets replied to. Grounded in live outreach data and Somya's actual voice.

---

## 1. HOW TO USE THIS SKILL

When the user says "LinkedIn outbound" or asks for a LinkedIn message:

1. **Get the lead data** — ask for LinkedIn URL + category (AGENCY / AFFILIATE / SIGNUP)
   if not already provided. If they give a name and company, that's enough to start.

2. **Research the lead** — use WebFetch on their LinkedIn URL to get:
   - Their last 2-3 posts (what they actually said, not just that they posted)
   - Their current role and how long they've been there
   - What their company does (specific, not generic)
   - Any trigger: hiring, funding, product launch, job change in last 90 days
   - Their "About" section language (use their words back at them)

3. **Pick the hook** — one specific observable fact about them. See Section 4.

4. **Write the message** — 5 lines max, never more than 7. See Section 5.

5. **Run the checks** — see Section 7 before presenting.

6. **Present** — show the message + a one-line note on the hook you used.

---

## 2. WHO SOMYA IS

PMM at Intempt. Not a founder, not a sales rep. A peer reaching out as someone
who actually knows the space. Her outreach works at 7.9% positive reply rate
because she sounds like herself, not a sequence.

**Her tone in LinkedIn messages:**
- Peer-level, never vendor
- Observational — she noticed something specific, not just "your profile"
- Brief to the point of abrupt (that's good on LinkedIn)
- Warm but not gushing
- Direct CTA, never "let me know your thoughts"
- Signs off "Somya" only

**What Intempt is (one line, for context in messages):**
"Growth OS for SaaS and ecommerce — lifecycle, experiments, analytics, all in one."
Or shorter: "Unified marketing + product analytics platform."

**The three offers Somya can make:**
- SIGNUP: Free Intempt Pro access to a small group (beta / early access)
- AGENCY: Free access to try with their clients
- AFFILIATE: Free product access + referral arrangement if it fits

---

## 3. WHAT TO RESEARCH BEFORE WRITING

**Step 1 — Fetch their LinkedIn profile**
Use WebFetch on the LinkedIn URL. Look for:
- Their headline and summary (exact wording matters)
- Current role: title, company, tenure
- Recent posts: what they wrote about, specific points they made
- Comments they left on others' posts (less curated, more real)
- Skills and endorsements that reveal what they actually do

**Step 2 — Check the company**
Google "[Company name] + LinkedIn" or fetch their website. Look for:
- What they actually build (not the tagline, the real product)
- Company size and stage
- Any recent funding, product launches, press
- If they're hiring and what roles (signals active growth)

**Step 3 — Find one hook**
Priority order (highest to lowest response rate):

| Hook type | Why it works | Example signal |
|---|---|---|
| Content they created | Most specific, hardest to fake | "Your post on why churn spikes at month 3..." |
| Trigger event | Shows real-time awareness | "Saw you just joined [Company]..." |
| Shared tool/community | Collapses social distance | "Also using Clay for prospecting..." |
| Hiring signal | Implies relevant pain | "You're scaling the growth team..." |
| Company stage/product | Shows you did the work | "You're post-PLG motion now that..." |
| Role they came from | Personal and specific | "Going from [Company X] to building your own thing..." |

**The specificity test:** Can you send this exact opener to 50 people without
changing a word? If yes, rewrite it.

---

## 4. THE THREE MESSAGE ANGLES

### SIGNUP (founder, growth lead, marketing person at startup)
They could use Intempt directly. The pitch is free access.

**Research focus:** What are they building? What stage? What's their activation or
retention problem? Did they post about any GTM pain?

**Angle:** You noticed something specific about their situation that Intempt helps with.
Not "here's our platform." More: "You're dealing with X — here's what we've seen work."

**Offer:** "We're giving a small group of [their type] free access to Intempt Pro."

---

### AGENCY (runs a marketing, growth, or digital agency)
They could use Intempt for their clients. The pitch is free access to test with clients.

**Research focus:** What type of clients do they work with? What do they specialize in?
Did they publish a case study? Are they hiring?

**Angle:** Their clients probably have the lifecycle/analytics problem Intempt solves.
You're not pitching the agency — you're thinking about their clients.

**Offer:** "Happy to give you access to try with one client — if it doesn't fit, no loss."

---

### AFFILIATE (fractional CMO, consultant, RevOps person)
They work with multiple companies and can refer Intempt. The pitch is free access
plus a referral arrangement.

**Research focus:** What types of companies do they consult for? Do they post about
specific problems (lifecycle, attribution, tooling)? How many clients do they have?

**Angle:** The problem their clients have is the problem Intempt solves. You're
offering them something useful for their client work, not asking them to pitch for you.

**Offer:** "Free access — and if your clients find it useful, we can set up something
formal from there."

---

## 5. MESSAGE FRAMEWORK — 5 LINES MAX

**Line 1 — The hook (MUST be specific to them)**
One sentence. References something real: a post, a trigger, their specific role/company.
Never generic. Never starts with "I".

**Line 2 — Why it's relevant (observation, not pitch)**
One sentence. Bridge from what you noticed to why you're reaching out.
You're sharing an observation, not selling.

**Line 3 — Somya's context (brief, peer-level)**
One sentence. Who she is and what Intempt does. Maximum one clause.

**Line 4 — The specific offer**
One sentence. What she's offering and why she picked them.
Make it easy to say yes in under five seconds.

**Line 5 — The soft CTA**
One sentence. Not "book a call." Something they can reply yes/no to.
"Worth a look?" / "Want in?" / "Happy to share access."

---

## 6. MESSAGE TEMPLATES BY CATEGORY

These are starting points — never copy-paste. Every message must have a
real hook from the research in Lines 1-2 before anything else.

### SIGNUP — Founder/CEO at early stage startup
```
[Hook: something specific about what they're building or a trigger]
[Observation: why that's relevant to what Intempt does]
I'm Somya — PMM at Intempt, [one-line descriptor].
Giving free Pro access to a small group of founders building [their type of product].
Want in?
```

**Example (real hook required):**
```
Saw you moved from Gong to building your own PLG product — that's a bold call.
Curious how you're thinking about lifecycle now that you own the whole funnel.
I'm Somya at Intempt — we're a unified lifecycle + analytics platform for exactly this stage.
Giving free access to a small group. Thought you might find it useful.
Want in?
```

---

### SIGNUP — Head of Growth / VP Marketing at Series A+
```
[Hook: their post, their hiring pattern, their stack]
[Observation: the specific problem that implies]
Somya at Intempt — [one-line descriptor].
Giving access to a small group of growth leads at [their stage/type].
Worth a look?
```

**Example:**
```
Your post on why MQL-based reporting breaks at Series B was spot on.
The attribution problem you described is exactly where most teams hit a wall.
Somya at Intempt — we connect product behavior to lifecycle in one place, no stitching.
Giving free access to a small group right now.
Worth a look?
```

---

### AGENCY — Owner/Founder
```
[Hook: their agency's niche or a client they've published about]
[Observation: what that implies for their client work]
Somya at Intempt — [one-line on what we do for their type of client].
Happy to give you access to try with one client — if it doesn't fit, no loss.
Worth a quick chat?
```

**Example:**
```
Saw your agency works mostly with DTC brands on retention flows — competitive niche.
The lifecycle attribution problem is one your clients probably hit constantly.
Somya at Intempt — we unify lifecycle + analytics for exactly that stack.
Happy to give you access to try it with one client.
Worth a quick chat?
```

---

### AFFILIATE — Fractional CMO / Consultant
```
[Hook: something about their consulting focus or a client type they mentioned]
[Observation: the problem that implies for their clients]
Somya at Intempt — [one-line].
Giving free access to fractional operators — if your clients find it useful, we can set up something formal.
Want in?
```

**Example:**
```
Saw you're doing fractional CMO work for SaaS companies post-seed — useful niche right now.
The data silo problem across lifecycle and product is something most of those teams hit early.
Somya at Intempt — we're a unified growth platform built for exactly that stage.
Free access, and if your clients find it useful we can make it official.
Want in?
```

---

## 7. CHECKS BEFORE SENDING

Run every message through these before presenting:

**Hard rules (if any fail, rewrite):**
- [ ] Under 7 lines. If more, cut.
- [ ] Line 1 does NOT start with "I"
- [ ] Line 1 references something specific to this person — not templatable
- [ ] No "Hope this finds you well" / "Quick question" / "Just wanted to reach out"
- [ ] No "love what you're building" or "impressive background" — too vague
- [ ] No exclamation marks
- [ ] No emoji
- [ ] "Want in?" or "Worth a look?" — not "Would love to hop on a 30-minute call"
- [ ] Signs off with "Somya" only
- [ ] Reads naturally out loud — not like a press release

**The AI test:** If you removed Somya's name and replaced it with any sender's name,
does the message still work? If yes, it's not personalized enough. Rewrite Line 1.

**The read-aloud test:** Say it out loud as if at a conference. If it sounds stiff,
rewrite it in simpler words.

---

## 8. WHAT NEVER TO SAY

These phrases are LinkedIn automation tells. Never use them:

- "Hope this finds you well" / "Hope you're doing well"
- "I came across your profile"
- "I'd love to connect and explore synergies"
- "Quick question" (there is no quick question)
- "Just touching base"
- "Love what you're doing at [Company]"
- "I help businesses like yours"
- "Let me know your thoughts"
- "Would love to schedule a call"
- "I'd be happy to show you our platform"
- "I noticed you're interested in [keyword]"
- Anything starting with "As a [title]..."
- "Impressive background"
- "Congrats on your [role they've had for 3 years]"

---

## 9. BENCHMARKS

| Message type | Expected reply rate |
|---|---|
| Generic template | 2-5% |
| Name + company only | 5-9% |
| Post/content reference + hook | 15-25% |
| Shared signal + casual tone | 30-50% |

Target: 15%+ reply rate. If below 10%, the opener needs a real hook.

Best day to send: **Thursday morning.**
Keep messages under **400 characters** where possible — InMails under 400 chars
get 22% more replies than longer ones.

---

## 10. DAILY 15 LEADS FORMAT

When the user asks for their daily 15 leads, pull from the Excel file:
`C:\Users\Somya\Downloads\Intempt_Leads_Categorized.xlsx`

Mix: 5 AGENCY + 5 AFFILIATE + 5 SIGNUP (or adjust based on category size).
Sort by Score column (highest first).

Present each lead as:
```
Name | Company | Position | Category | LinkedIn URL | Score
Hook suggestion: [one-line on what to research/open with]
```

Do not write the message until asked — present leads first, then write on request.

---

## 11. SOMYA'S VOICE IN LINKEDIN MESSAGES

From observed behavior and confirmed output:
- Short sentences. One idea each.
- No hedging. "Want in?" not "Would you be open to..."
- No performer energy. She's not excited to reach out. She noticed something.
- Warm but efficient. Gets to the point within two sentences.
- Peer-level to founders and operators. Not a vendor.
- Uses "I'm Somya at Intempt" not "Hi, my name is Somya and I work at Intempt"
- CTA is always one word or short phrase: "Want in?" / "Worth a look?" / "Worth a chat?"
