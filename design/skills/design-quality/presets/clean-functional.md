# Preset: Linear / Mercury

## Philosophy

Clean, functional, minimal. Every element earns its place. Information density is high but never feels cluttered — achieved through precise spacing, restrained color, and typographic hierarchy rather than decoration. The interface disappears; the content speaks.

## Reference Apps

- **Linear** — Navigation patterns, sidebar density, keyboard-first design, status indicators
- **Mercury** — Dashboard layout, data display, financial typography, trust-building UI
- **Raycast** — Minimal chrome, command palette patterns, fast-feeling interactions

Study: How these apps use whitespace to create hierarchy without borders. How typography weight alone separates sections. How accent color appears sparingly but with purpose.

## Typography

- **Heading font:** Inter, weights 600 (semibold) for section headings, 700 (bold) for hero only
- **Body font:** Inter, weight 400 (regular), 500 (medium) for emphasis
- **Display:** Hero headlines may use a serif accent (e.g., Instrument Serif italic) for ONE phrase only — not entire headings
- **Heading hierarchy:**
  - H1: text-4xl font-bold tracking-tight (hero only)
  - H2: text-2xl font-semibold tracking-tight
  - H3: text-lg font-semibold
  - H4: text-base font-medium
- **Body:** text-sm (14px), line-height 1.5 (leading-normal)
- **Labels:** text-xs font-medium uppercase tracking-wide (max 0.05em)
- **Anti-patterns:**
  - No serif fonts for UI elements (buttons, labels, nav)
  - No tracking wider than 0.05em
  - No font-light or font-thin — too low contrast
  - No more than 3 font weights on a single screen

## Color

- **Palette philosophy:** 2 colors maximum — one neutral family, one accent. Restraint is the design.
- **Neutrals:** Stone scale (warm gray, HSL-based CSS variables: `--stone-50` through `--stone-950`)
- **Primary accent:** Copper (`--copper-500` through `--copper-900`). Use sparingly — active states, CTAs, focus rings
- **Status colors:** Semantic tokens only — `text-success`, `text-warning`, `text-destructive`. Never raw Tailwind colors (no `text-green-500`)
- **Usage rule:** 90% neutral, 8% accent, 2% status. If more than two things are copper on screen, one shouldn't be.
- **Anti-patterns:**
  - No hardcoded hex values anywhere
  - No Tailwind palette colors (`bg-blue-500`, `text-red-400`)
  - No gradients (this aesthetic is flat)
  - No multiple accent colors — copper is the ONLY accent

## Spacing

- **Grid:** 8px base unit (CSS variable `--space-2`)
- **Component padding:**
  - Cards: `p-6` (24px)
  - Buttons: `px-4 py-2` (16px / 8px)
  - Input fields: `px-3 py-2` (12px / 8px)
  - List items: `px-4 py-3` (16px / 12px)
- **Section spacing:**
  - Between cards in a grid: `gap-4` (16px) or `gap-6` (24px)
  - Between page sections: `py-16` (64px) or `py-20` (80px)
  - Between heading and content: `mb-4` (16px)
- **Touch targets:** Minimum `min-h-11` (44px) for all interactive elements
- **Anti-patterns:**
  - No odd pixel values (5px, 7px, 13px) — stay on the 4px/8px grid
  - No `gap-1` between substantial elements — too tight
  - No `py-32` or larger — excessive padding signals empty design, not breathing room

## Elevation

- **Philosophy:** Shadows replace borders for depth. Borders are for form inputs and dividers only.
- **Levels:**
  - Resting cards: `shadow-xs` (barely visible, just enough to float)
  - Hover / active cards: `shadow-sm` (subtle lift)
  - Dropdowns, popovers: `shadow-md`
  - Modals, dialogs: `shadow-lg`
  - Sticky header: `shadow-sm` (replaces `border-b`)
- **Selected state:** `shadow-md ring-2 ring-primary` (shadow + ring, not background change)
- **Anti-patterns:**
  - No `border` on cards that have shadows (pick one)
  - No `shadow-xl` or `shadow-2xl` — too dramatic for this aesthetic
  - No colored shadows — shadows are always neutral

## Motion

- **Philosophy:** Functional, not decorative. Animations communicate state change, not delight.
- **Timing:**
  - Hover transitions: `duration-150` with `ease-in-out`
  - Page transitions: `duration-200`
  - Stagger delays: `50ms` between items (max 5 items)
- **Key moments:**
  - Hover: subtle shadow lift or background shift
  - Focus: ring appears with no animation (instant)
  - Page load: fade-in with slight upward motion (`fadeInUp` from `lib/animations.ts`)
  - Sidebar collapse: `duration-300 ease-in-out`
- **Anti-patterns:**
  - No bounce or spring physics — too playful
  - No `duration-500` or longer — feels sluggish
  - No parallax scrolling
  - No animations on text (no typing effects, no color shifts)

## Component Patterns

- **Cards:** `bg-card shadow-xs hover:shadow-sm transition-shadow rounded-lg p-6`. No border. Content-first.
- **Buttons:**
  - Primary: `bg-primary text-primary-foreground hover:bg-primary/90 rounded-md px-4 py-2 text-sm font-medium`
  - Secondary: `bg-secondary text-secondary-foreground hover:bg-accent rounded-md px-4 py-2 text-sm font-medium`
  - Ghost: `hover:bg-accent text-muted-foreground hover:text-foreground`
- **Navigation:** Background-based active state (`bg-accent`), not left-border indicators. Icon + label, no decorative dividers.
- **Forms:** Input with `border-input` border, `focus:ring-2 ring-ring`. Labels above inputs, not floating.

## Do / Don't Examples

### Colors
```tsx
// DO: Semantic tokens
<div className="bg-background text-foreground">
<span className="text-muted-foreground">
<button className="bg-primary text-primary-foreground">

// DON'T: Hardcoded or palette colors
<div className="bg-white text-gray-900">
<span className="text-stone-500">
<button className="bg-[#B87333] text-white">
```

### Typography
```tsx
// DO: Consistent hierarchy
<h2 className="font-sans text-2xl font-semibold tracking-tight text-foreground">
<p className="font-sans text-sm text-muted-foreground">

// DON'T: Inconsistent fonts/weights
<h2 className="font-serif text-3xl font-bold tracking-widest">
<p className="font-sans text-base font-light text-gray-400">
```

### Elevation
```tsx
// DO: Shadow-based depth
<div className="bg-card shadow-xs hover:shadow-sm transition-shadow rounded-lg p-6">

// DON'T: Border-based depth on cards
<div className="bg-white border border-gray-200 rounded-lg p-6">
```

### Spacing
```tsx
// DO: Grid-aligned spacing
<div className="grid gap-4">        {/* 16px — on grid */}
<div className="p-6">               {/* 24px — on grid */}
<button className="min-h-11 px-4">  {/* 44px touch target */}

// DON'T: Off-grid spacing
<div className="grid gap-3.5">      {/* 14px — off grid */}
<div className="p-5">               {/* 20px — off 8px grid */}
<button className="h-8 px-2">       {/* 32px — below touch target */}
```

### Dark Mode
```tsx
// DO: CSS variables (auto-adapt)
<div className="bg-background text-foreground border-border">

// DON'T: Hardcoded light/dark values
<div className="bg-white dark:bg-gray-900 text-black dark:text-white">
```
