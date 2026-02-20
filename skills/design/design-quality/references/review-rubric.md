# Review Rubric

Scoring criteria for Design Quality Review mode. Produces a **composite score** from three layer sub-scores, each broken into categories.

## Composite Score

The composite score combines three layer sub-scores. Only active layers contribute.

| Layer | Sub-Score | Categories |
|-------|-----------|------------|
| **Craft** (bringhurst) | /30 | Measure & Readability, Vertical Rhythm, Type Scale & Weight |
| **Aesthetic** (preset) | /40 | Hierarchy, Color & Tokens, Spacing & Grid, Elevation & Polish |
| **Accessibility** (a11y) | /30 | WCAG Compliance, Visual Quality, Component States |
| **Composite** | /100 | Weighted sum of active layers |

**When all layers active:** Craft 30 + Aesthetic 40 + A11y 30 = 100
**When only bringhurst + a11y:** Craft 40 + A11y 60 = 100 (re-weighted)
**When only aesthetic + a11y:** Aesthetic 55 + A11y 45 = 100 (re-weighted)

---

## Scoring Scale

| Rating | Points | Meaning |
|--------|--------|---------|
| **Pass** | Full marks for category | Meets or exceeds standards. No errors, minor suggestions at most. |
| **Needs Work** | ~50-70% of category | Functional but has warnings. Specific improvements identified. |
| **Fail** | 0-40% of category | Errors present. Does not meet standards. Must fix before shipping. |

The score is qualitative — treat as directional, not precise.

---

## Craft Sub-Score (Bringhurst Layer) — /30

### Category: Measure & Readability (~10 pts)

Does text have room to breathe? Can the eye travel the line comfortably?

**Pass Criteria:**
- [ ] Body text containers constrained to 45-75ch
- [ ] Headings have appropriate measure (can be wider than body)
- [ ] Multi-column layouts use narrower measures (35-50ch)
- [ ] No full-width text blocks without constraining class

**Common Violations:**
| Violation | Severity | Example |
|-----------|----------|---------|
| Body text in `max-w-4xl`+ container | Warning | ~100ch measure, eye loses its place |
| `w-full` on paragraph text | Warning | Measure depends entirely on viewport |
| No `max-w-*` on text content | Warning | Unbounded measure |

### Category: Vertical Rhythm (~10 pts)

Does spacing down the page follow a consistent beat?

**Pass Criteria:**
- [ ] Spacing values share a common base unit (or close multiples)
- [ ] Heading-to-content gap < heading-to-previous-section gap (proximity principle)
- [ ] Consistent gaps between same-level elements
- [ ] Line-height contributes to the rhythm (body 1.5+)

**Common Violations:**
| Violation | Severity | Example |
|-----------|----------|---------|
| Unrelated spacing values | Warning | `mb-3`, `mt-5`, `gap-7` in same component |
| Heading closer to previous section than own content | Warning | `mt-4 mb-8` on heading (inverted proximity) |
| Inconsistent element gaps | Warning | Some cards `gap-4`, others `gap-6` at same level |

### Category: Type Scale & Weight (~10 pts)

Do font sizes form a coherent progression? Are weights used with restraint?

**Pass Criteria:**
- [ ] Font sizes follow a discernible ratio between adjacent steps
- [ ] Maximum 3 font weights per component
- [ ] Weight assignments are consistent (same level = same weight)
- [ ] Maximum 2 font families (excluding code blocks)
- [ ] Each text style variation earns its place

**Common Violations:**
| Violation | Severity | Example |
|-----------|----------|---------|
| Arbitrary size jumps | Suggestion | 14px → 24px (1.71x) skipping intermediate |
| 4+ font weights in component | Suggestion | thin + regular + medium + semibold + bold |
| 3+ font families | Warning | sans + serif + mono + display |
| Inconsistent weight assignment | Warning | Some H2s bold, others semibold |

---

## Aesthetic Sub-Score (Preset Layer) — /40

### Category: Hierarchy (~10 pts)

Does the interface guide the eye? Can you identify the most important element instantly?

**Pass Criteria:**
- [ ] Clear primary focal point on the page/section
- [ ] Heading sizes decrease logically (H1 > H2 > H3)
- [ ] Visual weight matches content importance
- [ ] No two elements compete for primary attention

**Common Violations:**
| Violation | Severity | Example |
|-----------|----------|---------|
| Multiple elements same visual weight | Warning | Two `text-2xl font-bold` headings side-by-side |
| Heading levels skipped | Warning | H1 followed directly by H4 |
| No clear CTA | Error | Page section with no primary action |

### Category: Color & Tokens (~10 pts)

Are colors used according to preset philosophy?

