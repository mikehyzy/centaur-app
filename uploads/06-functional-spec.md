# Centaur: Functional Specification
**Version:** v1 (beta)
**Grounded in:** `01-product-brief.md`, `02-success-metrics.md`, `03-market-validation.md`, `05-persona-jtbd.md`
**Deployment target:** Static site, GitHub Pages

---

## Open Questions (resolve before build)

1. **Input model:** Does the user manually paste their AI prompt and response into Centaur, or does Centaur infer from clipboard monitoring, browser extension, or OS-level integration? V1 brief says standalone (no AI platform integration), but the interaction model depends entirely on this answer. Manual paste is high friction and will depress the behavioral compliance assumption (Assumption 2, `05-persona-jtbd.md`). Decision needed before any UI is designed.

2. **Session boundary:** What defines the start and end of a "session"? Is one session one AI prompt-response pair? One work block (30-minute window)? One decision unit (a single question the user is trying to answer)? The classification engine, the ratio computation, and the UI all depend on this definition being consistent.

3. **Classification override:** Can the user override the behavioral inference classification? If the engine says Defer and the user knows it was Direct (they had a full reasoning process before opening the AI), do they get to correct it? Allowing overrides improves data quality but introduces self-report bias. Blocking overrides preserves inference integrity but may frustrate users who feel misclassified.

4. **Granularity of classification:** Is one session one D/D/D event, or can a single session contain multiple events (e.g., user goes Direct on framing, then Defers on the recommendation)? V1 recommendation: one session = one classification, revisited in v2.

5. **Export and backup:** Is session export to JSON in scope for v1 or v2? The local-only privacy principle (`01-product-brief.md`) makes backup critical — a cleared browser cache destroys all history. Minimum: warn users; ideal: export on every session.

---

## Product Scope: What Centaur Does in V1

Centaur is a cognitive instrument, not a productivity tool. It does three things and nothing else:

1. **Logs AI handoff sessions** with behavioral signals that enable classification.
2. **Classifies each session** as Direct, Delegate, or Defer using behavioral inference.
3. **Surfaces the drift pattern** as a 4-week rolling ratio and trend direction.

Everything else — coaching, blocking, team analytics, AI platform integration — is explicitly out of scope for v1 (`01-product-brief.md`).

---

## Core Concepts (shared vocabulary across all spec files)

**Direct:** The user held the thinking. They articulated their own framing or position before engaging with AI, inspected the AI's output critically, and either confirmed their own view or made a deliberate, named departure from it.

**Delegate:** The user handed a bounded outcome to AI and inspected the result. They defined the scope, checked the reasoning, and remained the decision authority. The output quality depended on the quality of their direction.

**Defer:** The user outsourced judgment itself. They accepted an AI output without a prior framing of their own, without challenging the reasoning, or with minimal inspection time. The decision is now the AI's, not theirs.

**Session:** One discrete AI handoff interaction. Defined as: user submits a question or task to AI → user reviews AI output → user accepts, edits, or rejects the output. One session = one classification event.

**Drift indicator:** A computed signal derived from the ratio of Defer-classified sessions over a 4-week rolling window, compared to the user's week 1–2 baseline. A rising Defer ratio relative to baseline is drift. A falling or stable Defer ratio is not drift.

**Forced reasoning step:** The UI gate that requires the user to submit their own framing before the AI output is shown. This is the highest-leverage mechanism in the product (`fbb755ba` Table 2). It is non-negotiable in v1.

---

## Feature List

### F1: Forced Reasoning Gate
The user cannot see AI output in the session flow without first submitting their own framing. This is a hard gate, not a recommendation.

- User is presented with a text input labeled with a single instruction: "What do you think the answer is, or what's your framing of this problem? Write it before you look at the AI's output."
- Minimum: 10 characters to proceed. No word count requirement — a fragment is fine.
- Timer starts when the gate is shown. Time-to-submission is logged as a behavioral signal.
- User submits → AI output field becomes available.

**Behavioral signals logged:**
- `framing_length`: character count of user's framing
- `framing_time_seconds`: time between gate shown and framing submitted
- `framing_submitted`: boolean (always true if they reached AI output — gate enforces this)

