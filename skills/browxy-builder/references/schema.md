# HTML Builder — Schema Reference

## Output Wrapper (always use this exact structure)

```json
{
  "elements": [
    {
      "content": [
        {
          "content": {
            "html": {
              "treeNode": {
                "id": "<uid>",
                "tag": "div",
                "attrs": {
                  "id": "V_NODE_ROOT",
                  "class": "relative min-h-[800px] bg-theme-primary text-theme-primary antialiased"
                },
                "children": []
              },
              "styles": {
                "custom": [
                  {
                    "type": "tag",
                    "key": ":root",
                    "props": { "--bg-cs-primary": "15 23 42" }
                  }
                ]
              },
              "enableEvents": true,
              "dynamicHandlers": {},
              "eventContext": {}
            }
          },
          "id": "<uid>",
          "name": "Html 1",
          "styles": { 
            "Desktop": {"height": "100dvh", "width": "100%", "scrollBehavior": "smooth"},
            "Tablet": {"height": "100dvh", "width": "100%", "scrollBehavior": "smooth"},
            "Mobile": {"height": "100dvh", "width": "100%", "scrollBehavior": "smooth"}
          },
          "animation": "",
          "type": "html",
          "canDelete": true
        }
      ],
      "id": "__body",
      "name": "Body",
      "styles": {
        "Desktop": { "height": "100dvh", "scrollBehavior": "smooth", "overflowX": "none" },
        "Tablet":  { "height": "100dvh", "scrollBehavior": "smooth" },
        "Mobile":  { "height": "100dvh", "scrollBehavior": "smooth" }
      },
      "type": "__body",
      "canDelete": false,
      "index": 1
    }
  ],
  "i18n": {
    "locale": "en",
    "translations": {}
  }
}
```

The page content goes in `treeNode.children`. Everything else in the wrapper is static.

---

## styles.custom — Theme & CSS System

`styles.custom` is an array of style rules applied globally to the page.
It is the primary theming mechanism — define all colors, fonts, and shadows here
so every `theme-*` utility class resolves correctly.

### Rule schema

```typescript
type StyleRule = {
  type: "tag" | "class" | "id";  // selector type
  key: string;                    // selector value (without . or #)
  props: Record<string, string>;  // CSS property → value (kebab-case)
}
```

### Selector types

| `type` | `key` example | Generated CSS selector |
|--------|--------------|----------------------|
| `"tag"` | `":root"` | `:root { ... }` |
| `"tag"` | `"body"` | `body { ... }` |
| `"tag"` | `"*"` | `* { ... }` |
| `"class"` | `"bg-theme-primary"` | `.bg-theme-primary { ... }` |
| `"class"` | `"hover\\:text-theme-accent:hover"` | `.hover\:text-theme-accent:hover { ... }` |
| `"id"` | `"hero-section"` | `#hero-section { ... }` |

> **Escaping** — Tailwind pseudo-class variants in class keys must escape the colon:
> `hover\\:text-theme-accent:hover` (double backslash in JSON → single in CSS)

---

### CSS Variable Naming Convention

All custom CSS variables **must** use the `-cs-` infix to avoid collisions with
Tailwind's own variables and any third-party libraries:

```
--{category}-cs-{name}
```

| Category prefix | Purpose |
|----------------|---------|
| `--bg-cs-*` | Background colors |
| `--text-cs-*` | Text colors |
| `--accent-cs-*` | Accent / brand colors |
| `--border-cs-*` | Border colors |
| `--highlight-cs` | Highlight / warning color |
| `--success-cs` | Success color |
| `--shadow-cs-*` | Shadow color and opacity |

Variable values are **space-separated RGB triplets** (no `rgb()` wrapper):
```
"--bg-cs-primary": "15 23 42"   →   used as rgb(var(--bg-cs-primary))
```
This allows opacity modifiers to work: `rgba(var(--bg-cs-primary), 0.8)`.

---

### Complete styles.custom example (dark theme)

