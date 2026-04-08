---
name: uiux
description: "Professional UI/UX design skill for React, Next.js, Tailwind CSS, React Native, and Flutter. Use when the user asks to create or polish UI components (modals, forms, tables, charts, navbars, sidebars, cards), design landing pages, build dashboards or admin panels, set up SaaS or mobile app screens, review or fix layout and accessibility issues, configure dark mode or responsive breakpoints, or establish a design system with tokens and component specs. Capabilities include: creating design-system token files and MASTER.md artifacts, generating responsive Tailwind layouts, scaffolding page-level component hierarchies, reviewing and fixing UI accessibility (a11y, WCAG), implementing React Native safe-area screens, and configuring Flutter ThemeData. Outputs design-system files (MASTER, page overrides, tokens, component specs) plus stack-faithful code. Do NOT use for: pure backend-only work with no UI impact, or inventing branding assets you do not have rights to use."
metadata:
  version: "0.1.0"
  tags: ui, ux, design-system, react, nextjs, tailwind, react-native, flutter, accessibility
---

# uiux

UI/UX design and implementation for **React, Next.js, Tailwind CSS, React Native, and Flutter**. When the repo or user targets another stack (e.g. Vue, Svelte, SwiftUI), follow that stack and apply the same UX priorities — see [stacks](./rules/stacks.md).

All normative guidance lives in this directory's **rules** files and `SKILL.md`.

## Non-negotiables

1. **Match stack to reality.** Default examples to the primary-focus stacks when scope is ambiguous; when the codebase or user names another framework, implement in that framework without multi-stack tangents.
2. **Run the priority ladder before pixel-pushing.** Apply categories **1 → 10** from [ux-priority](./rules/ux-priority.md); treat accessibility, touch targets, and performance as blocking for ship-quality work unless the user explicitly accepts risk.
3. **Default to file-first deliverables** for non-trivial UI. Create or update `design-system/MASTER.md` and, when needed, page overrides and component specs per [deliverables](./rules/deliverables.md). Chat-only responses are insufficient when building a new surface or system.
4. **Read the companion rules** before large recommendations: [stacks](./rules/stacks.md), [ux-priority](./rules/ux-priority.md), [design-reference](./rules/design-reference.md), [professional-ui](./rules/professional-ui.md), [deliverables](./rules/deliverables.md).
5. **Never** invent official brand assets, logos, or trademarked design kits. Use placeholders or ask the user for assets.
6. **Never** use emoji as structural icons; use a single vector icon family (see [professional-ui](./rules/professional-ui.md)).
7. **Never** remove focus indicators or rely on hover-only primary actions for essential flows on the web.

## Process

### Phase 1 — Classify and scope

Extract: product type, audience, platforms (web vs native), style keywords, and which stack the edited files use. If unclear, inspect the repo (`package.json`, `pubspec.yaml`, framework configs) per [stacks](./rules/stacks.md). Use [design-reference](./rules/design-reference.md) to align product shape (e.g. dashboard vs landing) with density, motion, and section patterns.

### Phase 2 — Load references

Use **Read** on the rule files listed in non-negotiable 4. Use **Grep/Glob** on the repo for existing tokens, themes, and layout primitives before proposing new patterns.

### Phase 3 — Plan the design system artifact

Following [deliverables](./rules/deliverables.md):

- Write or update **`design-system/MASTER.md`** with tokens, typography roles, motion rules, and component inventory.
- Add **`design-system/pages/<slug>.md`** when a screen legitimately diverges from global rules.
- Add **`design-system/components/<name>.md`** for substantial new components.

Document color, type, and layout decisions using [design-reference](./rules/design-reference.md) so choices are traceable in-repo.

### Phase 4 — Implement with stack guardrails

- **Web (React / Next / Tailwind):** semantic HTML, focus-visible, tokenized color/spacing, Next image/font and layout discipline where applicable.

  ```tsx
  // Example: accessible Tailwind button with design-token classes
  export function PrimaryButton({ children, onClick, disabled }: ButtonProps) {
    return (
      <button
        onClick={onClick}
        disabled={disabled}
        className="inline-flex items-center gap-2 rounded-lg bg-brand-600 px-4 py-2.5
                   text-sm font-medium text-white shadow-sm
                   hover:bg-brand-700 focus-visible:outline focus-visible:outline-2
                   focus-visible:outline-offset-2 focus-visible:outline-brand-600
                   disabled:cursor-not-allowed disabled:opacity-50
                   transition-colors duration-150"
      >
        {children}
      </button>
    );
  }
  ```

- **React Native:** safe areas, touch targets ≥ 48 dp, list performance, accessibility labels.

  ```tsx
  // Example: accessible settings row (48dp minimum touch target)
  import { Pressable, Text, View } from 'react-native';
  import { useSafeAreaInsets } from 'react-native-safe-area-context';

  export function SettingsRow({ label, onPress }: { label: string; onPress: () => void }) {
    return (
      <Pressable
        onPress={onPress}
        accessibilityRole="button"
        accessibilityLabel={label}
        style={({ pressed }) => ({
          minHeight: 48,
          paddingHorizontal: 16,
          backgroundColor: pressed ? '#f3f4f6' : '#ffffff',
          justifyContent: 'center',
        })}
      >
        <Text style={{ fontSize: 16, color: '#111827' }}>{label}</Text>
      </Pressable>
    );
  }
  ```

- **Flutter:** central `ThemeData` / `ColorScheme`, adaptive idioms when relevant.

### Phase 5 — Pre-delivery pass

Run the checklist in [professional-ui](./rules/professional-ui.md) and re-scan **priority 1–3** in [ux-priority](./rules/ux-priority.md).

### Optional — External references

Use **WebSearch** or **WebFetch** for platform guidelines (WCAG, Material, Apple HIG) or library docs when the user asks for citations or unfamiliar APIs. Prefer official docs.

## Integrated example

**User:** "Add a billing settings screen in our Expo app; make it look professional."

1. **Scope:** React Native (matches repo). Read [stacks](./rules/stacks.md), [ux-priority](./rules/ux-priority.md), and [design-reference](./rules/design-reference.md) (dense settings pattern).
2. **Files:** Create `design-system/pages/billing-settings.md`. Update `design-system/MASTER.md` if danger/success tokens or spacing tiers are undefined.
3. **Implementation:** Safe-area padding, 48dp touch rows, labeled inputs with inline error text, loading state on save, confirmation before removing a payment method, `accessibilityLabel` on icon-only overflow actions.
4. **Pass:** Contrast and focus order checked; reduced-motion respected for nonessential transitions; examples stay React Native–faithful.

Exercises non-negotiables **1–3** and **6–7**: stack fidelity, file-first artifacts, native touch/a11y, no emoji icons.

## Anti-patterns

- **Framework soup:** mixing APIs from unrelated stacks when the project uses one stack.
- Long chat responses **without** updating `design-system/*` when building durable UI.
- **Raw hex** or ad-hoc spacing scattered across components when a token system is feasible.
- **Placeholder-only** inputs without persistent labels on web or native.
- **Decorative animation** that ignores reduced-motion or doesn't support comprehension or state.
- Copying **brand logos** or color systems from memory instead of user-supplied assets.
