---
name: cv-job-matcher
description: >
  Evaluates the suitability of a job description against a candidate's CV.
  Use this skill whenever a user shares a job description (as text, a URL, or a pasted listing)
  and wants to know how well it matches their CV or work experience — even if they don't use
  the words "match" or "fit". Trigger phrases include: "what do you think of this role",
  "is this a good fit", "evaluate this role", "should I apply", "what's your take on this job",
  or any message where a JD or job link is shared alongside CV context.
  Also triggers when the user pastes a job description mid-conversation where a CV is already in context.
  Always run this skill before writing a cover letter for a new role — the fit assessment
  informs the cover letter framing.
---

# CV / Job Matcher Skill

Produces an honest, structured suitability assessment of a job description against a candidate's CV.
The goal is to give the candidate a clear-eyed view of fit — not to encourage or discourage
application, but to surface real strengths, real gaps, and practical concerns so they can
make an informed decision.

---

## Inputs Required

- **CV**: Either already in conversation context, or ask the user to paste/upload it
- **JD**: Either pasted as text, or a URL to fetch. If a URL fails to load (LinkedIn, robots.txt),
  ask the user to paste the JD text directly — don't guess at content
- **Candidate preferences**: If not already known from the conversation, ask upfront:
  - Preferred work location(s) and maximum acceptable commute / remote preference
  - Salary or day rate expectations (permanent salary range or contract day rate)
  - Inside vs outside IR35 preference for contract roles (UK candidates)

  Ask these once per conversation — if already established earlier, don't ask again.
  Store and reference them across all subsequent role assessments in the session.

---

## Assessment Process

### Step 1 — Structural Check (run first, before skills matching)

Before comparing skills, flag any structural mismatches that could screen the candidate out
regardless of experience:

- **Role type mismatch** — e.g. applying for a BA role when they're a PM, engineering role
  when they're non-technical, project manager when they're a product manager
- **Seniority mismatch** — significantly over or underqualified
- **Domain must-haves** — when the JD explicitly requires domain experience the candidate
  lacks (e.g. "must have Commercial Pharma experience", "strong checkout ecommerce background required")
