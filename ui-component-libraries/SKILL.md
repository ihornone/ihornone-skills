---
name: ui-component-libraries
description: Component selection, integration, and restyling strategy for web and mobile (React, Next.js, Expo, React Native). Decides which mature library to use (shadcn/ui, Radix, Sonner, Vaul, cmdk, NumberFlow, Aceternity, Reanimated, gorhom), how to install and restyle components to the product's brand tokens, and how to verify accessibility, keyboard navigation, and motion behavior.
---

# 📌 UI Component Libraries

## 🎯 GOAL
**One sentence:** Select, integrate, and restyle production-ready UI component libraries and headless primitives instead of hand-rolling complex UI behaviors, ensuring perfect accessibility, token consistency, and zero duplicate systems.

> Example:
> Integrate accessible toasts (Sonner), drawers (Vaul), and command palettes (cmdk) into a Next.js / Tailwind application, fully styled with project design tokens rather than default unstyled or raw library themes.

---

## 💡 KEY PRINCIPLES

- **Read Before Choosing** – Inspect `package.json`, existing `components/ui`, and design tokens. Never introduce a second library for an already solved job.
- **Mature Primitives Over Hand-Rolling** – Never hand-build complex behaviors: focus traps, keyboard navigation, drag-to-dismiss sheets, stacking toasts, or command palettes.
- **One Primitive System Per Project** – Stick to one headless foundation (e.g., Radix/shadcn or Base UI or React Aria). Do not mix foundational primitives.
- **Never Ship Library Defaults Unchanged** – Tailor radius, neutral scale, typography, and accent tokens to the product brand before shipping.
- **One Standout Designed Component Per View** – Limit high-impact animated effects (Magic UI, Aceternity) to at most one per page to avoid a chaotic, AI-generated look.
- **Accessibility & Focus Are Inviolable** – Never strip `aria-*`, focus rings, or keyboard handlers to simplify CSS styling.

---

## 📁 COMPONENT LIFECYCLE & INTEGRATION

```
project/
├── components/
│   ├── ui/                    # Copy-in & foundation primitives (shadcn, Radix)
│   │   ├── Button/
│   │   ├── Dialog/
│   │   ├── Command/
│   │   └── Toast/
│   └── feedback/              # Specialists (Sonner Toaster mounting)
├── hooks/
│   └── use-toast.ts
└── styles/
    └── globals.css            # Root tokens (--radius, --primary, neutral scale)
```

### Decision Order Matrix
1. **Existing Component:** Use and extend what is already in `components/ui`.
2. **Specialist Library:** For hard-to-get-right behaviors (Sonner, Vaul, cmdk, NumberFlow).
3. **Foundation Library:** For standard primitives (shadcn/ui, Radix, Base UI).
4. **Copy-In Designed Component:** For single standout moments (Magic UI, Aceternity, Beautiful UI).
5. **Build From Scratch:** Only for trivial static UI (spacers, badges, custom card frames).

---

## 🔧 BEST PRACTICES

### 1. Web Library Selection Reference

| Component Need | Primary Choice | Key Implementation Notes |
|---|---|---|
| **Buttons, Inputs, Dialogs, Menus** | `shadcn/ui` (Radix) | Copy-in CLI: `npx shadcn@latest add <component>`. Edit in place. |
| **Toasts & Notifications** | `Sonner` | Mount `<Toaster />` in root layout; call `toast()` imperatively. |
| **Drawer / Bottom Sheet** | `Vaul` | Built on Radix Dialog; supports drag-to-dismiss and snap points. |
| **Command Palette (Cmd+K)** | `cmdk` | Accessible filtering and keyboard control via shadcn Command. |
| **Animated Numbers / Counters** | `@number-flow/react` | Drop-in numeric ticker without layout jumps. |
| **Complex Tables** | `TanStack Table` | Headless data management; styled with local table primitives. |
| **Charts** | `Recharts` | Declarative; pass project semantic color tokens into series. |
| **Icons** | `lucide-react` | Standardize on 1 icon family, 1 stroke width, unified sizes. |

### 2. React Native / Expo Mapping

| Web Equivalent | React Native / Expo Choice | Architecture Note |
|---|---|---|
| shadcn/ui | `react-native-reusables` or existing project kit | Copy-in primitives adapted for native views. |
| Bottom Sheet / Drawer | `@gorhom/bottom-sheet` | Reanimated + Gesture Handler driven; supports snap points. |
| Toasts | `Sonner Native` or lightweight Reanimated layer | Position to avoid covering bottom tabs and primary CTAs. |
| Motion & Gestures | `react-native-reanimated` | Use worklets and `useNativeDriver` where applicable. |
| Icons | `@expo/vector-icons` or `lucide-react-native` | Match platform aesthetic and unified sizing. |