```json
"styles": {
  "custom": [
    {
      "type": "tag",
      "key": ":root",
      "props": {
        "--bg-cs-primary":       "15 23 42",
        "--bg-cs-secondary":     "15 23 42",
        "--bg-cs-tertiary":      "30 41 59",
        "--bg-cs-card":          "15 23 42",
        "--text-cs-primary":     "241 245 249",
        "--text-cs-secondary":   "226 232 240",
        "--text-cs-tertiary":    "203 213 225",
        "--text-cs-muted":       "148 163 184",
        "--text-cs-subtle":      "100 116 139",
        "--accent-cs-primary":   "96 165 250",
        "--accent-cs-hover":     "59 130 246",
        "--accent-cs-bg":        "37 99 235",
        "--border-cs-primary":   "30 41 59",
        "--border-cs-secondary": "51 65 85",
        "--border-cs-accent":    "59 130 246",
        "--highlight-cs":        "251 191 36",
        "--success-cs":          "52 211 153",
        "--shadow-cs-color":     "37 99 235",
        "--shadow-cs-opacity":   "0.1"
      }
    },
    {
      "type": "tag",
      "key": "body",
      "props": {
        "font-family":       "\"Inter\", sans-serif",
        "background-color":  "rgb(var(--bg-cs-primary))",
        "color":             "rgb(var(--text-cs-primary))"
      }
    },
    { "type": "class", "key": "bg-theme-primary",   "props": { "background-color": "rgb(var(--bg-cs-primary))" } },
    { "type": "class", "key": "bg-theme-secondary",  "props": { "background-color": "rgb(var(--bg-cs-secondary))" } },
    { "type": "class", "key": "bg-theme-tertiary",   "props": { "background-color": "rgb(var(--bg-cs-tertiary))" } },
    { "type": "class", "key": "bg-theme-card",       "props": { "background-color": "rgb(var(--bg-cs-card))" } },
    { "type": "class", "key": "bg-theme-accent",     "props": { "background-color": "rgb(var(--accent-cs-bg))" } },
    { "type": "class", "key": "text-theme-primary",  "props": { "color": "rgb(var(--text-cs-primary))" } },
    { "type": "class", "key": "text-theme-secondary","props": { "color": "rgb(var(--text-cs-secondary))" } },
    { "type": "class", "key": "text-theme-tertiary", "props": { "color": "rgb(var(--text-cs-tertiary))" } },
    { "type": "class", "key": "text-theme-muted",    "props": { "color": "rgb(var(--text-cs-muted))" } },
    { "type": "class", "key": "text-theme-subtle",   "props": { "color": "rgb(var(--text-cs-subtle))" } },
    { "type": "class", "key": "text-theme-accent",   "props": { "color": "rgb(var(--accent-cs-primary))" } },
    { "type": "class", "key": "border-theme-primary",  "props": { "border-color": "rgb(var(--border-cs-primary))" } },
    { "type": "class", "key": "border-theme-secondary","props": { "border-color": "rgb(var(--border-cs-secondary))" } },
    { "type": "class", "key": "border-theme-accent",   "props": { "border-color": "rgb(var(--border-cs-accent))" } },
    { "type": "class", "key": "border-theme-highlight-cs", "props": { "border-color": "rgb(var(--highlight-cs))" } },
    { "type": "class", "key": "bg-theme-success-cs", "props": { "background-color": "rgb(var(--success-cs))" } },
    {
      "type": "class",
      "key": "hover\\:text-theme-accent:hover",
      "props": { "color": "rgb(var(--accent-cs-primary))" }
    },
    {
      "type": "class",
      "key": "hover\\:bg-theme-tertiary:hover",
      "props": { "background-color": "rgb(var(--bg-cs-tertiary))" }
    },
    {
      "type": "class",
      "key": "hover\\:border-theme-accent:hover",
      "props": { "border-color": "rgb(var(--border-cs-accent))" }
    },
    {
      "type": "class",
      "key": "hover\\:bg-theme-accent-cs-hover:hover",
      "props": { "background-color": "rgb(var(--accent-cs-hover))" }
    },
    {
      "type": "class",
      "key": "shadow-theme-glow",
      "props": {
        "box-shadow": "0 10px 15px -3px rgba(var(--shadow-cs-color), var(--shadow-cs-opacity)), 0 4px 6px -4px rgba(var(--shadow-cs-color), calc(var(--shadow-cs-opacity) * 0.5))"
      }
    },
    {
      "type": "class",
      "key": "shadow-theme-xl",
      "props": {
        "box-shadow": "0 20px 25px -5px rgba(var(--shadow-cs-color), var(--shadow-cs-opacity)), 0 8px 10px -6px rgba(var(--shadow-cs-color), calc(var(--shadow-cs-opacity) * 0.5))"
      }
    }
  ]
}
```

---

### Light theme variant (`:root` block only — class mappings stay the same)