- **Title mismatch** — when the candidate's titles don't map to the role's expectations
  (note this separately from a skills gap — it's a screening risk, not a capability gap)

If structural issues exist, lead with them before the fit table. Don't bury them.

### Step 2 — Fit Table

Produce a markdown table with four columns:

| Requirement | Candidate's Evidence | Fit |
|---|---|---|

**Requirement**: Pull directly from the JD — use the JD's language, not paraphrased versions.
Focus on must-haves first, nice-to-haves second.

**Evidence**: One concise sentence referencing specific roles, projects, or metrics from the CV.
If there is no evidence, say so plainly — don't stretch weak signals into strong ones.

**Fit**: One of three values only:
- ✅ Strong — direct, credible evidence in the CV
- ⚠️ Partial — adjacent experience that partially covers the requirement
- ❌ Gap — no credible evidence; requirement is unmet

### Step 3 — Compensation & Location Analysis

#### Location fit
Compare the role's location and working pattern against the candidate's stated preference:
- Flag clearly if onsite requirements exceed the candidate's stated comfort (e.g. candidate wants remote-first, role requires 3 days onsite)
- Note commute implications if the office location is known and the candidate is London-based
- Flag if the role is outside the candidate's preferred geography entirely

#### Compensation analysis
This is a required step for every role — not optional.

**If the JD states a salary or day rate:**
- Compare directly against the candidate's stated expectations
- Flag if it meets, falls short of, or exceeds expectations
- For contract roles, always note PAYE vs outside IR35 distinction and the take-home impact:
  - Outside IR35: candidate keeps the full day rate (minus personal tax)
  - Inside IR35: effective take-home is roughly 55–65% of the stated rate after tax and NI
  - PAYE: similar take-home to inside IR35; clarify if umbrella or direct employment

**If the JD does not state compensation:**
- Use the web_search tool to find market rate data for this role, seniority level,
  location, and sector. Search for terms like:
  `"[role title] salary [city] [year]"` or `"[role title] day rate UK contract [year]"`
- Use sources like Glassdoor, LinkedIn Salary, Reed, CWJobs, ITJobsWatch (for tech contracts),
  or recent recruiter salary surveys
- Present a rough market range clearly labelled as an estimate:
  > "No salary listed. Based on market data for [role] in [location], typical ranges are:
  > Permanent: £X–£Y | Contract: £X–£Y per day (outside IR35)"
- Then assess whether this estimated range is likely to meet the candidate's expectations

**Always conclude compensation analysis with one of:**
- ✅ Likely meets expectations
- ⚠️ May fall short — worth clarifying before investing time in application
- ❌ Unlikely to meet expectations — flag as a dealbreaker if candidate has confirmed hard floor

#### Other practical concerns
- **Contract type** — inside vs outside IR35 implications for UK contractors
- **Employment type mismatch** — permanent role when candidate is actively contracting, or vice versa
- **Start date** — flag if an immediate start is required and candidate has a notice period

### Step 4 — Overall Verdict

Provide:
- **Score**: X/10 — be honest. Don't inflate to be encouraging.
- **2–3 sentence plain English verdict** — what's the strongest case for applying,
  and what's the realistic risk
- **Recommendation**: One of:
  - *Strong fit — prioritise this application*
  - *Good fit — worth applying*
  - *Partial fit — apply with targeted framing*
  - *Weak fit — manage expectations*
  - *Skip — structural gap is too significant*

---

## Tone & Honesty Standards

- **Be direct about gaps.** If a must-have requirement is missing, say so clearly.
  Don't soften it with "however, your transferable experience..." unless that's genuinely true.
- **Don't overclaim on partial matches.** "Adjacent experience" is not the same as
  "direct experience." Mark it partial, not strong.
- **Flag domain gaps explicitly.** If a role requires specific domain knowledge
  (pharma, FMCG, ecommerce checkout, etc.) that the candidate doesn't have,
  name it as a gap even if their general skills are strong.
- **Consider the competition.** For a strong fit, note if the candidate is competitive.
  For a weak fit, note that other candidates will likely have the missing credentials.
- **AI literacy caveat**: If a role lists AI as a requirement and the candidate's AI
  experience is limited to general tool usage (ChatGPT, Copilot etc.), flag this as
  a weak signal — it's table stakes in 2025/2026, not a differentiator.

---

## Output Format

```
## [Role Title] — [Company if known]

### 🔴 Structural Issues (if any)
[Flag role type, domain must-haves, or title mismatches here]

### Fit Breakdown
[Fit table]

### Compensation & Location
**Location:** [meets / partially meets / does not meet candidate preference — one line]
**Compensation:** [stated rate or estimated market range] — [✅ / ⚠️ / ❌ vs candidate expectations]
[1–2 sentences of context if needed — e.g. IR35 impact, PAYE take-home note]

### Overall Verdict
**Score: X/10**
[2–3 sentence verdict]
**Recommendation: [one of the five options above]**
```

If no structural issues exist, omit that section entirely — don't include it with "none".

---

## After the Assessment

Once the fit assessment is complete, offer one of:
- Write a cover letter (if fit is 6/10 or above)
- Suggest CV tweaks to improve fit for this specific role
- Compare against other roles already assessed in the conversation

If fit is below 5/10, be honest that a cover letter is unlikely to bridge the gap,
and suggest focusing effort on better-matched roles instead.

---

## Multi-Role Context

If multiple roles have been assessed in the same conversation, maintain a running
priority ranking and reference it when a new role is assessed. Example:

> "This puts it behind the Wolt role (8/10) but ahead of the Hampshire contract (7.5/10)
> in terms of overall fit."

This helps the candidate prioritise application effort.

---

## Edge Cases

**URL fails to load** (LinkedIn, robots.txt blocked):
> "I can't fetch that URL directly — LinkedIn blocks automated access. Could you paste
> the job description text here and I'll assess it straight away."

**JD is very short / vague** (e.g. a recruiter post with 3 bullet points):
Assess what's available, but note the limited information and suggest the candidate
ask the recruiter for the full JD before applying.

**Role is clearly wrong type** (engineering, legal, finance etc. for a PM candidate):
State this immediately and concisely — don't produce a full fit table for a role
that is categorically unsuitable. Save the candidate's time.

**Candidate asks "can I use the same cover letter for this role?"**:
Check if a cover letter was already written in the conversation. If yes, assess
whether the fit and framing are compatible. If not, write a new one or suggest
specific tweaks.