### 3. Restyling Workflow (From Library Default to Brand)
1. **Configure Theme Tokens:** Update CSS variables / theme object (`--primary`, `--radius`, neutrals).
2. **Adjust Radius & Padding:** Align card/button radii with the project's scale (e.g. 8px controls, 12px cards).
3. **Refine Surfaces & Shadows:** Replace generic heavy shadows with subtle 1px borders and soft elevation.
4. **Trim Unused Variants:** Delete unused component sizes and variants to minimize bundle size.
5. **Inject Real Content:** Replace mock strings and generic placeholder icons with domain data.

---

## ✅ CODE EXAMPLE (Correct: cmdk + Sonner Integration)

```tsx
// Example: Polished Command Palette + Toast Feedback using cmdk and Sonner
// File: components/features/search/QuickActionsModal.tsx
'use client';

import React, { useEffect, useState } from 'react';
import { Command } from 'cmdk';
import { toast } from 'sonner';
import { Laptop, Settings, User, LogOut } from 'lucide-react';

export function QuickActionsModal() {
  const [open, setOpen] = useState(false);

  useEffect(() => {
    const down = (e: KeyboardEvent) => {
      if (e.key === 'k' && (e.metaKey || e.ctrlKey)) {
        e.preventDefault();
        setOpen((prev) => !prev);
      }
    };
    document.addEventListener('keydown', down);
    return () => document.removeEventListener('keydown', down);
  }, []);

  const executeAction = (label: string, action: () => void) => {
    setOpen(false);
    action();
    toast.success(`Action executed: ${label}`);
  };

  if (!open) return null;

  return (
    <div className="fixed inset-0 z-50 flex items-start justify-center bg-black/40 p-4 pt-[15vh] backdrop-blur-xs">
      <Command
        className="w-full max-w-lg overflow-hidden rounded-xl border border-neutral-200 bg-white shadow-2xl dark:border-neutral-800 dark:bg-neutral-900"
        onKeyDown={(e) => e.key === 'Escape' && setOpen(false)}
      >
        <Command.Input
          placeholder="Type a command or search..."
          className="w-full border-b border-neutral-100 px-4 py-3 text-sm outline-none placeholder:text-neutral-400 dark:border-neutral-800 dark:bg-neutral-900"
        />
        <Command.List className="max-h-72 overflow-y-auto p-2">
          <Command.Empty className="p-4 text-center text-xs text-neutral-500">
            No results found.
          </Command.Empty>

          <Command.Group heading="Navigation" className="text-xs text-neutral-400 px-2 py-1">
            <Command.Item
              onSelect={() => executeAction('Profile', () => {})}
              className="flex cursor-pointer items-center gap-2 rounded-lg px-3 py-2 text-sm text-neutral-700 hover:bg-neutral-100 aria-selected:bg-neutral-100 dark:text-neutral-200 dark:hover:bg-neutral-800"
            >
              <User className="h-4 w-4 text-neutral-500" />
              <span>User Profile</span>
            </Command.Item>
            <Command.Item
              onSelect={() => executeAction('Settings', () => {})}
              className="flex cursor-pointer items-center gap-2 rounded-lg px-3 py-2 text-sm text-neutral-700 hover:bg-neutral-100 aria-selected:bg-neutral-100 dark:text-neutral-200 dark:hover:bg-neutral-800"
            >
              <Settings className="h-4 w-4 text-neutral-500" />
              <span>Preferences & Security</span>
            </Command.Item>
          </Command.Group>
        </Command.List>
      </Command>
    </div>
  );
}
```

**Why this works:**
- Uses mature specialist `cmdk` for keyboard navigation, focus trap, and filtering.
- Fully restyled with project's 4px spacing scale and neutral border tokens.
- Native keyboard shortcut (`Cmd+K` / `Ctrl+K`) and clean modal dismiss (`Esc`).

---

## ❌ ANTI-PATTERN (Hand-Rolling Overlays & Over-decorating)

