# Centaur: Design Direction
**Version:** v1 (beta)
**Grounded in:** `centaur_brand_seed.md`, `06-functional-spec.md`, `05-persona-jtbd.md`
**Intended consumer:** Claude Design (Anthropic) — this file is formatted as a DESIGN.md ingestion spec

---

## Open Questions (resolve before Claude Design session)

1. **Direct/Delegate/Defer color logic:** Should the three classifications have distinct colors, or does color imply judgment? The brand is explicitly an instrument, not a coach. Color-coded classifications risk reading as good/bad/neutral rather than measurement/measurement/measurement. Option A: monochromatic with typographic differentiation only. Option B: three neutral, equally-weighted colors (no green/red connotations). Decide before the Claude Design session — this is the highest-stakes visual decision in the product.

2. **Drift indicator form:** The drift signal is a single computed direction: rising, stable, or falling. Should it render as (a) a number with a trend arrow, (b) a text descriptor only, or (c) a small sparkline with an overlay label? The instrument aesthetic favors (c), but (b) is lower cognitive load for a user checking in 30 seconds. Resolve before screen design.

3. **Mobile vs. desktop priority:** Jordan (`05-persona-jtbd.md`) runs sessions at a desk. The session entry flow (forced reasoning gate → AI output paste) is keyboard-native. Mobile is secondary but must not be broken. Claude Design session should start with a desktop-first canvas (1280px) and verify mobile as a constraint check, not a co-primary design target.

4. **Empty state illustration vs. text only:** The brand avoids stock illustrations and decorative elements. Empty states should be text-led. Confirm before Claude Design session — do not let Claude Design default to SVG decorative illustrations in empty states.

---

## Brand Foundation (for Claude Design ingestion)

*This section is source material. Claude Design infers visual treatment from it; it does not override it.*

**Archetypes (Mark and Pearson):** Sage primary, Magician secondary. Sage brands are trusted because they are honest, including when honesty is uncomfortable. The Magician transforms the user, not the output. Together: an instrument that changes how you think, communicated with intellectual honesty.

**Brand personality (Aaker):** Dominant dimensions are Competence and Sophistication. Low Excitement, low Ruggedness, medium-high Sincerity. Tonal range: warm not cute, expert not academic, direct not blunt, restrained not cold.

**Brand essence:** Instrument for judgment. Three words. Every design decision is testable against them.

**Positioning:** Centaur is not a productivity tool. It is not a wellness app. It is not a dashboard. It is an instrument — in the same semantic register as a thermometer, an EEG, a balance scale. Instruments surface truth without editorializing. The product communicates that the user is sharper after they use it than before, without flattering them.

---

## Visual Direction (what Claude Design should infer)

### Palette

Cool, restrained. Two-color floor — a near-black background or a deep cool gray paired with an off-white or silver-white — with a single accent color for state changes and classification verdicts. The accent should read as a measurement indicator, not a brand color. Think instrument display, not app primary.

**What to avoid:** Warm earth tones, cream, terracotta, warm grays. Purple or indigo gradients — the default AI-product palette. Blue-to-purple gradients of any kind. Saturated hues that read as "fun" or "active."

**Reference aesthetic:** The palette of a Bloomberg terminal or a medical monitoring display, pulled back to consumer-product restraint. Controlled, precise, not cold.

### Typography

Two faces. One workhorse sans-serif for all interface text — data, labels, navigation, body. One display face for the moments that carry weight: the classification verdict, the drift indicator, the session count. The display face should have enough personality to signal that a verdict has been delivered, but not so much that it decorates rather than informs.

**What to avoid:** Inter, Roboto, Arial, system fonts — the default AI product stack. Any script face. Any decorative or novelty face. The type should feel like it belongs in a precision instrument, not a startup landing page.

### Layout

High information density without crowding. White space is a design feature, not filler. The ratio dashboard, the session history, and the session entry form are all information-dense screens — they should read closer to a professional terminal than to a consumer mobile app, while remaining legible and navigable without training.

Grid: tight, consistent. Generous line-height on reading text. Compact on data displays (ratios, session list). No decorative spacing — every margin earns its place.

