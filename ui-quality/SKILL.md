---
name: ui-quality
description: Use this skill whenever you build, review, or restyle any user interface: web pages, landing pages, dashboards, settings screens, forms, mobile screens (React Native, Expo, SwiftUI), components, or any "make it look good / premium / not AI-looking / polished" request. It defines a seven-part quality system (focal point, spacing scale, typographic hierarchy, restrained color, real content, fine details, purposeful motion) plus a before-and-after workflow and a self-check.
---

# 📌 UI Quality System

## 🎯 GOAL
**One sentence:** Elevate generated and standard interfaces into professional, production-grade products by applying a disciplined seven-part quality system across hierarchy, spacing, typography, color, content, details, and motion.

> Example:
> Transform a generic AI-generated landing page with competing gradients, placeholder copy, and arbitrary spacing into a polished, accessible, token-driven UI with clear focal point and realistic domain content.

---

## 💡 KEY PRINCIPLES

- **Focal Point First** – Exactly one primary element catches the eye first; everything else is subordinate.
- **Spacing as Grouping** – Scale based on 4 px steps (4, 8, 12, 16, 24, 32, 48, 64). Internal group spacing is tighter than external section spacing (ratio ≥ 2:1).
- **Hierarchy from Weight & Color, Not Giant Text** – Use 1–2 font families and at most 3 sizes per card. Drive contrast via font-weight and subtle neutral shades.
- **Neutrals + Single Accent** – Neutral surface and text with one accent color occupying ≤10% of visible area. Avoid decorative gradients.
- **Real Domain Content** – Zero "Lorem ipsum", "John Doe", or "$99" placeholders. Use realistic names, numbers, dates, and action-oriented labels.
- **Micro-Details & Concentricity** – Consistent border radius (nested elements smaller to stay concentric), 1 px subtle borders, and soft elevation shadows.
- **Purposeful Motion** – Animations respond strictly to user actions (150–250 ms, ease-out entry, ease-in exit). Respect `prefers-reduced-motion`.

---

## 📁 WORKFLOW & DIRECTORY INTEGRATION

When implementing UI screens and components, integrate design tokens and structured UI components:

```
src/ (or app/)
├── styles/
│   ├── tokens.css             # CSS variables (colors, radii, spacing)
│   └── theme.ts               # Theme tokens for Tailwind / React Native
├── components/
│   ├── ui/                    # Base primitives (Button, Input, Card)
│   ├── forms/                 # Form controls
│   └── layout/                # Structural containers
└── features/                  # Domain screens using real content & tokens
```

### Pre-Implementation Protocol (Step 0)
Before writing any UI code, specify in comments or initial reply:
1. **One job of this screen:** e.g., "start a booking", "inspect invoice details".
2. **User context:** Desktop analyst, mobile user on the go, first-time visitor.
3. **Real content baseline:** Concrete product name, domain metrics, explicit labels.

---

## 🔧 BEST PRACTICES

### 1. Focal Point & Layout Discipline
- One primary action per view (only filled, accent button). All other actions are secondary (outline/ghost) or subtle links.
- Place primary task actions at top or docked focal areas; subordinate optional settings below.
- Remove before adding: eliminate vanity badges, unneeded stat tickers, and decorative fluff.

### 2. Spacing Scale & Grouping Tokens
- In Tailwind: use default scale (`p-2`, `p-4`, `gap-6`); avoid arbitrary `p-[13px]`.
- In React Native: use explicit tokens (`xs: 4, sm: 8, md: 16, lg: 24, xl: 32`).
- Form labels sit closer to their input (4–8 px) than the gap between fields (20–32 px).

### 3. Typography Rules
- Headings: slightly tighter letter-spacing (`tracking-tight` / `-0.01em` to `-0.02em`).
- Body line-height: ~1.5; heading line-height: 1.1–1.3. Max line length: ~70–75 characters.
- Avoid AI tells: no gradient headlines, no decorative sequence numbers (`01, 02`), no redundant middle dots (`Fast · Secure · Simple`), no forced trailing arrows (`→`).

### 4. Restrained Color & Contrast
- Build an 8–10 step neutral scale (50–950), tinted subtly toward the brand hue.
- Text contrast: ≥ 4.5:1 for body text, ≥ 3:1 for large text and interactive boundaries.
- Dark mode: derive from the neutral palette without raw color inversion; soften accent saturation.

