# Current handoff

- Actor: Codex
- Updated: 2026-07-31 UTC
- Task: Establish safe alternating work between Codex, Claude, and the user.
- Outcome: Added a shared Git synchronization and conflict-avoidance protocol in `AI_WORKFLOW.md`, with agent entrypoints in `AGENTS.md` and `CLAUDE.md`.
- Files changed: `AI_WORKFLOW.md`, `AGENTS.md`, `CLAUDE.md`, `HANDOFF.md`, `.gitignore`.
- Validation: Reviewed the scoped Git diff; only the shared workflow and agent handoff files are included.
- Remaining work: Claude must clone or open this repository, read the protocol, verify its local Git identity/authentication, and confirm a clean synchronized `main` before editing.

Update this section at every agent handoff. Preserve the historical project notes below unless the user asks to revise them.

---

# D&D Partners Website — Historical Session Notes

## Project
Static single-file site: `/Users/viktorfedyuk/D&D website restart/index.html`
Dev server: Python HTTP at `http://localhost:4321` (run `python3 -m http.server 4321` from the project folder)

## Architecture

**Single file** — all HTML, CSS, JS in `index.html` (~2913 lines). No build step.

**Scroll**: `html { scroll-snap-type: y mandatory }` + `.section { height:100vh; scroll-snap-align:start; overflow:hidden }`. Six snap sections:
1. `.hero` — #home
2. `.about-section` — #about
3. `.services-section` — #services (+ `.prices-section` subscrolls inside)
4. `.cases-section` — #cases
5. `.qo-banner` — #special-offer ← **recently restructured**
6. `.contacts-section` — #contact ← **mobile added**

**Design tokens** (`:root` light / `[data-theme="dark"]`):
```
--bg: #F0F1F5 / #14151F
--t1: #222331 / #EDEFF5   ← primary text
--m1: #6A6D7C (same both themes) ← muted text
--wmc: #EEEFF3 / #1B1C29  ← contacts section bg
--c1..c4: surface tones
```

**Gradient** (used on ALL buttons site-wide):
`linear-gradient(96.58deg, #71B5C9 -17.37%, var(--t1) 57.3%)`

---

## Sections — current CSS state

### Special Offer (`#special-offer`, `.qo-banner`)
**Restructured this session** — canvas-scaling gadget removed.

```css
.qo-banner { display:flex; flex-direction:row; align-items:stretch; height:100vh; }

.qo-images {            /* left 55% — holds the 4 case-study phone images */
  position:relative; flex:0 0 55%; height:100%; overflow:hidden;
}

/* Images positioned as % within .qo-images (derived from original 1030×1080 canvas) */
.qo-img-1 { left:25.7%; top:28.5%; width:23.3%; height:56.3%; }
.qo-img-2 { left:30.6%; top:19.5%; width:48.5%; height:83.7%; }
.qo-img-3 { left:64.9%; top:47.3%; width:19.4%; height:37%;   }
.qo-img-4 { left:78.2%; top:35.2%; width:13.6%; height:21.6%; }
/* each .qo-img-N > .qo-img-float has ambient CSS keyframe animation */
/* .qo-img-2/3/4 .qo-img-inner have clip-path inset() values */

.qo-rainbow {           /* decorative SVG blob — absolute within .qo-images */
  position:absolute; left:13.7%; top:31.5%; width:47.9%; height:auto;
  transform: matrix(-0.77,-0.64,-0.64,0.77,0,0) scaleX(-1);
}

.qo-card {              /* right 45% — the offer card */
  flex:1; display:flex; flex-direction:column; justify-content:center;
  gap:32px; padding:60px 60px 60px 40px; position:relative; z-index:5;
}
```

**Card content:**
- `.qo-title` — "Landing page" (uppercase gradient text, 44px)
- `.qo-subtitle` — muted body text, 16px
- `.qo-list` — 3 checkmark items
- `.qo-btn` — gradient button "Let's discuss yours" (`href="mailto:hello@dnd.partners"`)

**Parallax JS** (scroll-based, no changes needed):
```js
layers = [qo-img-1 speed:-0.40, qo-img-2 speed:0.28, qo-img-3 speed:0.50, qo-img-4 speed:0.75]
// applies translateY(offset*speed) + opacity fade on banner scroll
```

---

### Contacts (`#contact`, `.contacts-section`)

Content lives OUTSIDE the scaled canvas wrapper, in `.contacts-columns` (1200px centred block):
```css
.contacts-columns {
  position:absolute; left:calc(50% - 600px); top:212px;
  width:1200px; height:588px; z-index:2;
}
.contacts-how    { position:absolute; left:24px;  top:0; width:430px; }  /* left col */
.contacts-form-col { position:absolute; left:620px; top:0; width:580px; } /* right col */
```

**Socials + legal** are direct children of `.contacts-section` (not inside .contacts-columns):
```css
.contacts-socials { position:absolute; left:calc(50% - 576px); bottom:28px; }
.contacts-legal   { position:absolute; left:calc(50% + 20px);  bottom:36px; }
```

**Trust row** (3 lines, all `color:var(--m1) = #6A6D7C`):
- Guarantee: / Support: / Discount: (bold label + muted body, same colour)

**Legal row** text: `color:#B6B9C6` (lighter than --m1)

**Highlight card** (`.contacts-step-highlight`) cycles through 6 steps:
- HOLD=2000ms slow fade 1→0.5, then exit 0.5→0 with slide
- Right-column cards (steps 2,4,6): 260px wide, +10px right shift from center

---

## Proposal panel (right column — двокроковий флоу)

Triggered by: valid `contacts-form` submit (validation first, see below).

