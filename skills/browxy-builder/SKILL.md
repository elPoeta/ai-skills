---
name: browxy-builder
description: >
  Generate and edit JSON pages for a custom HTML Builder system from natural language.
  Use this skill whenever the user asks to create a web page, landing page, portfolio,
  blog, contact page, about page, dashboard, 404 page, or any other webpage type —
  even if they don't say "HTML Builder" explicitly. Also use it when the user asks to
  add sections, edit existing pages, change styles, add components, or modify any page
  JSON for this builder. The output is always a complete JSON file written to disk,
  ready to paste or load directly into the HTML Builder.
---

# HTML Builder Skill

Generate and edit complete web pages as JSON for a custom HTML Builder system.
The builder renders a tree of VNodes (virtual DOM nodes) with Tailwind v3 classes,
theme CSS variables, and a set of custom React components.

## Workflow

### generate_page
1. Read `references/schema.md` — VNode structure, wrapper format, **styles.custom system**, rules
2. Read `references/components.md` — all custom components and their attrs
3. Plan the page: sections, layout, components, color scheme (dark or light)
4. Generate `styles.custom` first — `:root` vars + `body` + all `theme-*` class mappings used
5. Generate the full VNode tree in `treeNode.children`
6. Validate: every node has a unique id, no orphan text, children always array, all theme classes have a matching rule in `styles.custom`
7. Write to the requested output path (create dirs if needed)
8. Report: file path + node count + custom components used

### edit_page
1. Read the existing JSON file from disk
2. Read `references/schema.md` and `references/components.md`
3. Apply the requested changes to the full tree
4. Return the **complete** updated JSON (never partial)
5. Overwrite original or write to new path as instructed
6. Report what changed

### General rules
- Output is always a file written to disk — never paste JSON in chat
- Always generate **complete, production-ready** pages (not placeholders or wireframes)
- Use theme CSS variables (`bg-theme-primary`, `text-theme-accent`, etc.) — never hardcode colors
- Every node needs a unique 8-char alphanumeric id — generate them randomly
- Responsive by default: mobile-first with `sm:`, `md:`, `lg:` breakpoints
- Navbar always sticky/fixed with `backdrop-blur`; mobile hamburger with dynamic handler
- Never use `console.log` in event handler code — use `console.error` if needed

## Quick Reference

### Page templates
| Request | Sections |
|---------|----------|
| landing page | navbar + hero + features(3 cards) + CTA + footer |
| portfolio | navbar + hero + projects grid + about + contact(FormBuilder) + footer |
| blog | navbar + hero + articles(DynamicBlock or MediaGallery) + footer |
| about | navbar + hero bio + story sections + team cards + CTA + footer |
| contact | navbar + hero + FormBuilder + info cards + footer |
| 404 | full-height centered — error code + message + RouterLink home |

### Most common patterns
```
Navbar:    nav.fixed.top-0.z-50.bg-theme-secondary/90.backdrop-blur-lg
             > div.max-w-6xl.mx-auto.px-4 > div.flex.items-center.justify-between.h-16

Hero:      section.py-24.px-4 > div.max-w-5xl.mx-auto.text-center

Card:      div.bg-theme-card.rounded-xl.p-6.border.border-theme-primary.shadow-md

Grid:      div.grid.grid-cols-1.sm:grid-cols-2.lg:grid-cols-3.gap-6

Btn/CTA:   button.px-6.py-3.bg-theme-accent.text-white.rounded-full.font-semibold
                  .hover:opacity-90.transition-all.duration-200
```

### Mobile hamburger (required in all navbars)
Button id `menu-toggle` + hidden div id `mobile-menu`:
```js
// dynamicHandlers["menu-toggle"]["click"]
"const m=document.getElementById('mobile-menu');m.classList.toggle('hidden');"
```

### Custom components (quick list)
`RouterLink` `MediaGallery` `FormBuilder` `DynamicBlock`
`Commentary` `Timeline` `GoogleLogin` `CommunityFootprint` `LanguageSwitcher`
→ Full attrs and usage in `references/components.md`

### Theme variables (quick list)
```
bg/text-theme-primary   bg/text-theme-secondary   bg-theme-tertiary
bg-theme-card           text/border-theme-accent   text-theme-muted
text-theme-subtle       border-theme-primary       border-theme-secondary
border-theme-highlight-cs  bg-theme-success-cs
shadow-theme-glow       shadow-theme-xl
```
Hover variants: `hover:text-theme-accent`, `hover:bg-theme-tertiary`, `hover:border-theme-accent`

> **All theme classes must be defined in `styles.custom`** — they are not Tailwind builtins.
> The `:root` block defines `-cs-` vars; class rules map them to `theme-*` utilities.
> See `references/schema.md` → *styles.custom* for the full system and both dark/light presets.

## Reference Files

Read these files before generating any page:

| File | When to read |
|------|-------------|
| `references/schema.md` | Always — VNode schema, wrapper, **styles.custom system** (vars, types, presets), all structural rules |
| `references/components.md` | Always — every custom component with full attr reference and usage patterns |

Both files must be read before writing any JSON. They contain critical constraints
that are not repeated here to keep this file short.