```json
{
  "type": "tag",
  "key": ":root",
  "props": {
    "--bg-cs-primary":       "255 255 255",
    "--bg-cs-secondary":     "248 250 252",
    "--bg-cs-tertiary":      "241 245 249",
    "--bg-cs-card":          "255 255 255",
    "--text-cs-primary":     "15 23 42",
    "--text-cs-secondary":   "30 41 59",
    "--text-cs-tertiary":    "51 65 85",
    "--text-cs-muted":       "100 116 139",
    "--text-cs-subtle":      "148 163 184",
    "--accent-cs-primary":   "37 99 235",
    "--accent-cs-hover":     "29 78 216",
    "--accent-cs-bg":        "37 99 235",
    "--border-cs-primary":   "226 232 240",
    "--border-cs-secondary": "203 213 225",
    "--border-cs-accent":    "37 99 235",
    "--highlight-cs":        "234 179 8",
    "--success-cs":          "22 163 74",
    "--shadow-cs-color":     "15 23 42",
    "--shadow-cs-opacity":   "0.08"
  }
}
```

---

### Per-element custom styles (node `styles` prop)

In addition to the global `styles.custom` array, individual VNodes accept
an inline `styles` object (React.CSSProperties — camelCase):

```json
{
  "id": "hero001a",
  "tag": "section",
  "attrs": { "class": "py-24 px-4" },
  "styles": {
    "background": "linear-gradient(135deg, rgb(var(--bg-cs-secondary)), rgb(var(--bg-cs-tertiary)))",
  },
  "children": []
}
```

Use the `styles` node prop for:

- backgroundImage
- background-Position
- backgroundSize
- backgroundRepeat
- backgroundAttachment

Use `styles.custom` for complex styles
- Gradients and complex backgrounds
- Precise borders that Tailwind classes can't express
- Animation keyframe references (`animation: "fadeIn 0.4s ease"`)
- CSS var expressions that need `calc()` or `rgba()`

Use Tailwind classes for everything else.

---

### Adding custom utility classes

You can define any additional Tailwind-like class in `styles.custom`:

```json
{ "type": "class", "key": "text-theme-gradient",
  "props": {
    "background": "linear-gradient(90deg, rgb(var(--accent-cs-primary)), rgb(var(--highlight-cs)))",
    "background-clip": "text",
    "-webkit-background-clip": "text",
    "-webkit-text-fill-color": "transparent"
  }
},
{ "type": "class", "key": "glass-card",
  "props": {
    "background": "rgba(var(--bg-cs-card), 0.6)",
    "backdrop-filter": "blur(12px)",
    "-webkit-backdrop-filter": "blur(12px)",
    "border": "1px solid rgba(var(--border-cs-primary), 0.5)"
  }
},
{ "type": "id", "key": "hero-section",
  "props": {
    "scroll-margin-top": "64px"
  }
}
```

Then use them directly in node `attrs.class`: `"class": "glass-card text-theme-gradient"`.

---

### Rules for generating styles.custom

1. **Always include `:root`** as the first rule with all `-cs-` vars defined
2. **Always include `body`** as the second rule — sets font and base colors
3. **Always map all `theme-*` classes** — every `bg-theme-*`, `text-theme-*`, `border-theme-*` used in nodes must have a corresponding class rule
4. **Also map hover variants** for any hover class used: `hover\\:text-theme-accent:hover`, `hover\\:bg-theme-tertiary:hover`, etc.
5. **Include shadow utilities** `shadow-theme-glow` and `shadow-theme-xl` using `rgba(var(--shadow-cs-color), ...)`
6. **Values are RGB triplets** — never `#hex` or `rgb(...)` directly in `:root` props
7. **Props use kebab-case** CSS property names — not camelCase (`background-color` not `backgroundColor`)
8. **Escape colons in class keys** — `hover\\:` in JSON becomes `hover\:` in CSS
9. **Custom classes go after** all standard theme mappings in the array



```typescript
type VNode = {
  id: string;                        // unique 8-char alphanumeric — REQUIRED on every node
  tag: string;                       // HTML tag or custom component name
  attrs: Record<string, string>;     // all attrs as strings; use "class" not "className"
  children: VNode[];                 // always an array, never null or missing
  text?: string;                     // ONLY on leaf text nodes (no children)
  styles?: React.CSSProperties;      // optional inline styles object (not a string)
  eventHandlers?: Record<string, string>; // rarely used, prefer dynamicHandlers
}
```

---

## Critical Rules

### IDs
- Every single node must have a unique `id`
- Format: 8 lowercase alphanumeric chars, e.g. `"a3bx9c1d"`, `"mrllun8y"`, `"4esnt3hd"`
- Generate randomly — do not reuse ids between nodes or between pages
- IDs referenced in `dynamicHandlers` must match the element's `attrs.id` (not `node.id`)