### 5. Details & Elevation
- **Concentric Radii:** Inner element radius equals outer container radius minus padding (`r_inner = r_outer - padding`).
- **Subtle Borders:** 1 px neutral borders slightly distinct from background surface.
- **Elevations:** Soft multi-layer shadows for modals and dropdowns; do not use heavy black dropshadows on flat cards.

---

## ✅ CODE EXAMPLE (Correct)

```tsx
// Example: Polished User Card with strict hierarchy and design tokens
// File: components/features/billing/SubscriptionCard.tsx

import React from 'react';
import { Button } from '@/components/ui/Button';

interface SubscriptionCardProps {
  planName: string;
  priceMonthly: number;
  renewalDate: string;
  seatsUsed: number;
  seatsTotal: number;
  onManagePlan: () => void;
}

export function SubscriptionCard({
  planName = 'Pro Team',
  priceMonthly = 48,
  renewalDate = 'Nov 24, 2026',
  seatsUsed = 8,
  seatsTotal = 10,
  onManagePlan,
}: SubscriptionCardProps) {
  return (
    <div className="rounded-xl border border-neutral-200 bg-white p-6 shadow-sm dark:border-neutral-800 dark:bg-neutral-900">
      {/* Header group: tight spacing */}
      <div className="flex items-baseline justify-between gap-4">
        <div>
          <span className="text-xs font-medium uppercase tracking-wider text-neutral-500">
            Current Plan
          </span>
          <h3 className="mt-1 text-lg font-semibold tracking-tight text-neutral-900 dark:text-neutral-100">
            {planName}
          </h3>
        </div>
        <div className="text-right">
          <span className="text-2xl font-bold text-neutral-900 dark:text-neutral-50">
            ${priceMonthly}
          </span>
          <span className="text-xs text-neutral-500"> / month</span>
        </div>
      </div>

      {/* Progress & metadata group */}
      <div className="mt-6 space-y-2 border-t border-neutral-100 pt-4 dark:border-neutral-800">
        <div className="flex justify-between text-xs text-neutral-600 dark:text-neutral-400">
          <span>Active Seats</span>
          <span className="font-medium text-neutral-900 dark:text-neutral-200">
            {seatsUsed} of {seatsTotal} used
          </span>
        </div>
        <div className="h-2 w-full rounded-full bg-neutral-100 dark:bg-neutral-800">
          <div
            className="h-2 rounded-full bg-blue-600 transition-all duration-300"
            style={{ width: `${(seatsUsed / seatsTotal) * 100}%` }}
          />
        </div>
        <p className="text-xs text-neutral-400">Renews automatically on {renewalDate}</p>
      </div>

      {/* Single primary action */}
      <div className="mt-6">
        <Button variant="primary" fullWidth onClick={onManagePlan}>
          Manage subscription
        </Button>
      </div>
    </div>
  );
}
```

**Why this works:**
- High contrast focal point: clear price and single primary button.
- Strict 4px-based spacing scale (`p-6`, `mt-6`, `space-y-2`).
- Real specific data (Pro Team, $48, Nov 24, 2026, 8 of 10 seats) instead of dummy mocks.
- Concentric, restrained borders and soft elevation.

---

## ❌ ANTI-PATTERN (Common AI-Generated UI Mistakes)

```tsx
// Anti-pattern: Generic AI style with competing accents, gradients, and dummy data
export function BadHeroBanner() {
  return (
    // Problem: heavy arbitrary gradients, glowing blobs
    <div className="bg-gradient-to-r from-purple-600 via-pink-500 to-indigo-600 p-[27px] rounded-[30px] shadow-2xl">
      {/* Problem: pill badge with glowing border, gradient text */}
      <span className="border border-yellow-300 bg-yellow-100 text-yellow-800 text-xs px-3 py-1 rounded-full uppercase tracking-widest">
        ✨ 01 · Revolutionary Platform · Fast · Secure · Simple ✨
      </span>
      
      {/* Problem: gradient text on gradient background, low legibility */}
      <h1 className="text-5xl font-black text-transparent bg-clip-text bg-gradient-to-r from-white to-pink-200 mt-4">
        Supercharge Your <span className="underline italic">Workflow</span> Now!
      </h1>
      
      {/* Problem: generic lorem copy */}
      <p className="text-white text-lg mt-3">
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Feature 1 and Plan Name available for John Doe.
      </p>

      {/* Problem: competing buttons with equal visual weight */}
      <div className="flex gap-4 mt-6">
        <button className="bg-emerald-500 hover:bg-emerald-600 text-white font-bold py-3 px-6 rounded-2xl shadow-lg">
          Get Started Now →
        </button>
        <button className="bg-indigo-500 hover:bg-indigo-600 text-white font-bold py-3 px-6 rounded-2xl shadow-lg">
          Explore Features →
        </button>
      </div>
    </div>
  );
}
```