**Pass Criteria:**
- [ ] All colors via semantic tokens or CSS variables
- [ ] No hardcoded hex values
- [ ] No Tailwind palette colors (no `bg-blue-500`)
- [ ] Accent color usage within preset limits
- [ ] Text contrast ratios meet WCAG AA (4.5:1 normal, 3:1 large)
- [ ] Dark mode support via CSS variables

**Common Violations:**
| Violation | Severity | Example |
|-----------|----------|---------|
| Hardcoded hex color | Error | `className="text-[#B87333]"` |
| Tailwind palette color | Error | `bg-green-500` instead of `text-success` |
| `bg-white` / `text-black` (without preset exception) | Error | Breaks dark mode |
| Low contrast text | Error | `text-muted-foreground` on `bg-muted` (check ratio) |
| Accent overuse | Warning | 5+ accent-colored elements on screen |

### Category: Spacing & Grid (~10 pts)

Is spacing consistent and on-grid?

**Pass Criteria:**
- [ ] All spacing values on the preset's grid (typically 4px/8px increments)
- [ ] Consistent padding within same component type
- [ ] Section spacing matches preset spec
- [ ] No cramped layouts (adequate breathing room)

**Common Violations:**
| Violation | Severity | Example |
|-----------|----------|---------|
| Off-grid spacing | Warning | `p-5` (20px) when grid is 8px |
| Inconsistent card padding | Warning | Some cards `p-4`, others `p-6` at same level |
| Missing section spacing | Warning | Two sections with only `py-4` between them |

### Category: Elevation & Polish (~10 pts)

Does the interface feel finished? Does depth match the preset's system?

**Pass Criteria:**
- [ ] Elevation hierarchy matches preset (consistent shadow usage)
- [ ] Hover states on all interactive elements
- [ ] Transition on hover/active state changes
- [ ] Consistent border-radius across same-level elements
- [ ] Motion matches preset philosophy

**Common Violations:**
| Violation | Severity | Example |
|-----------|----------|---------|
| Wrong elevation pattern for preset | Warning | Shadows on cards in apple-notion preset |
| Missing hover state | Warning | Card with no visual feedback on hover |
| No transition on state change | Warning | Shadow appears instantly |
| Inconsistent border-radius | Suggestion | Mix of `rounded-md` and `rounded-lg` |

---

## Accessibility Sub-Score (A11y Layer) — /30

### Category: WCAG Compliance (~15 pts)

Can all users access the interface?

**Pass Criteria:**
- [ ] All interactive elements have accessible names (visible text or `aria-label`)
- [ ] Touch targets >= 44px minimum dimension
- [ ] Focus indicators visible on tab navigation
- [ ] `prefers-reduced-motion` respected on animations
- [ ] Color not the only way to convey information
- [ ] `aria-live` regions for dynamic content updates
- [ ] Semantic HTML used appropriately
- [ ] Heading hierarchy maintained (no skipped levels)

**Common Violations:**
| Violation | Severity | Example |
|-----------|----------|---------|
| Icon-only button without `aria-label` | Error | `<button><X /></button>` |
| Touch target too small | Error | `h-8 w-8` icon button (32px) |
| No focus indicator | Error | `focus:outline-none` without ring replacement |
| Color-only status | Warning | Green dot for "active" with no text label |
| Missing aria-live | Warning | Toast without `aria-live="polite"` |
| Animations ignore reduced-motion | Warning | No `motion-safe:` or media query |

### Category: Visual Quality (~8 pts)

Are visual design fundamentals sound?

**Pass Criteria:**
- [ ] Consistent spacing within component levels
- [ ] No overflow or alignment issues
- [ ] Font usage consistent (families, weights)
- [ ] Adequate contrast on all text

**Common Violations:**
| Violation | Severity | Example |
|-----------|----------|---------|
| Spacing inconsistency within component | Warning | Cards with mixed padding |
| Content overflow | Warning | Text breaks container bounds |
| Mixed unauthorized fonts | Warning | Font not in preset |

### Category: Component States (~7 pts)

Do components handle all states gracefully?

**Pass Criteria:**
- [ ] Loading states for async actions
- [ ] Empty states for data-dependent components
- [ ] Error states for forms and data fetching
- [ ] Disabled states styled appropriately

**Common Violations:**
| Violation | Severity | Example |
|-----------|----------|---------|
| Missing loading state | Warning | Button submits with no indicator |
| Missing empty state | Suggestion | "No data" shown as blank area |
| Missing error state | Warning | Form submission fails silently |

---

## Anchor Examples

Concrete code examples to calibrate scoring consistency across sessions.

### 90+ Score Component (All Layers Pass)