### F2: AI Output Entry
The user pastes or types the AI's response into a second field after submitting their framing.

- Text area. No formatting required.
- `ai_output_length`: character count logged.
- `time_to_acceptance_seconds`: time between AI output entry and session submission logged.
- Optional: user can highlight specific sections of AI output to mark as "challenged" — these highlights are logged as challenge gestures.

### F3: Behavioral Inference Classification
After session submission, the product computes a Direct/Delegate/Defer classification from the logged behavioral signals.

**Classification logic (v1 heuristic — to be validated against labeled data in beta):**

| Signal | Direct indicators | Delegate indicators | Defer indicators |
|---|---|---|---|
| `framing_length` | Long (>150 chars), substantive | Medium (50–150 chars), directive | Short (<50 chars), vague |
| `framing_time_seconds` | Long (>120s) — user deliberated | Medium (30–120s) | Short (<30s) — user grabbed AI fast |
| `time_to_acceptance_seconds` | Long (>90s) — user inspected | Medium (30–90s) | Short (<30s) — snap acceptance |
| `edit_distance` (framing vs. AI output) | High — user's view diverged | Medium — user refined AI | Low — user adopted AI wholesale |
| `challenge_gestures` | Present — user highlighted/marked | May be present | Absent |

**Classification rule (v1):** Score each signal on a 1–3 scale (1=Defer signal, 2=Delegate signal, 3=Direct signal). Sum the scores. Score ≤7: Defer. Score 8–11: Delegate. Score ≥12: Direct. This is a heuristic for beta — it will be refined against labeled ground truth from weeks 1–4.

**Display:** Classification is shown immediately after submission as the session's verdict. Label only — no explanation, no score, no coaching.

### F4: Session Log
Every submitted session is stored locally with its classification and behavioral signals.

**Session record (see `07-technical-spec.md` for full schema):**
- Timestamp
- User's framing (text)
- AI output (text)
- Classification (Direct/Delegate/Defer)
- All behavioral signals
- Optional: user's session note (free text, added post-classification)

### F5: Revisit Flow
The user can reopen any past session and record their current view of the decision.