### States (Direct / Delegate / Defer)

These three classifications are the product's primary visual vocabulary. They must:

- Read as measurement, not judgment. No green for Direct, no red for Defer.
- Be visually distinct without connotation. Typographic differentiation (weight, case, spacing) is preferred over color differentiation. If color is used, it must be three equally-weighted, non-valenced tones.
- Be immediately scannable in the session history list (one classification label per row).
- Scale from single label (history list) to headline (classification verdict after session) without losing legibility.

### Motion

Functional only. Two permitted uses: state transition (session flow moving from gate to output to classified) and data update (ratio refreshing on new session save). No decorative animation. No entrance animations on static content. No hover micro-animations on informational elements. If it does not signal state change, it does not move.

---

## Core Screens (intent, not wireframe)

This section describes what each screen must communicate and its information hierarchy. Claude Design uses this to infer layout, not to copy a wireframe.

### Screen 1: Dashboard

**Communication goal:** The user sees their pattern at a glance. One number matters most right now: whether their Defer rate is moving.

**Information hierarchy:**
1. Drift indicator (top, prominent) — only shown after week 4; before that, baseline mode message
2. Current week ratio (D/D/D percentages, this week)
3. 4-week trend (three sparklines, small, aligned)
4. Session count (this week / all time, subdued)
5. New session CTA (always accessible, not dominant)

**Tone:** An instrument reading, not a report card. The numbers are neither good nor bad — they are measurements. The design must not editorialize.

**Empty / baseline state:** Text-only. "Building your baseline — 3 sessions logged, 2 weeks remaining." No illustration. No progress bar. Just an honest statement of where things are.

### Screen 2: Session Entry (Core Loop)

**Communication goal:** This is where the product's behavioral change mechanism lives. Three stages in strict sequence: forced reasoning, AI output review, verdict.

**Stage 1 — Forced Reasoning Gate:**
- Single text input, large and inviting — the user is being asked to think out loud
- One instruction, no more: something in the register of "What's your take before you look at the AI's answer?"
- No UI chrome. No ratio display. No history. Just this prompt.
- Timer running invisibly (not displayed — would gamify the framing time signal)

**Stage 2 — AI Output Review:**
- User's framing remains visible above (they can see the delta)
- AI output text area below
- Challenge gesture affordance: user can select/highlight text in AI output to mark points of friction or challenge
- Submit is available but not pushed — the screen should not rush acceptance

**Stage 3 — Verdict:**
- Classification displayed as a headline: "Direct." / "Delegate." / "Defer."
- Single word. Period. No explanation, no score breakdown.
- Optional note field below, secondary
- Confirm and save

**Tone:** The verdict screen is the one moment of authority in the product. It should feel like an instrument reading settling — precise, non-negotiable, value-neutral.

### Screen 3: Session History

**Communication goal:** The user's record. Scannable, not analyzed.

**Information hierarchy:**
- Filter controls (Direct / Delegate / Defer / All) — compact, not prominent
- Session list, reverse-chronological
- Each row: date, classification label, framing excerpt (first ~80 characters)
- Tap to open session detail

**Tone:** A log. Not a report, not a dashboard. The history screen should feel like a notebook — dense, honest, revisitable.

### Screen 4: Session Detail + Revisit

**Communication goal:** The full record of one session, plus the revisit action.

**Information hierarchy:**
1. Date and classification (header)
2. User's original framing (labeled clearly as "Your framing")
3. AI output (labeled clearly as "AI output")
4. Behavioral signal summary (simplified — show only the delta between framing and AI output, plus acceptance time; hide raw scores)
5. Revisit panel: "How does this hold up?" → Affirm / Refine / Reverse
6. If revisited: show revisit action and timestamp

**Tone:** The revisit panel is the product's most powerful long-term mechanism. It should feel significant — a deliberate act, not a quick tap. The three options should have enough visual weight that the user pauses before selecting.

---

## Claude Design Ingestion: How to Use This File

### What Claude Design does with this spec

Claude Design (claude.ai/design) reads DESIGN.md files as part of its design system onboarding. When starting a new project in Claude Design:

