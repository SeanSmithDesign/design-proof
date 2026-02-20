# Preset: Refined Simple

<!-- Inspired by Apple and Notion aesthetics -->

## Philosophy

Refined simplicity. The interface feels inevitable — as if no other design was possible. Whitespace is the primary design element. Typography does the heavy lifting. Color is so restrained it's almost monochrome. When everything is quiet, the one thing with color demands attention.

## Reference Apps

- **Apple** — System-level typography, whitespace as luxury, product showcase composition
- **Notion** — Friendly minimalism, content-first layout, subtle interactive states
- **Things** — Perfect task hierarchy, weight-based visual organization, calming palette

Study: How these apps make complex information feel simple. How whitespace creates premium perception. How a single weight change (regular → medium) creates clear hierarchy without size change.

## Typography

- **Heading font:** System sans or SF-style — Inter, SF Pro, or system-ui. The font should be invisible — you notice the words, not the typeface.
- **Body font:** Same family as headings. Consistency over variety.
- **Display:** Moderate sizing, heavy weight contrast. A 48px bold heading next to 16px regular body creates hierarchy through weight, not scale.
- **Heading hierarchy:**
  - H1: text-3xl font-bold tracking-tight
  - H2: text-2xl font-semibold
  - H3: text-lg font-medium
  - H4: text-base font-medium
- **Body:** text-base (16px), line-height 1.7 (leading-loose) — generous line-height is a signature
- **Captions:** text-sm text-muted-foreground, line-height 1.5
- **Anti-patterns:**
  - No display/novelty fonts — invisible typography only
  - No tight line-height (under 1.5 for body)
  - No uppercase labels — sentence case everywhere
  - No letter-spacing wider than `tracking-normal` for body text

## Color

- **Palette philosophy:** Near-monochrome. One neutral scale + one subtle accent. The accent appears in 3 or fewer places on any screen.
- **Neutrals:** Cool gray or warm stone. Full scale from near-white to near-black.
- **Primary accent:** Soft blue, muted teal, or warm coral — never saturated. Think pastel-adjacent.
- **Backgrounds:** White or near-white (`bg-white` or `bg-gray-50`). No dark mode as default (light-first aesthetic).
- **Guard exception:** `bg-white` is explicitly allowed in this preset for root/section backgrounds. This is a light-first aesthetic — white backgrounds are the design intent, not a dark-mode oversight. The guard check for "no hardcoded light/dark values" should not flag `bg-white` when this preset is active. Still flag `text-white`, `text-black`, and `dark:bg-*` overrides.
- **Status colors:** Desaturated. Success is sage green, not emerald. Error is dusty rose, not fire-engine red.
- **Anti-patterns:**
  - No saturated accent colors (no `blue-500`, `red-600`)
  - No gradient anything — flat is the point
  - No colored backgrounds on sections (white or near-white only)
  - No more than 3 accent-colored elements visible at once

## Spacing

- **Grid:** 8px base, but generous vertical rhythm. This aesthetic breathes vertically.
- **Component padding:**
  - Cards: `p-6` to `p-8` (24-32px)
  - Buttons: `px-4 py-2.5` (16px / 10px)
  - List items: `py-3` (12px) with `divide-y divide-border` between
- **Section spacing:**
  - Between sections: `py-16` to `py-24` (64-96px)
  - Between cards: `gap-6` (24px)
  - Between heading and content: `mb-8` (32px) — more than you think
- **Whitespace rule:** When in doubt, add more space. This aesthetic can't have too much whitespace.
- **Anti-patterns:**
  - No dense grids (more than 3 columns for cards)
  - No section spacing under `py-12`
  - No compact data tables — use generous row height (`h-14` minimum)

## Elevation