### Text content
Text never goes directly on a structural node. Always use a child span:
```json
{
  "id": "ysip8ske",
  "tag": "span",
  "attrs": { "data-text-node": "true" },
  "children": [],
  "text": "Hello world"
}
```
The `data-text-node="true"` attr marks it as a text node for the editor.

### Children
- `children` is always an array — even empty: `"children": []`
- Void tags (`img`, `input`, `br`, `hr`, `link`, `meta`, `area`, `base`, `col`, `embed`, `param`, `source`, `track`, `wbr`) may have children in the JSON but they won't be rendered

### Attrs
- Use `"class"` not `"className"` — the renderer maps it
- All attr values are strings, including booleans: `"data-open-new-tab": "true"`
- SVG attrs use camelCase in the renderer: `stroke-linecap` → handled automatically
- `id` in attrs is the HTML element id (used by dynamicHandlers); separate from node `id`

### Styles
`styles` is a React.CSSProperties object (camelCase), not a CSS string:
```json
"styles": { "background": "linear-gradient(135deg, #667eea, #764ba2)" }
```

---

## dynamicHandlers

Attach JavaScript to DOM elements by their HTML `id` attr:

```json
"dynamicHandlers": {
  "menu-toggle": {
    "click": "const m=document.getElementById('mobile-menu');m.classList.toggle('hidden');"
  },
  "hero-cta": {
    "click": "document.getElementById('features').scrollIntoView({behavior:'smooth'});"
  },
  "contact-form": {
    "submit": "event.preventDefault(); alert('Sent!');"
  }
}
```

Available in handler scope: `document`, `window`, `event`, `element` (the element itself).
Never use `console.log` — use `console.error` if logging is needed.

Supported event types: `click`, `mouseover`, `mouseout`, `focus`, `blur`,
`keydown`, `keyup`, `input`, `change`, `submit`, `load`

---

## i18n / Translations

For bilingual (EN/ES) pages, add node translations keyed by node `id`:

```json
"i18n": {
  "locale": "en",
  "translations": {
    "ysip8ske": { "en": "Hello", "es": "Hola" },
    "4esnt3hd": { "en": "About us", "es": "Sobre nosotros" }
  }
}
```

The renderer swaps text based on active locale. For `RouterLink` use
`data-text-en` / `data-text-es` attrs directly instead.

---

## Tailwind v3 Reference

### Layout & positioning
```
flex inline-flex grid block inline-block hidden
relative absolute fixed sticky
top-0 left-0 right-0 bottom-0 inset-0
z-10 z-20 z-50
overflow-hidden overflow-auto overflow-x-hidden
```

### Sizing
```
w-full w-screen w-1/2 w-1/3 w-auto
h-full h-screen h-16 h-48 h-64 h-auto
max-w-xs max-w-sm max-w-md max-w-lg max-w-xl max-w-2xl max-w-4xl max-w-6xl max-w-7xl
min-h-[800px] min-h-screen
```

### Spacing
```
p-2 p-4 p-6 p-8 p-12    px-4 px-6 px-8    py-3 py-4 py-6 py-8 py-12 py-16 py-20 py-24
m-auto mx-auto           gap-2 gap-4 gap-6 gap-8
space-x-2 space-x-4 space-x-8    space-y-2 space-y-4 space-y-6
```

### Typography
```
text-xs text-sm text-base text-lg text-xl text-2xl text-3xl text-4xl text-5xl text-6xl
font-light font-normal font-medium font-semibold font-bold font-extrabold
leading-tight leading-snug leading-normal leading-relaxed leading-loose
tracking-tight tracking-normal tracking-wide tracking-wider tracking-widest
text-left text-center text-right
uppercase lowercase capitalize
truncate line-clamp-2 line-clamp-3
```

### Borders & effects
```
border border-2 border-b border-t border-l border-r
rounded rounded-sm rounded-md rounded-lg rounded-xl rounded-2xl rounded-full rounded-none
shadow shadow-md shadow-lg shadow-xl shadow-2xl
opacity-0 opacity-50 opacity-80 opacity-100
backdrop-blur-sm backdrop-blur backdrop-blur-lg backdrop-blur-xl
```