1. Upload this file (`08-design-direction.md`) as a project input.
2. Paste the **Claude Design Prompt** below as the opening chat instruction.
3. Use inline comments for component-level adjustments (e.g., "This classification label needs more weight"). Use chat for structural changes (e.g., "Redesign the dashboard hierarchy").
4. Use the Tweaks panel for spacing, density, and color temperature adjustments — these do not consume chat tokens.
5. When design is validated, use "Send to Claude Code" to generate the handoff bundle. Claude Code will receive the component structure, design tokens, and layout hierarchy as a machine-readable spec.

**Do not ask Claude Design to:**
- Add illustrations or decorative SVGs
- Use gradients on any surface
- Use Inter, Roboto, or system fonts as the primary typeface
- Apply color connotations to Direct/Delegate/Defer (no green/red)
- Animate anything that is not a state transition

### Claude Design Prompt (paste as opening instruction)

```
Design a cognitive instrument application called Centaur. This is not a productivity app or a wellness app. It is a precision measurement tool for knowledge workers who want to track how deliberately they use AI in their reasoning.

Brand: Sage/Magician archetype. Competence and Sophistication are dominant. Think Bloomberg terminal meets consumer restraint — high information density, cool palette, restrained typography. The product must feel like an instrument that delivers a verdict, not an app that cheerleads.

Screens to design (in order):
1. Dashboard — shows the user's Direct/Delegate/Defer ratio for the current week, a 4-week sparkline trend, and a drift indicator. Instrument-grade layout. Data-forward.
2. Session Entry — three sequential stages: (a) forced reasoning text input with a single instruction prompt, (b) AI output text area with challenge-gesture highlighting capability, (c) classification verdict displayed as a single word headline. The verdict stage is the moment of authority — design it accordingly.
3. Session History — a dense, scannable log. Each row: date, classification label, framing excerpt. Filter controls for Direct/Delegate/Defer.
4. Session Detail + Revisit — full session record plus a revisit panel with three weighted options: Affirm, Refine, Reverse.

Visual constraints:
- Palette: cool, restrained. Two-color floor (near-black or deep cool gray + off-white). One accent for state changes. No warm tones, no purple gradients, no saturated hues.
- Typography: one workhorse sans for interface text, one display face for verdict moments (classification headline, drift indicator). Not Inter, not Roboto.
- Direct/Delegate/Defer must be visually distinct without color connotation. Typographic differentiation preferred over color. If color is used, three equally-weighted, non-valenced tones only.
- Motion: functional only. State transitions and data updates. Nothing decorative.
- Empty states: text only. No illustrations.
- Layout: tight grid, high density, generous white space used deliberately not decoratively.

Avoid: stock illustrations, gradients on body copy, emoji in UI, circuit-board or particle-effect AI iconography, anything that signals "fun" or "playful," any type treatment that reads as startup-generic.

Start with the Dashboard at desktop width (1280px). Show three variations: one with a dark background, one with a light cool-gray background, one with the drift indicator in a "rising" state (Defer rate worsening). I will select one direction and iterate from there.
```

### Handoff to Claude Code

Once a design direction is validated in Claude Design, use "Send to Claude Code" to generate the handoff bundle. The bundle will contain:

- Component structure matching the four screens above
- Design tokens: palette, type scale, spacing units, border radii
- Layout hierarchy: grid definitions, information density rules
- Referenced assets: any icons or custom marks generated during the session

Claude Code receives this bundle and builds against the component library defined in `07-technical-spec.md` (React 18, CSS Modules). The handoff eliminates the pixel-to-code translation step — the design system and the implementation share the same token vocabulary.

---

## What This File Is Not

This file does not specify typefaces, exact hex values, exact spacing units, or exact grid columns. Those decisions belong to Claude Design — it has the context, the rendering capability, and the iteration speed to make those calls correctly. This file gives Claude Design everything it needs to make the right choices: the archetype, the personality dimensions, the aesthetic reference, the screen inventory, the tone constraints, and the explicit avoid list.

The heavy design lifting happens in Claude Design. This file is the brief that makes that session productive rather than generic.