```tsx
<section className="py-24">
  <div className="max-w-prose mx-auto">
    <h2 className="text-2xl font-semibold mb-6 text-foreground">
      Recent Activity
    </h2>
    <p className="text-muted-foreground leading-relaxed mb-8">
      Your latest updates and notifications.
    </p>
    <ul className="divide-y divide-border" role="list">
      {items.length === 0 ? (
        <li className="py-6 text-center text-muted-foreground">
          No recent activity
        </li>
      ) : (
        items.map((item) => (
          <li key={item.id} className="py-4">
            <button
              className="w-full text-left min-h-11 px-4 py-3
                         rounded-lg hover:bg-accent
                         transition-colors duration-150
                         focus:outline-none focus-visible:ring-2
                         focus-visible:ring-ring"
              aria-label={`View ${item.title}`}
            >
              <span className="text-sm font-medium text-foreground">
                {item.title}
              </span>
              <span className="text-sm text-muted-foreground ml-2">
                {item.date}
              </span>
            </button>
          </li>
        ))
      )}
    </ul>
  </div>
</section>
```

**Why 90+:**
- Craft: `max-w-prose` (65ch measure), consistent spacing rhythm (py-24 → mb-6 → mb-8 → py-4), two weights (semibold + medium)
- Aesthetic: Semantic tokens throughout, 8px grid spacing, hover + transition, consistent radius
- A11y: `role="list"`, `aria-label`, `min-h-11` touch targets, `focus-visible:ring`, empty state handled

### 70-80 Score Component (Needs Work)

```tsx
<div className="py-8">
  <div className="max-w-4xl mx-auto">
    <h2 className="text-2xl font-bold mb-2">Recent Activity</h2>
    <p className="text-gray-500 mb-4">Your latest updates.</p>
    <div>
      {items.map((item) => (
        <div
          key={item.id}
          className="p-4 border-b border-gray-200 cursor-pointer"
          onClick={() => navigate(item.id)}
        >
          <span className="font-semibold">{item.title}</span>
          <span className="text-gray-400 ml-3">{item.date}</span>
        </div>
      ))}
    </div>
  </div>
</div>
```

**Why 70-80:**
- Craft: `max-w-4xl` (~100ch, measure violation), `mb-2` too tight under heading, but scale is reasonable
- Aesthetic: `text-gray-500`, `border-gray-200` (palette colors, not tokens), `py-8` too tight for section, no hover state
- A11y: `div onClick` without role/tabIndex/keyboard handler, no empty state, no focus indicator

### Below 60 Score Component (Fail)

```tsx
<div className="p-3">
  <h2 className="text-lg font-light text-[#333]">Activity</h2>
  <div className="w-full">
    {items.map((item) => (
      <div key={item.id} className="p-2 bg-white hover:bg-gray-100"
           style={{ borderBottom: '1px solid #eee' }}>
        <span className="font-bold text-sm">{item.title}</span>
        <button className="h-6 w-6 float-right">
          <ChevronRight className="w-3 h-3" />
        </button>
      </div>
    ))}
  </div>
</div>
```

**Why below 60:**
- Craft: `w-full` (unbounded measure), no rhythm (p-3, p-2 — unrelated), font-light (extreme weight)
- Aesthetic: Hardcoded `text-[#333]`, inline `style`, `bg-white`, `bg-gray-100` (palette), no transitions, `float-right`
- A11y: Icon button `h-6` (24px, well below 44px), no `aria-label`, no keyboard handling, no empty state

---

## Output Format

### Composite Score Report

```markdown
## Design Quality Review

**Composite Score: XX/100** (Preset: [name])

### Layer Sub-Scores
| Layer | Score | Rating |
|-------|-------|--------|
| Craft (bringhurst) | XX/30 | Pass / Needs Work / Fail |
| Aesthetic ([preset]) | XX/40 | Pass / Needs Work / Fail |
| Accessibility (a11y) | XX/30 | Pass / Needs Work / Fail |

### Category Breakdown
| Layer | Category | Score | Key Finding |
|-------|----------|-------|-------------|
| Craft | Measure & Readability | X/10 | [one-line summary] |
| Craft | Vertical Rhythm | X/10 | [one-line summary] |
| Craft | Type Scale & Weight | X/10 | [one-line summary] |
| Aesthetic | Hierarchy | X/10 | [one-line summary] |
| Aesthetic | Color & Tokens | X/10 | [one-line summary] |
| Aesthetic | Spacing & Grid | X/10 | [one-line summary] |
| Aesthetic | Elevation & Polish | X/10 | [one-line summary] |
| A11y | WCAG Compliance | X/15 | [one-line summary] |
| A11y | Visual Quality | X/8 | [one-line summary] |
| A11y | Component States | X/7 | [one-line summary] |

### Strengths
- [What's working well — be specific, reference layer]

### Issues Found (N)
- **[Layer]** Error `file:line` — Issue → Fix
- **[Layer]** Warning `file:line` — Issue → Fix
- **[Layer]** Suggestion `file:line` — Opportunity → Improvement

### Auto-Fixable (N of M issues)
Errors and warnings that can be automatically corrected.
```
