# Preset: Stripe / Vercel

## Philosophy

Premium, polished, depth. This aesthetic communicates trust through craft. Rich visual layers, confident dark backgrounds, and generous typography create a sense of quality. The interface feels expensive — not through ornamentation, but through precision and depth.

## Reference Apps

- **Stripe** — Gradient usage, card depth, documentation layout, pricing page composition
- **Vercel** — Typography confidence, dark theme execution, whitespace as a luxury element
- **Resend** — Developer tool polish, monospace accents, API-first branding

Study: How these apps use layered depth (shadows + gradients + borders) together. How dark backgrounds make accent colors pop. How generous font sizing creates authority.

## Typography

- **Heading font:** A distinctive display font — GT Walsheim, Satoshi, or Geist. NOT Inter or system fonts. The heading font is a brand signature.
- **Body font:** Clean sans-serif — Geist, Inter, or system-ui. Prioritize legibility.
- **Display:** Large and confident. Hero text can be 64-80px. Let it breathe.
- **Heading hierarchy:**
  - H1: text-5xl or text-6xl, font-bold, tracking-tight
  - H2: text-3xl font-semibold tracking-tight
  - H3: text-xl font-semibold
  - H4: text-base font-medium
- **Body:** text-base (16px), line-height 1.6 (leading-relaxed)
- **Code/mono:** Monospace accents for technical content — `font-mono text-sm`
- **Anti-patterns:**
  - No small hero text (under 48px)
  - No tight body line-height (under 1.5)
  - No generic fonts — the display font should be recognizable

## Color

- **Palette philosophy:** Rich but controlled. Dark backgrounds as a canvas, accent colors as highlights. Gradients are encouraged.
- **Dark theme:** Primary mode. `bg-gray-950` or `bg-black` backgrounds with `text-gray-100` text.
- **Accent:** Bold, saturated. Electric blue, vivid purple, warm orange — one signature color.
- **Gradients:** Linear gradients on hero sections, card backgrounds, or CTAs. Keep to 2-3 stops.
- **Status colors:** Saturated and clear. Green for success, red for error, amber for warning.
- **Anti-patterns:**
  - No washed-out pastels — colors should have conviction
  - No more than 2 gradient directions on a page
  - No light gray on dark background (contrast fail) — use `text-gray-400` minimum

## Spacing

- **Grid:** 8px base, but generous. This aesthetic breathes.
- **Component padding:**
  - Cards: `p-8` (32px) or `p-10` (40px) — more generous than linear-mercury
  - Buttons: `px-6 py-3` (24px / 12px)
  - Hero section: `py-24` to `py-32` (96-128px)
- **Section spacing:**
  - Between sections: `py-20` to `py-32` (80-128px)
  - Between cards: `gap-6` to `gap-8` (24-32px)
  - Between heading and content: `mb-6` (24px)
- **Touch targets:** `min-h-12` (48px) for primary actions
- **Anti-patterns:**
  - No cramped layouts — if it feels tight, add space
  - No section spacing under `py-16`

## Elevation

- **Philosophy:** Layered depth. Shadows, borders, AND background shifts work together.
- **Levels:**
  - Resting cards: `shadow-lg` with subtle border (`border border-white/10` on dark)
  - Hover: `shadow-xl` with scale or glow effect
  - Glass effects: `backdrop-blur-xl bg-white/5 border border-white/10` (glassmorphism, used sparingly)
  - Featured elements: Gradient border or glow ring
- **Anti-patterns:**
  - No flat cards on dark backgrounds (they disappear)
  - No glassmorphism on every element — use it for 1-2 hero elements only

## Motion

- **Philosophy:** Polished and performant. Animations are a premium touch — they communicate quality.
- **Timing:**
  - Hover: `duration-200` with `ease-out`
  - Page entrance: `duration-500` to `duration-700` for staggered reveals
  - Scroll-triggered: `whileInView` with `once: true`
- **Key moments:**
  - Hero load: Staggered text reveal (title → subtitle → CTA, 100ms delay each)
  - Card hover: Subtle scale (`scale-[1.02]`) + shadow increase
  - Section entrance: Fade up from 20px below
  - Gradient animation: Slow color shift on hero backgrounds (15-20s cycle)
- **Anti-patterns:**
  - No janky animations (use `will-change: transform` on animated elements)
  - No motion on every scroll — pick 3-5 key moments per page
  - No animation `duration` over 800ms — it starts feeling slow

## Component Patterns

- **Cards:** `bg-gray-900 border border-white/10 shadow-lg rounded-xl p-8`. On dark backgrounds, borders are essential for definition.
- **Buttons:**
  - Primary: `bg-white text-black hover:bg-gray-100 rounded-full px-6 py-3 font-medium` (inverted on dark)
  - Secondary: `border border-white/20 text-white hover:bg-white/10 rounded-full px-6 py-3`
  - Gradient CTA: `bg-gradient-to-r from-blue-500 to-purple-600 text-white rounded-full`
- **Navigation:** Transparent header with `backdrop-blur-lg`. Minimal items. Logo prominent.
- **Forms:** Dark inputs with `bg-gray-900 border border-gray-700 focus:border-blue-500`. Rounded corners.

## Do / Don't Examples

### Dark Theme
```tsx
// DO: Rich dark backgrounds with proper contrast
<div className="bg-gray-950 text-gray-100">
<p className="text-gray-400">  {/* 4.5:1 contrast on gray-950 */}

// DON'T: Washed out or low contrast
<div className="bg-gray-800 text-gray-500">  {/* Too low contrast */}
```

### Typography
```tsx
// DO: Confident sizing with distinctive fonts
<h1 className="font-display text-6xl font-bold tracking-tight">
<p className="text-lg text-gray-400 leading-relaxed">

// DON'T: Timid sizing
<h1 className="text-2xl font-semibold">  {/* Too small for hero */}
```

### Depth
```tsx
// DO: Layered depth on dark
<div className="bg-gray-900 border border-white/10 shadow-lg rounded-xl p-8">

// DON'T: Flat cards on dark (invisible)
<div className="bg-gray-900 rounded-xl p-8">  {/* No border or shadow */}
```

### Motion
```tsx
// DO: Staggered entrance
<motion.div variants={staggerContainer} initial="hidden" animate="visible">
  <motion.h1 variants={staggerItem}>Title</motion.h1>
  <motion.p variants={staggerItem}>Subtitle</motion.p>
  <motion.div variants={staggerItem}><CTA /></motion.div>
</motion.div>

// DON'T: Everything appears at once
<div>
  <h1>Title</h1>
  <p>Subtitle</p>
  <CTA />
</div>
```