**Why this fails:**
- No visual focal point: multiple vivid gradients fight for attention.
- Clichéd AI markers: pill badge with middle dots, arbitrary padding (`p-[27px]`), multiple equal primary buttons with arrows.
- Unusable placeholder copy ("Lorem ipsum", "John Doe", "Feature 1").

---

## 🚨 COMMON MISTAKES

1. **Equal Visual Weight** – Placing two vibrant, filled primary buttons side-by-side. Always choose one primary action; make others secondary or text links.
2. **Decorative AI Clichés** – Using gradient text, glowing radial backgrounds, or forced arrows (`→`) without product justification.
3. **Arbitrary Spacing Values** – Mixing arbitrary padding (`p-[13px]`, `gap-[22px]`). Stick strictly to the 4 px token scale.
4. **Placeholder Data in UI Outputs** – Using "Lorem Ipsum", "Jane Doe", "$99", or "Feature Name". Always generate domain-specific realistic content.
5. **Over-Sized Font Scaling** – Relying solely on huge font size increases for contrast rather than font-weight and neutral color shifts.

---

## ✔️ CHECKLIST (Self-Check Before Final Output)

- [ ] **1. Focal point:** Exactly one primary element and one primary action stand out first.
- [ ] **2. Spacing scale:** All margins, paddings, and gaps use the 4 px scale (4, 8, 12, 16, 24, 32, 48, 64).
- [ ] **3. Spacing as grouping:** Internal element gaps are tighter than section gaps (ratio ≥ 2:1).
- [ ] **4. Typography:** At most 2 font families; hierarchy built with weight/color; no arbitrary gradient headlines.
- [ ] **5. Restrained color:** Neutral canvas with accent color occupying ≤ 10% of visible area.
- [ ] **6. Real content:** Concrete names, dates, realistic metrics, and verb-first action buttons (no "Submit").
- [ ] **7. Micro-details:** Radii are consistent and concentric; borders are 1 px neutral; shadows are subtle.
- [ ] **8. Purposeful motion:** Transitions triggered by user action, short (150–250 ms), with ease-out.

---

## 📚 CHEATSHEET (Pattern Quick Reference)

| UI Surface | Primary Rule | Key Styling / Layout Decision |
|---|---|---|
| **Landing Page** | 1 headline, 1 primary CTA | Remove stat bars and gradient titles; real product value prop |
| **Settings / Forms** | Primary action at top/aligned | Label 4–8 px above input; field groups separated by 24–32 px |
| **Dashboard** | 1 headline metric at top | Neutral chart cards; accent color strictly reserved for key metric |
| **Mobile Screen** | Docked primary action | 16 px screen padding; sheets/menus originate from touch target |
| **Toast / Alert** | Minimal, actionable | 1 line specific text, soft elevation, 1 px border, non-blocking |

---

## 🔗 RELATED SKILLS

- ⬆️ [`web-components-patterns`] – Component architecture, compound components, and accessibility in web apps
- ⬆️ [`mobile-components-ui`] – Mobile component patterns, theme tokens, and native UI primitives
- ⬆️ [`web-performance`] – Rendering performance and bundle optimization for web UI
- ⬆️ [`web-project-structure`] – Project layout for styles, design tokens, and components

---

## 📝 NOTES

- Apply this quality system automatically whenever redesigning or refining interfaces, even if the prompt only asks to "make it look modern" or "clean this up".
- Always preserve user-defined brand hues and layout structure unless a complete overhaul is explicitly requested.

---

**Last Updated:** 2026-11-16  
**Version:** 1.0