- Revisit action: user opens a past session, reads their framing and the AI output.
- User selects one: **Affirm** (my reasoning still holds), **Refine** (I'd change something), **Reverse** (I now disagree with the decision).
- Revisit action is logged with timestamp. This feeds metric #6 (reasoning quality / reversal rate, `02-success-metrics.md`).

### F6: Ratio Dashboard
The primary view. Shows the user's Direct/Delegate/Defer ratio for the current week and a 4-week rolling trend.

- Current week ratio: three numbers, displayed as percentages (e.g., Direct 30% / Delegate 40% / Defer 30%).
- Trend: 4-week sparkline for each classification type. Arrow indicating direction of change for Defer (up = worsening drift, down = improving).
- Drift indicator: a single computed signal. Displayed only after week 4. Shows whether the user's Defer rate is rising, stable, or falling relative to their week 1–2 baseline. No absolute judgment — only trend direction.
- Session count: total sessions this week, total all-time. Provides context for the ratio.

### F7: Session History
A reverse-chronological list of all logged sessions.

- Each entry shows: date, classification label, framing excerpt (first 80 characters).
- Tap/click to open full session detail.
- Filter by classification type (Direct / Delegate / Defer).
- No search in v1.

### F8: Baseline Capture
During weeks 1–2, the product operates in "baseline mode." The ratio is logged but not displayed. The drift indicator is not computed. The product tells the user: "We're establishing your baseline. Your pattern will be visible after week 2."

- This prevents users from gaming the ratio before a baseline is set.
- It also sets accurate expectations: the product is a 4–8 week instrument, not an instant signal.

---

## User Flows

### Flow 1: First Session (Onboarding)

```
App opens (first time)
  → Welcome screen: one sentence on what Centaur does
  → "What kind of work do you use AI for most?" (PM / Investor / Analyst / Strategist / Other)
     [logged as user context — not used in v1 classification, reserved for v2 cohort analysis]
  → "Start your first session" CTA
  → Session flow begins (Flow 2)
```

**Empty state:** No sessions exist. Dashboard shows: "Your first session will establish your baseline. Complete 3 sessions this week to start your pattern."

### Flow 2: Session Entry (Core Loop)

```
New session initiated
  → Forced Reasoning Gate shown
     [Timer starts]
     User writes framing
     [framing_length, framing_time_seconds logged]
  → User submits framing
  → AI output field unlocked
     User pastes/types AI output
     [time_to_acceptance_seconds starts]
  → Optional: user highlights challenge gestures in AI output
  → User submits session
     [time_to_acceptance_seconds logged, edit_distance computed]
  → Classification computed (2–3 second loading state)
  → Classification displayed: "This session was: [Direct / Delegate / Defer]"
     [No explanation. Just the verdict.]
  → Optional: user adds a session note (free text)
  → Session saved to local storage
  → Return to dashboard
```

**Loading state (classification):** Subtle spinner or skeleton for 2–3 seconds. Label: "Analyzing your session." Not instant — the pause creates the impression of deliberation, which is accurate.

**Error state (save failure):** "Your session couldn't be saved. Copy your framing and try again." Provide a text copy of the framing so the user doesn't lose their reasoning.

### Flow 3: Dashboard View (Returning User)

```
App opens (returning user, weeks 1–2)
  → "Baseline building" banner: "X sessions logged. Keep going — your pattern appears after week 2."
  → Session history visible (most recent first)
  → "New session" CTA prominent

App opens (returning user, week 3+)
  → Ratio dashboard: current week D/D/D percentages
  → 4-week trend sparklines
  → Drift indicator (if week 4+): "Your Defer rate is [rising / stable / falling] vs. your baseline."
  → Session history below
  → "New session" CTA
```

**Empty state (no sessions this week):** "No sessions this week yet. Your trend is computed from active weeks only."

**Loading state (dashboard):** Dashboard data is local — load time should be <100ms. If indexedDB read is slow, show skeleton cards for ratio and trend only.

### Flow 4: Session Revisit

```
User opens past session from history
  → Full session detail: date, framing, AI output, original classification, behavioral signals (simplified display)
  → Revisit panel: "How does your original reasoning hold up?"
     → Affirm / Refine / Reverse (three buttons)
  → User selects one → logged with timestamp
  → Confirmation: "Revisit logged."
  → Return to history
```

**Error state (revisit):** If the revisit write fails: "Couldn't save your revisit. Try again." Non-blocking — the original session record is not affected.

---

## States Reference

| Screen | Empty | Loading | Error | First Use | Returning |
|---|---|---|---|---|---|
| Dashboard | "Complete 3 sessions to start your pattern" | Skeleton ratio cards | "Dashboard failed to load. Refresh." | Baseline mode banner | Full ratio + drift |
| Session entry | N/A | "Analyzing your session" (2–3s) | "Session couldn't be saved. Copy your framing." | Onboarding tooltip on forced reasoning gate | Standard flow |
| History | "No sessions yet. Start your first session." | Skeleton list items | "History failed to load. Refresh." | Empty with CTA | Reverse-chron list |
| Session detail | N/A | N/A | "Session data incomplete." | N/A | Full detail + revisit panel |

---

## Metric Instrumentation Hooks
*(see `02-success-metrics.md` for full metric definitions)*

| Metric | Instrumentation point |
|---|---|
| D/D/D ratio trend (#1) | Classification event on every session submission |
| Inspection depth / challenge rate (#2) | Challenge gesture events in AI output field |
| Independent decision rate (#3) | Forced reasoning gate: framing submitted = independent decision first |
| Revisitation rate (#4) | Session detail opens + revisit action events |
| Acceptance lag (#7) | `time_to_acceptance_seconds` on every session |

Metrics #5 (drift self-report) and #6 (reasoning quality reversal rate) require the revisit flow (F5) and a weekly opt-in survey prompt (out of scope for v1 UI, handled via email or in-app modal at week 4 and 8).
