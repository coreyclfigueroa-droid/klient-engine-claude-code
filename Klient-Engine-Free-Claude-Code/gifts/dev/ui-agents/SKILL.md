---
name: ui-agents
description: Frontend UI design agents from mustafakendiguzel/claude-code-ui-agents. Invokes expert sub-agents for design systems, CSS architecture, React components, accessibility, animation, responsive layouts, and UX research. Use when building or styling any UI.
---

# Frontend UI Design Agents

Source: https://github.com/mustafakendiguzel/claude-code-ui-agents

Seven specialized agents for frontend UI work. Pick the one that matches your task and apply its methodology.

---

## 1. Universal UI Design Methodology (ui-design-expert)

Use for: Any new UI project — creates a complete design system with semantic tokens, color psychology, animation library, and component variants.

```
I need you to create a comprehensive UI/UX design system using the Universal Design Methodology:

PROJECT CONTEXT:
- Project type: [SaaS, e-commerce, portfolio, healthcare, fintech, etc.]
- Target audience: [developers, consumers, professionals, etc.]
- Brand personality: [playful, serious, innovative, traditional, etc.]
- Industry: [technology, healthcare, finance, creative, etc.]

## CORE DESIGN PHILOSOPHY
1. DESIGN SYSTEM FIRST — never write custom styles in components; use semantic tokens exclusively
2. SEMANTIC TOKEN ARCHITECTURE — HSL-based CSS custom properties:
   :root {
     --primary: [hsl values];
     --primary-glow: [lighter variant];
     --accent: [hsl values];
     --gradient-primary: linear-gradient(135deg, hsl(var(--primary)), hsl(var(--accent)));
     --shadow-glow: 0 0 40px hsl(var(--primary) / 0.3);
     --transition-smooth: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
   }

## COLOR SYSTEM
Color psychology:
- Blue: Trust, professionalism, technology
- Purple: Creativity, luxury, innovation
- Green: Growth, success, health
- Orange: Enthusiasm, warmth, energy
- Black/Dark: Premium, sophisticated

Harmony types: Complementary (opposite) | Analogous (adjacent) | Triadic (120° apart) | Monochromatic

## ANIMATION SYSTEM
1. Entrance: fade-in-up (opacity 0→1, translateY 30px→0)
2. Hover: hover-scale (scale-105, 200ms)
3. Ambient: float (translateY 0→-10px→0, infinite)
4. Attention: glow (opacity 1→0.5→1)

## COMPONENT VARIANTS (cva pattern)
const buttonVariants = cva("base-classes", {
  variants: {
    variant: {
      default: "bg-primary text-primary-foreground",
      hero: "bg-gradient-primary hover:shadow-glow hover:scale-105",
      accent: "bg-accent hover:shadow-accent",
      ghost: "hover:bg-accent/10",
    }
  }
})

## SPACING (8px base unit)
gap-2:8px | gap-4:16px | gap-6:24px | gap-8:32px | py-12:48px | py-16:64px | py-24:96px

OUTPUT: semantic token system (index.css), tailwind config, component variants, animation library, responsive strategy, color psychology explanation, quality checklist
```

---

## 2. Design System Generator

Use for: Generating tokens, component specs, and brand guidelines from scratch.

```
Create a comprehensive design system:

BRAND CONTEXT:
- Brand name: [BRAND_NAME]
- Industry: [INDUSTRY_TYPE]
- Brand personality: [TRAITS]
- Target audience: [USERS]

DELIVER:
1. Color system (primary, secondary, neutrals, semantic colors, dark/light modes)
2. Typography (font stack, H1-H6 scale, line-heights, weights)
3. Spacing system (4px/8px grid, margin/padding scale)
4. Component specs (buttons, forms, cards, nav)
5. Accessibility guidelines (contrast ratios, focus indicators)

FORMAT: Design tokens as CSS custom properties + JSON, component docs with examples, implementation guide
```

---

## 3. CSS Architecture Planner

Use for: Structuring CSS for scalability on large projects.

```
Plan a scalable CSS architecture for my project:

PROJECT:
- Scale: [small/medium/large/enterprise]
- Team size: [solo/small/large]
- Framework: [vanilla/React/Vue/etc.]
- Current challenge: [specifics]

DECIDE:
- Naming methodology: BEM | OOCSS | SMACSS | custom
- CSS approach: CSS Modules | CSS-in-JS | Sass/SCSS | PostCSS | Tailwind
- Folder structure, naming conventions, base setup
- Scalability features, performance optimization
- Migration strategy if applicable
```

---

## 4. React Component Generator

Use for: Production-ready React components with TypeScript, accessibility, and tests.

```
Generate a production-ready React component:

COMPONENT SPEC:
- Name: [ComponentName]
- Purpose: [what it does]
- Props: [list props and types]
- States: [loading, error, empty, success]

STANDARDS:
- TypeScript with proper interfaces and JSDoc
- Hooks and functional components only
- Error boundaries
- ARIA labels, semantic HTML, keyboard navigation
- CSS Modules or styled-components
- Hover, focus, active states
- Unit test structure (Jest + RTL)
- Responsive (mobile-first)
```

---

## 5. Micro-Interactions & Animation

Use for: Adding polished motion to existing UI.

```
Design micro-interactions for [COMPONENT/FEATURE]:

CONTEXT:
- Element type: [button/card/nav/form/etc.]
- User action: [click/hover/focus/scroll/load]
- Desired feeling: [snappy/smooth/playful/professional]

DELIVER:
- CSS keyframe animations (transform/opacity only for GPU acceleration)
- Transition timings and easing curves
- prefers-reduced-motion fallbacks
- JavaScript trigger patterns if needed
- Performance budget (< 16ms frame time)
```

---

## 6. Mobile-First Responsive Layout

Use for: Responsive layout strategy and breakpoint systems.

```
Create a mobile-first responsive layout strategy:

PROJECT:
- Content type: [landing/dashboard/docs/e-commerce/etc.]
- Key breakpoints needed: [specify or use defaults: 375/768/1024/1280/1536]
- Complex layout areas: [describe problem sections]

DELIVER:
- CSS Grid and Flexbox patterns for each section
- Container query strategies
- Touch target sizing (44×44px iOS, 48×48px Android)
- Navigation pattern (tab bar / hamburger / sidebar)
- Typography fluid scaling (clamp())
- Image responsive strategy (srcset, aspect-ratio)
```

---

## 7. Accessibility (WCAG) Audit Agent

Use for: Making UI WCAG AA/AAA compliant.

```
Audit and fix accessibility for [COMPONENT/PAGE]:

AUDIT TARGETS:
- Color contrast (WCAG AA: 4.5:1 text, 3:1 UI elements)
- Keyboard navigation and focus order
- ARIA roles, labels, live regions
- Screen reader announcements
- Touch/pointer accessibility
- Motion and animation (prefers-reduced-motion)

DELIVER:
- Issue list with WCAG criterion references
- Code fixes for each issue
- Before/after contrast ratios
- Testing checklist (axe, Lighthouse, manual keyboard test)
```

---

## Quick Reference

| Task | Agent |
|------|-------|
| New project, need design system | Universal UI Design Methodology |
| Brand tokens + component specs | Design System Generator |
| Large codebase CSS organization | CSS Architecture Planner |
| New React component | React Component Generator |
| Add animations/motion | Micro-Interactions |
| Responsive layout problems | Mobile-First Responsive Layout |
| Accessibility fixes | WCAG Audit Agent |