### Flexbox & Grid
```
flex-row flex-col flex-wrap flex-nowrap
items-start items-center items-end items-stretch
justify-start justify-center justify-end justify-between justify-around
flex-1 flex-none flex-shrink-0 flex-grow
grid-cols-1 grid-cols-2 grid-cols-3 grid-cols-4 grid-cols-6 grid-cols-12
col-span-2 col-span-3 col-span-full
```

### Transitions & interactions
```
transition transition-all transition-colors transition-opacity transition-transform
duration-100 duration-200 duration-300 duration-500
ease-in ease-out ease-in-out
hover:opacity-90 hover:bg-theme-tertiary hover:text-theme-accent hover:shadow-xl
hover:scale-105 hover:-translate-y-1
focus:outline-none focus:ring-2 focus:ring-theme-accent
cursor-pointer cursor-default
```

### Responsive breakpoints (mobile-first)
```
sm:  ≥640px
md:  ≥768px
lg:  ≥1024px
xl:  ≥1280px
2xl: ≥1536px

Examples:
  hidden md:flex              (hide on mobile, flex on desktop)
  grid-cols-1 md:grid-cols-3  (1 col mobile, 3 cols desktop)
  text-3xl md:text-5xl        (smaller on mobile)
  px-4 md:px-8                (less padding on mobile)
```

### Arbitrary values
```
min-h-[800px]   w-[320px]   h-[2px]   top-[60px]
text-[#hex]     bg-[#hex]   border-[#hex]   (use theme vars instead when possible)
grid-template-columns: use CSS var pattern with style prop for dynamic cols
```

---

## Theme CSS Variables (full list)

These map to the page's active theme. Always prefer these over hardcoded colors.

| Class | Usage |
|-------|-------|
| `bg-theme-primary` / `text-theme-primary` | Main page background / main text |
| `bg-theme-secondary` / `text-theme-secondary` | Secondary bg (navbar, cards alt) / secondary text |
| `bg-theme-tertiary` | Tertiary bg (hover states, subtle fills) |
| `bg-theme-card` | Card component background |
| `text-theme-accent` / `border-theme-accent` / `bg-theme-accent` | Brand/accent color |
| `text-theme-muted` | Subdued/hint text |
| `border-theme-primary` | Main border color |
| `border-theme-secondary` | Secondary border |

Opacity modifiers: `bg-theme-secondary/90`, `bg-theme-card/50`, `border-theme-primary/30`

---

## Complete Minimal Example

```json
{
  "elements": [{
    "content": [{
      "content": {
        "html": {
          "treeNode": {
            "id": "fwb7e6x1",
            "tag": "div",
            "attrs": { "id": "V_NODE_ROOT", "class": "relative min-h-[800px] bg-theme-primary text-theme-primary antialiased" },
            "children": [
              {
                "id": "a1b2c3d4",
                "tag": "section",
                "attrs": { "class": "flex items-center justify-center min-h-screen text-center px-4" },
                "children": [
                  {
                    "id": "e5f6g7h8",
                    "tag": "div",
                    "attrs": { "class": "max-w-2xl mx-auto" },
                    "children": [
                      {
                        "id": "i9j0k1l2",
                        "tag": "h1",
                        "attrs": { "class": "text-5xl font-bold text-theme-primary mb-4" },
                        "children": [
                          {
                            "id": "m3n4o5p6",
                            "tag": "span",
                            "attrs": { "data-text-node": "true" },
                            "children": [],
                            "text": "Hello World"
                          }
                        ]
                      }
                    ]
                  }
                ]
              }
            ]
          },
          "styles": { "custom": [{}] },
          "enableEvents": true,
          "dynamicHandlers": {},
          "eventContext": {}
        }
      },
      "id": "3d51af2c",
      "name": "Html 1",
      "styles": { 
        "Desktop": {"height": "100dvh", "width": "100%", "scrollBehavior": "smooth"},
        "Tablet": {"height": "100dvh", "width": "100%", "scrollBehavior": "smooth"},
        "Mobile": {"height": "100dvh", "width": "100%", "scrollBehavior": "smooth"}   
      },
      "animation": "",
      "type": "html",
      "canDelete": true
    }],
    "id": "__body",
    "name": "Body",
    "styles": {
      "Desktop": { "height": "100dvh", "scrollBehavior": "smooth", "overflowX": "none" },
      "Tablet":  { "height": "100dvh", "scrollBehavior": "smooth" },
      "Mobile":  { "height": "100dvh", "scrollBehavior": "smooth" }
    },
    "type": "__body",
    "canDelete": false,
    "index": 1
  }],
  "i18n": { "locale": "en", "translations": {} }
}
```