- **Philosophy:** Whitespace IS the elevation. Minimal shadows. Hairline borders for structure.
- **Levels:**
  - Cards: No shadow. Use `border border-border` (hairline, 1px)
  - Hover: `bg-accent/50` background shift (no shadow)
  - Dropdowns: `shadow-sm border border-border` (minimal shadow)
  - Modals: `shadow-md` (the only substantial shadow on the page)
- **Dividers:** `border-t border-border` or `divide-y divide-border` between list items
- **Anti-patterns:**
  - No `shadow-lg` or `shadow-xl` — too heavy for this aesthetic
  - No multiple shadow levels visible simultaneously
  - No border-radius larger than `rounded-lg` — keep corners modest

## Motion

- **Philosophy:** Organic and subtle. Animations should feel like natural physics, not choreography.
- **Timing:**
  - Hover: `duration-200` with `ease-out`
  - Transitions: `duration-300` with spring-like easing
  - Page entrance: `duration-400` with slight ease-out
- **Key moments:**
  - Hover: Background color shift only (no scale, no shadow)
  - Toggle: Smooth expand/collapse with natural deceleration
  - Page load: Simple fade-in, no upward motion
  - Checkboxes: Satisfying check animation (200ms)
- **Anti-patterns:**
  - No staggered reveals — everything in a section appears together
  - No scale effects on hover
  - No scroll-triggered animations (content is always visible)
  - No `duration` over 400ms

## Component Patterns

- **Cards:** `bg-card border border-border rounded-lg p-6`. Hairline border, no shadow. Content-first.
- **Buttons:**
  - Primary: `bg-foreground text-background rounded-lg px-4 py-2.5 font-medium` (black/white inversion)
  - Secondary: `border border-border text-foreground hover:bg-accent rounded-lg px-4 py-2.5`
  - Text: `text-primary hover:underline` (link-style)
- **Navigation:** Clean horizontal nav or sidebar. Active state: `font-medium text-foreground` vs inactive `text-muted-foreground`. No background highlights.
- **Forms:** Minimal inputs with `border-b border-border` (bottom-border only) or full border. Label above, generous spacing.
- **Lists:** `divide-y divide-border` with `py-4` per item. No bullet points — use whitespace to separate.

## Do / Don't Examples

### Whitespace
```tsx
// DO: Generous vertical rhythm
<section className="py-24">
  <h2 className="text-2xl font-semibold mb-8">Section Title</h2>
  <p className="text-muted-foreground leading-loose max-w-xl">
    Content with breathing room.
  </p>
</section>

// DON'T: Cramped layout
<section className="py-8">
  <h2 className="text-2xl font-semibold mb-2">Section Title</h2>
  <p className="text-gray-600 leading-tight">Content feels rushed.</p>
</section>
```

### Color Restraint
```tsx
// DO: Near-monochrome with minimal accent
<div className="bg-white text-foreground">
  <h2 className="text-foreground">Title</h2>
  <p className="text-muted-foreground">Description</p>
  <button className="text-primary">One accent element</button>
</div>

// DON'T: Multiple colors competing
<div className="bg-blue-50">
  <h2 className="text-blue-900">Title</h2>
  <p className="text-green-600">Description</p>
  <button className="bg-purple-500 text-white">Too many colors</button>
</div>
```

### Elevation
```tsx
// DO: Hairline borders, whitespace separation
<div className="border border-border rounded-lg p-6">
<ul className="divide-y divide-border">

// DON'T: Heavy shadows
<div className="shadow-xl rounded-2xl p-6">
```

### Typography Hierarchy
```tsx
// DO: Weight-based hierarchy (same family)
<h2 className="font-sans text-2xl font-semibold">Heading</h2>
<h3 className="font-sans text-lg font-medium">Subheading</h3>
<p className="font-sans text-base text-muted-foreground">Body</p>

// DON'T: Size-based hierarchy (too many sizes)
<h2 className="text-4xl font-bold">HEADING</h2>
<h3 className="text-2xl font-bold">SUBHEADING</h3>
<p className="text-sm">body</p>
```