### Step 1 — Thank You
- Heading via `.section-title` class: "Thank You"
- Subtitle via `.section-tagline` class: "Now let's make it perfect for you"
- Button `.proposal-adddetails-btn` (200×36, gradient) → opens Step 2

### Step 2 — Let us know (questions)
- Heading: "Let us know" / tagline: "These details will help us prepare a more personalized offer" (same `.section-title`/`.section-tagline`)
- Questions order:
  1. Dropdown "What service are you looking for?" — 2 cols: Landing Page/Website/Mobile App/SaaS/Ecommerce + Consultation/Web Dev/UX-UI Audit/AI service/Logo & Branding — opens **downward**
  2. Input "Who's your audience?"
  3. Input "Any competitors or references you like?"
  4. Input "Do you have any materials / references / inspirations you'd like to use for it?"
  5. Input "Is this project urgent, do you have a specific deadline?"
  6. Input "Do you have an estimated budget range for this project?"
  7. Dropdown "What's the best way to communicate with you?" — 1 col: Email/WhatsApp/Video Call/Telephone/Other — opens **UPWARD** (class `.proposal-dropdown--up`)
- Actions: `.proposal-submit-btn` "Add Project Details" (flex:0 0 350px) + `.proposal-cancel-btn` "Cancel"
- **Cancel** → hides questions panel, shows Step 1 (ty panel) again — NOT original form

### Form validation
Runs on `.contacts-submit-btn` click before showing Step 1:
- Name ≥ 3 chars → error "Min 3"
- Email valid regex → error "Invalid email"
- Textarea ≥ 120 chars → error "Min 120"
- Error style: `border:1px solid #D90808` on field + red span `.contacts-err` at `position:absolute; right:12px`
- Fields are dynamically wrapped in `.contacts-field` div on first attempt

### Filled input style
`.proposal-input:not(:placeholder-shown)` → `font-size:13px; color:#60638A` (no font-variant, no letter-spacing)

### Dropdown gap
`.proposal-dd-col { gap: 8px }` — padding per item: `4px 8px`

### Book a call
`.contacts-schedule-btn { color: var(--m1) }` — same muted color as trust row (#6A6D7C)

---

## Buttons — gradient style (applied to all 5):

| Class | Text | Location |
|-------|------|----------|
| `.nav-cta` | Contact Us | Header |
| `.about-btn` | Let's talk how we evaluate your Business | About section |
| `.svc-contact-btn` | Request it | Services section |
| `.btn-outline` | I want similar | Cases section |
| `.qo-btn` | Let's discuss yours | Special Offer |

All: `background: linear-gradient(96.58deg, #71B5C9 -17.37%, var(--t1) 57.3%); color:#fff; border:none;`
Hover: `opacity:0.9; transform:translateY(-2px)`

Contacts submit button (`.contacts-submit-btn`) uses same gradient — was the source/reference button.

---

## Mobile CSS (`@media (max-width:768px)`)

### Special Offer
```css
.qo-banner { flex-direction:column; height:auto; min-height:100vh; }
.qo-images { flex:0 0 46vh; width:100%; height:46vh; }
.qo-card   { padding:36px 24px 40px; gap:24px; justify-content:flex-start; }
.qo-title  { font-size:34px; width:auto; }
.qo-subtitle { width:auto; }
```

### Contacts
```css
.contacts-section.section { height:auto; min-height:100vh; overflow:visible; scroll-snap-align:none; }
.contacts-columns { position:relative; left:0; top:0; width:100%; padding:80px 24px 0;
                    display:flex; flex-direction:column; gap:48px; box-sizing:border-box; }
.contacts-how      { position:static; width:100%; }
.contacts-form-col { position:static; width:100%; height:auto; }
.contacts-steps    { grid-template-columns:repeat(2,1fr); column-gap:24px; margin-left:0; margin-top:32px; }
.contacts-form-row { flex-direction:column; gap:12px; }
.contacts-actions  { flex-direction:column; gap:12px; }
.contacts-socials  { position:relative; left:0; bottom:auto; padding:32px 24px 0; justify-content:flex-start; }
.contacts-legal    { position:relative; left:0; bottom:auto; padding:16px 24px 40px; }
.contacts-step-highlight { display:none; }
```

---

## Typography reference

| Token | Value | Use |
|-------|-------|-----|
| Body text | Roboto 13px / 20px | descriptions, trust, captions |
| UI labels | SF Pro Text 13px | buttons, steps, nav |
| Section titles | SF Pro Text, varies | section headings |
| Gradient text | `background-clip:text` + gradient | qo-title, hero headline |

**Cases description** recently updated: `font-size:13px; line-height:20px` (was 16/25).

---

## Files in project folder

Images: `L1.png`, `Landpic_2.png`, `Landpic_3.png`, `Landpic_4.png` (qo section phones)
Icons: `checkmark.svg`, `logodd.svg`, `Logo D&D.svg`, `cases-tag-icon.svg`
Cases: `Case3.png`–`Case5.png`, `case-*.png`, `case2.png`, `case6.png`, `case7.png`
Decoration: `tube.png`, `tube-1.svg`, `tube-2.png`, `rainbow.png`, `rainb.svg`
Blob SVGs: `cases-blob-1.svg`, `cases-blob-2.svg`, `cases-blob-3.svg`

---

## Known context to keep in mind

- `--m1` has NO dark-theme override — stays `#6A6D7C` in both themes (deliberate)
- Contacts `.contacts-section` background is `var(--wmc)` not `var(--bg)`
- `overflow:hidden` on `.section` clips anything below 100vh — socials/legal are section-level children to avoid clipping
- The `.contacts-steps` grid column widths are `188px` fixed (on desktop) — right-column desc text is 210px wide, wider than cell
- Highlight card `measure()` uses `halfContent = 105` for right steps (= 210/2) to center on the text, not on the grid cell
