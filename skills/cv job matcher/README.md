# cv-job-matcher

A Claude skill that evaluates how well a job description matches a candidate's CV — giving an honest, structured suitability assessment with compensation analysis and location fit.

---

## What it does

For every role assessed, the skill produces:

- **Structural check** — flags role type mismatches, domain must-haves, and title gaps before wasting time on a skills comparison
- **Fit breakdown table** — maps each JD requirement to CV evidence, rated Strong / Partial / Gap
- **Compensation analysis** — compares stated salary/rate against candidate expectations; if no salary is listed, searches for market rate data and assesses against expectations
- **Location fit** — flags when onsite requirements clash with the candidate's stated preferences
- **Overall verdict** — honest score out of 10 and a clear recommendation

---

## How to install

1. Download `cv-job-matcher.skill`
2. Go to **Settings → Skills** in Claude
3. Upload the `.skill` file

---

## How to use

Once installed, share a job description in any Claude conversation alongside your CV and the skill triggers automatically.

**Trigger phrases:**
- "Is this a good fit for me?"
- "Evaluate this role"
- "Should I apply for this?"
- Paste a JD with your CV already in context

On first use in a session, the skill will ask for:
- Your preferred work location and commute tolerance
- Your salary or day rate expectations
- IR35 preference (for UK contract roles)

These are asked **once per conversation** and remembered across all subsequent role assessments in the same session.

---

## Output format

```
## [Role Title] — [Company]

### 🔴 Structural Issues (if any)

### Fit Breakdown
| Requirement | Evidence | Fit |
|---|---|---|

### Compensation & Location
Location: [meets / partially meets / does not meet preference]
Compensation: [stated or estimated range] — [✅ / ⚠️ / ❌ vs expectations]

### Overall Verdict
Score: X/10
Recommendation: [Strong fit / Good fit / Partial fit / Weak fit / Skip]
```

---

## Compensation logic

| Scenario | Behaviour |
|---|---|
| Salary stated in JD | Compares directly against candidate expectations |
| No salary in JD | Web searches market rate for role, seniority, location, and sector |
| UK contract role | Notes PAYE vs outside IR35 take-home difference |
| Inside IR35 | Flags effective take-home (~55–65% of stated rate) |

---

## Honest assessment standards

The skill is designed to give **clear-eyed, honest assessments** — not to encourage applications where the fit is poor:

- Domain gaps are called out explicitly, even when general skills are strong
- "Adjacent experience" is marked Partial, not Strong
- AI literacy claimed only through tool usage is flagged as table stakes, not a differentiator
- Roles scoring below 5/10 receive a Skip recommendation rather than a cover letter offer

---

## After the assessment

For roles scoring 6/10 or above, the skill offers to:
- Write a targeted cover letter
- Suggest CV tweaks for this specific role
- Compare against other roles assessed in the same session

---

## Files

```
cv-job-matcher/
├── SKILL.md        — skill instructions (install this)
└── README.md       — this file
```

---

## Changelog

| Version | Notes |
|---|---|
| 1.0 | Initial skill — fit table, structural check, practical concerns |
| 1.1 | Added candidate preferences (location, salary, IR35), compensation analysis, market rate web search |