```tsx
// Anti-pattern: Hand-rolled accessible overlay with raw CSS and hardcoded styles
export function BadCustomToastManager() {
  // Problem: reinventing toast queuing, stacking, and animations from scratch
  const [toasts, setToasts] = useState<string[]>([]);

  return (
    // Problem: arbitrary z-index, hardcoded inline positions, no keyboard escape, no focus trapping
    <div style={{ position: 'absolute', top: 20, right: 20, zIndex: 999999 }}>
      {toasts.map((msg, i) => (
        // Problem: non-accessible div, hardcoded hex colors, generic AI styling
        <div
          key={i}
          className="bg-purple-600 text-white p-[14px] rounded-[18px] shadow-2xl flex items-center gap-3"
          onClick={() => setToasts(toasts.filter((_, idx) => idx !== i))}
        >
          <span>✨ Notice: {msg}</span>
          <button style={{ marginLeft: 10 }}>×</button>
        </div>
      ))}
    </div>
  );
}
```

**Why this fails:**
- Ignores mature libraries (`Sonner`): lacks automatic stacking, drag dismissal, and timer pausing on hover.
- Zero accessibility: missing ARIA live regions (`role="status"` / `aria-live="polite"`).
- Hardcoded inline styles and arbitrary border-radii (`p-[14px]`, `rounded-[18px]`).

---

## 🚨 COMMON MISTAKES

1. **Duplicate Libraries** – Installing `react-toastify` when `Sonner` is already configured, or importing Headless UI into a Radix/shadcn codebase.
2. **Shipping Raw shadcn Styles** – Leaving default zinc palette and roundness unchanged so the product looks like a template.
3. **Removing Accessibility to Fix Layout** – Deleting `aria-expanded`, `role="dialog"`, or `focus-visible` rings to bypass styling hurdles.
4. **Too Many Designed Effects** – Combining background beams, 3D tilt cards, and glowing borders on one page. Keep to at most one standout moment.
5. **Ignoring Expo / Native SDK Compatibility** – Installing web-only copy-in components into React Native without verifying native module dependencies.

---

## ✔️ CHECKLIST (Before Shipping Integrated Components)

- [ ] **1. Single system:** No duplicate libraries performing the same task.
- [ ] **2. Project tokens applied:** Component colors, radii, and typography inherit from global tokens.
- [ ] **3. Keyboard navigable:** Tab navigation, Enter/Space activation, and Escape overlay closing work seamlessly.
- [ ] **4. Focus preservation:** Focus correctly shifts into modals/drawers and returns to the trigger on close.
- [ ] **5. Screen reader ready:** Icon buttons have `aria-label`; dialogs have programmatic titles.
- [ ] **6. Motion restraint:** Animations respect `prefers-reduced-motion` and complete within 150–250 ms.
- [ ] **7. Mobile viewport check:** Drawers dismiss properly; notifications do not obscure primary bottom buttons.
- [ ] **8. Clean imports:** No unused component variants or dead sample code left in repository.

---

## 📚 CHEATSHEET

| Component Task | Preferred Tool | Quick Install / Setup Pattern |
|---|---|---|
| Dialog / Modal | `shadcn/ui` (Radix) | `npx shadcn@latest add dialog` |
| Toast Feed | `Sonner` | `npm i sonner` → `<Toaster richColors position="top-right" />` |
| Drawer Sheet | `Vaul` | `npm i vaul` → `<Drawer.Root><Drawer.Content /></Drawer.Root>` |
| Search Palette | `cmdk` | `npm i cmdk` → `<Command><Command.Input /></Command>` |
| Animated Number | `@number-flow/react` | `npm i @number-flow/react` → `<NumberFlow value={price} />` |
| Form Validation | `react-hook-form` + `zod` | `npm i react-hook-form zod @hookform/resolvers` |
| Native Drawer | `@gorhom/bottom-sheet` | `npm i @gorhom/bottom-sheet react-native-reanimated` |

---

## 🔗 RELATED SKILLS

- ⬆️ [`ui-quality`] – The 7-part quality system (spacing, color tokens, typography) used to restyle library components
- ⬆️ [`web-components-patterns`] – Component architecture, compound components, and accessibility in React
- ⬆️ [`mobile-components-ui`] – Mobile component patterns and theme tokens in React Native / Expo
- ⬆️ [`web-project-structure`] – File organization for UI primitives and feature modules

---

## 📝 NOTES

- Always run `npx shadcn@latest add <component>` using the exact syntax supported by the project's installed CLI version.
- When copying designed components from marketplaces (21st.dev, Aceternity), inspect licenses and remove hardcoded styling before committing.

---

**Last Updated:** 2026-11-16  
**Version:** 1.0
