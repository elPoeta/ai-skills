# HTML Builder — Custom Components Reference

Custom components are used as VNode `tag` values. They are React components rendered
by the builder. Children are NOT rendered unless the component explicitly supports them
(only `DynamicBlock` uses children as a template).

All attr values are strings. Booleans: `"true"` / `"false"`. JSON: stringified.

---

## RouterLink

Internal SPA navigation (React Router). Use instead of `<a>` for all internal links.
For external links or `target="_blank"`, use a plain `<a>` tag.

```json
{
  "id": "oyl12km2",
  "tag": "RouterLink",
  "attrs": {
    "data-to": "/about",
    "data-text": "About",
    "data-text-en": "About",
    "data-text-es": "Acerca de",
    "data-open-new-tab": "false",
    "data-text-color": "#e2e8f0",
    "data-hover-color": "#3b82f6",
    "data-underline": "none",
    "data-font-size": "base",
    "data-font-weight": "medium",
    "data-icon": "none",
    "data-icon-position": "left",
    "data-active-class-name": "text-theme-accent border-b-2 border-theme-accent",
    "class": "transition-colors duration-200"
  },
  "children": []
}
```

### Attr reference

| Attr | Values | Notes |
|------|--------|-------|
| `data-to` | `/path` | Required. Internal path. |
| `data-text` | string | Fallback text if no locale match |
| `data-text-en` | string | English text |
| `data-text-es` | string | Spanish text |
| `data-open-new-tab` | `"true"` `"false"` | Also forces `<a>` for http/https links |
| `data-text-color` | `#hex` | Default text color |
| `data-hover-color` | `#hex` | Text color on hover |
| `data-underline` | `"none"` `"hover"` `"always"` | |
| `data-font-size` | `"xs"` `"sm"` `"base"` `"lg"` `"xl"` | |
| `data-font-weight` | `"light"` `"normal"` `"medium"` `"semibold"` `"bold"` | |
| `data-icon` | `"none"` `"arrow"` `"arrow-left"` `"chevron"` `"chevron-left"` `"home"` `"user"` `"settings"` `"search"` `"heart"` `"star"` `"mail"` `"phone"` `"location"` `"calendar"` `"clock"` `"download"` `"upload"` `"edit"` `"trash"` `"check"` `"close"` `"info"` `"warning"` `"plus"` `"minus"` `"external"` | Lucide icon |
| `data-icon-position` | `"left"` `"right"` | |
| `data-active-class-name` | CSS classes | Applied when route is active |
| `class` | Tailwind classes | Applied to the link wrapper |

---

## LanguageSwitcher

EN/ES language toggle. Add to navbar for bilingual pages.

```json
{
  "id": "1z7h72ui",
  "tag": "LanguageSwitcher",
  "attrs": {
    "data-variant": "compact",
    "data-position": "right",
    "data-show-icon": "false",
    "data-show-labels": "true",
    "data-active-text-color": "#0f172a",
    "data-active-bg-color": "#5c9ef0",
    "data-inactive-text-color": "#a7adb9",
    "data-container-bg-color": "#1e293b",
    "data-border-color": "#0f172a",
    "data-icon-color": "#e2e8f0",
    "class": ""
  },
  "children": []
}
```

### Attr reference

| Attr | Values | Notes |
|------|--------|-------|
| `data-variant` | `"default"` `"compact"` `"minimal"` `"pill"` `"flag"` `"dropdown"` | Visual style |
| `data-position` | `"left"` `"center"` `"right"` | Alignment |
| `data-show-icon` | `"true"` `"false"` | Show globe/flag icon |
| `data-show-labels` | `"true"` `"false"` | Show EN/ES text labels |
| `data-active-text-color` | `#hex` | Text color of active language |
| `data-active-bg-color` | `#hex` | Background of active language pill |
| `data-inactive-text-color` | `#hex` | Text color of inactive option |
| `data-container-bg-color` | `#hex` | Switcher container background |
| `data-border-color` | `#hex` | Border color |
| `data-icon-color` | `#hex` | Icon color |
| `class` | Tailwind | Wrapper classes |

---

## MediaGallery

Paginated responsive grid of media items loaded from a JSON file.
The JSON file must be a project asset.

```json
{
  "id": "mg4x8b2a",
  "tag": "MediaGallery",
  "attrs": {
    "data-json-file": "gallery.json",
    "data-theme": "dark",
    "data-items-per-page": "9",
    "data-grid-cols": "3",
    "data-gap": "4",
    "data-card-radius": "rounded-xl",
    "data-card-shadow": "shadow-md",
    "data-card-hover": "hover:shadow-xl hover:-translate-y-1",
    "data-button-variant": "primary",
    "data-button-dnd-enabled": "false",
    "class": "w-full"
  },
  "children": []
}
```

### Attr reference

| Attr | Values | Notes |
|------|--------|-------|
| `data-json-file` | filename | Asset file name |
| `data-json-data` | JSON string | Inline data (alternative to file) |
| `data-theme` | `"dark"` `"light"` | |
| `data-items-per-page` | number string | Default `"9"` |
| `data-grid-cols` | number string | Default `"3"` |
| `data-gap` | Tailwind gap number | Default `"4"` |
| `data-card-radius` | Tailwind class | e.g. `"rounded-xl"` |
| `data-card-shadow` | Tailwind class | e.g. `"shadow-md"` |
| `data-card-hover` | Tailwind classes | Hover state classes |
| `data-button-variant` | `"primary"` `"secondary"` | Pagination button style |
| `data-button-dnd-enabled` | `"true"` `"false"` | Drag-and-drop reorder |
| `class` | Tailwind | Wrapper classes |

---

## FormBuilder

Fully configurable form. Fields, validation, labels, and submission endpoint
are defined in `data-form-config` as a JSON string.

```json
{
  "id": "fb9k3m7x",
  "tag": "FormBuilder",
  "attrs": {
    "data-form-config": "{\"fields\":[{\"name\":\"name\",\"label\":\"Name\",\"type\":\"text\",\"required\":true},{\"name\":\"email\",\"label\":\"Email\",\"type\":\"email\",\"required\":true},{\"name\":\"message\",\"label\":\"Message\",\"type\":\"textarea\",\"required\":true}],\"submitLabel\":\"Send\",\"action\":\"/api/contact\"}",
    "data-layout": "single",
    "data-spacing": "normal",
    "data-theme": "dark",
    "data-field-radius": "rounded-lg",
    "data-button-radius": "rounded-full",
    "data-label-size": "sm",
    "class": "w-full max-w-2xl mx-auto"
  },
  "children": []
}
```

### Attr reference

| Attr | Values | Notes |
|------|--------|-------|
| `data-form-config` | JSON string | Form definition (see below) |
| `data-layout` | `"single"` `"double"` | Column layout |
| `data-spacing` | `"compact"` `"normal"` `"relaxed"` | Field vertical spacing |
| `data-theme` | `"dark"` `"light"` | |
| `data-field-radius` | Tailwind class | e.g. `"rounded-lg"` |
| `data-button-radius` | Tailwind class | e.g. `"rounded-full"` |
| `data-label-size` | `"sm"` `"base"` | |
| `class` | Tailwind | Wrapper classes |

### form-config schema
```json
{
  "fields": [
    {
      "name": "fieldName",
      "label": "Display Label",
      "type": "text | email | tel | textarea | select | checkbox | radio",
      "required": true,
      "placeholder": "optional",
      "options": ["opt1", "opt2"]
    }
  ],
  "submitLabel": "Send",
  "action": "/api/endpoint",
  "successMessage": "Thanks!"
}
```

---

## DynamicBlock

Renders a template once per item in a data source array.
**Children are the template** — the first child is cloned for each data item.
Use `{{fieldName}}` placeholders in text and attrs (including nested paths like `{{author.name}}`).

```json
{
  "id": "db2y7n1q",
  "tag": "DynamicBlock",
  "attrs": {
    "data-source-type": "static",
    "data-json-file": "articles.json",
    "data-source-path": "items",
    "data-layout": "grid",
    "data-grid-cols": "3",
    "data-gap": "6",
    "class": "w-full"
  },
  "children": [
    {
      "id": "tp3a8c0s",
      "tag": "div",
      "attrs": { "class": "bg-theme-card rounded-xl p-6 border border-theme-primary" },
      "children": [
        {
          "id": "tt4b9d1f",
          "tag": "h3",
          "attrs": { "class": "text-lg font-bold text-theme-primary mb-2" },
          "children": [
            { "id": "tx5c0e2g", "tag": "span", "attrs": { "data-text-node": "true" }, "children": [], "text": "{{title}}" }
          ]
        },
        {
          "id": "td6e1f3h",
          "tag": "p",
          "attrs": { "class": "text-theme-secondary text-sm" },
          "children": [
            { "id": "ts7f2g4i", "tag": "span", "attrs": { "data-text-node": "true" }, "children": [], "text": "{{description}}" }
          ]
        }
      ]
    }
  ]
}
```

### Attr reference

| Attr | Values | Notes |
|------|--------|-------|
| `data-source-type` | `"static"` `"dynamic"` | `static` = JSON file; `dynamic` = globalData |
| `data-json-file` | filename | For `static` type |
| `data-source-path` | dot path | Drill into nested data, e.g. `"response.items"` |
| `data-layout` | `"list"` `"grid"` | |
| `data-grid-cols` | number string | Used when layout is `"grid"` |
| `data-gap` | Tailwind gap number | |
| `class` | Tailwind | Container classes |

---

## Commentary

Comment and review system with moderation, rate limiting, and optional CAPTCHA.

```json
{
  "id": "cm8g3h5j",
  "tag": "Commentary",
  "attrs": {
    "data-mode": "moderated",
    "data-storage-type": "file",
    "data-storage-file": "comments.json",
    "data-theme": "dark",
    "data-min-chars": "20",
    "data-max-chars": "500",
    "data-rate-limit-minutes": "10",
    "data-rate-limit-count": "3",
    "data-require-captcha": "false",
    "data-max-width": "800px",
    "data-border-radius": "rounded-xl",
    "class": "w-full"
  },
  "children": []
}
```

### Attr reference

| Attr | Values | Notes |
|------|--------|-------|
| `data-mode` | `"public"` `"moderated"` | Moderated = admin approval required |
| `data-storage-type` | `"file"` | Storage backend |
| `data-storage-file` | filename | Storage file name |
| `data-method-create` | endpoint | API endpoint for creating comments |
| `data-method-read` | endpoint | API endpoint for reading comments |
| `data-method-update` | endpoint | API endpoint for updating |
| `data-method-delete` | endpoint | API endpoint for deleting |
| `data-require-captcha` | `"true"` `"false"` | |
| `data-forbidden-words` | JSON array string | e.g. `'["spam","hate"]'` |
| `data-admin-emails` | JSON array string | e.g. `'["admin@example.com"]'` |
| `data-admin-name` | string | Display name for admin |
| `data-theme` | `"dark"` `"light"` | |
| `data-min-chars` | number string | Min comment length |
| `data-max-chars` | number string | Max comment length |
| `data-rate-limit-minutes` | number string | Time window for rate limit |
| `data-rate-limit-count` | number string | Max comments per window |
| `data-max-width` | CSS value | e.g. `"800px"` |
| `data-border-radius` | Tailwind class | e.g. `"rounded-xl"` |
| `class` | Tailwind | |

---

## Timeline

Visual bilateral timeline loaded from a JSON asset file.
Items alternate left/right sides with customizable gradient line.

```json
{
  "id": "tl9h4i6k",
  "tag": "Timeline",
  "attrs": {
    "data-json-file": "timeline.json",
    "data-line-gradient-from": "rgb(147, 51, 234)",
    "data-line-gradient-via": "rgb(124, 58, 237)",
    "data-line-gradient-to": "rgb(109, 40, 217)",
    "class": "w-full max-w-5xl mx-auto px-4"
  },
  "children": []
}
```

### Attr reference

| Attr | Values | Notes |
|------|--------|-------|
| `data-json-file` | filename | Timeline data asset |
| `data-json-data` | JSON string | Inline data (alternative) |
| `data-line-gradient-from` | CSS color | Top of the vertical line |
| `data-line-gradient-via` | CSS color | Middle of the line |
| `data-line-gradient-to` | CSS color | Bottom of the line |
| `class` | Tailwind | Wrapper classes |

---

## GoogleLogin

Google OAuth login button. Requires Google OAuth setup in the project.

```json
{
  "id": "gl0i5j7l",
  "tag": "GoogleLogin",
  "attrs": {
    "data-size": "large",
    "data-shape": "pill",
    "data-type": "standard",
    "data-text": "signin_with",
    "data-theme": "filled_blue",
    "data-logo_alignment": "left",
    "data-width": "300",
    "class": "flex justify-center"
  },
  "children": []
}
```

### Attr reference

| Attr | Values | Notes |
|------|--------|-------|
| `data-size` | `"large"` `"medium"` `"small"` | |
| `data-shape` | `"rectangular"` `"pill"` | |
| `data-type` | `"standard"` `"icon"` | `"icon"` shows only the Google logo |
| `data-text` | `"signin_with"` `"signup_with"` | Button label |
| `data-theme` | `"filled_blue"` `"filled_black"` `"outline"` | |
| `data-logo_alignment` | `"left"` `"center"` | |
| `data-width` | number string | Button width in px |
| `class` | Tailwind | Wrapper container classes |

---

## CommunityFootprint

Display community contributions (posts, submissions) with admin approval workflow.

```json
{
  "id": "cf1j6k8m",
  "tag": "CommunityFootprint",
  "attrs": {
    "data-json-file": "footprint.json",
    "data-items-per-page": "6",
    "data-admin-emails": "[\"admin@example.com\"]",
    "data-method-approve": "/api/approve",
    "data-method-delete": "/api/delete",
    "data-accent-color": "#3b82f6",
    "data-accent-hover-color": "#2563eb",
    "data-card-bg-color": "#1e293b",
    "data-border-color": "#334155",
    "data-text-primary-color": "#f1f5f9",
    "data-text-secondary-color": "#94a3b8",
    "data-text-muted-color": "#64748b",
    "class": "w-full"
  },
  "children": []
}
```

### Attr reference

| Attr | Values | Notes |
|------|--------|-------|
| `data-json-file` | filename | Data asset |
| `data-json-data` | JSON string | Inline data |
| `data-items-per-page` | number string | Default `"6"` |
| `data-admin-emails` | JSON array string | Emails with admin access |
| `data-method-approve` | endpoint | Approve endpoint |
| `data-method-delete` | endpoint | Delete endpoint |
| `data-base-path` | JSON | Base path config |
| `data-accent-color` | `#hex` | Primary action color |
| `data-accent-hover-color` | `#hex` | Hover state for accent |
| `data-card-bg-color` | `#hex` | Card background |
| `data-border-color` | `#hex` | Card border |
| `data-text-primary-color` | `#hex` | Main text |
| `data-text-secondary-color` | `#hex` | Secondary text |
| `data-text-muted-color` | `#hex` | Muted/meta text |
| `class` | Tailwind | Wrapper |

---

## Usage Patterns

### Navbar with RouterLink + LanguageSwitcher
```json
{
  "id": "nav00001",
  "tag": "nav",
  "attrs": { "class": "fixed top-0 left-0 right-0 z-50 bg-theme-secondary/90 border-b border-theme-primary backdrop-blur-lg" },
  "children": [
    {
      "id": "nwrap001",
      "tag": "div",
      "attrs": { "class": "max-w-6xl mx-auto px-4 sm:px-6" },
      "children": [
        {
          "id": "nflex001",
          "tag": "div",
          "attrs": { "class": "flex items-center justify-between h-16" },
          "children": [
            { ...logo RouterLink or a tag... },
            { ...desktop nav links (hidden md:flex)... },
            { ...LanguageSwitcher... },
            { ...hamburger button (md:hidden)... }
          ]
        }
      ]
    },
    {
      "id": "mmenu001",
      "tag": "div",
      "attrs": { "id": "mobile-menu", "class": "hidden md:hidden border-t border-theme-primary bg-theme-card" },
      "children": [ ...mobile nav links... ]
    }
  ]
}
```

### Contact section with FormBuilder
```json
{
  "id": "contact1",
  "tag": "section",
  "attrs": { "class": "py-16 px-4 bg-theme-secondary" },
  "children": [
    {
      "id": "ctitle01",
      "tag": "h2",
      "attrs": { "class": "text-3xl font-bold text-center text-theme-primary mb-10" },
      "children": [{ "id": "ctitsp1", "tag": "span", "attrs": {"data-text-node":"true"}, "children": [], "text": "Contact Us" }]
    },
    {
      "id": "fb00001",
      "tag": "FormBuilder",
      "attrs": {
        "data-form-config": "{\"fields\":[{\"name\":\"name\",\"label\":\"Name\",\"type\":\"text\",\"required\":true},{\"name\":\"email\",\"label\":\"Email\",\"type\":\"email\",\"required\":true},{\"name\":\"message\",\"label\":\"Message\",\"type\":\"textarea\",\"required\":true}],\"submitLabel\":\"Send Message\",\"action\":\"/api/contact\"}",
        "data-layout": "single",
        "data-theme": "dark",
        "data-field-radius": "rounded-lg",
        "data-button-radius": "rounded-full",
        "class": "max-w-xl mx-auto"
      },
      "children": []
    }
  ]
}
```

### Article grid with DynamicBlock
```json
{
  "id": "artgrid1",
  "tag": "DynamicBlock",
  "attrs": {
    "data-source-type": "static",
    "data-json-file": "articles.json",
    "data-layout": "grid",
    "data-grid-cols": "3",
    "data-gap": "6",
    "class": "w-full"
  },
  "children": [
    {
      "id": "artcard1",
      "tag": "article",
      "attrs": { "class": "bg-theme-card rounded-xl overflow-hidden border border-theme-primary shadow-md hover:shadow-xl transition-shadow" },
      "children": [
        { "id": "artimg01", "tag": "img", "attrs": { "src": "{{imageUrl}}", "alt": "{{title}}", "class": "w-full h-48 object-cover" }, "children": [] },
        {
          "id": "artbody1",
          "tag": "div",
          "attrs": { "class": "p-6" },
          "children": [
            {
              "id": "arttitl1",
              "tag": "h3",
              "attrs": { "class": "text-lg font-bold text-theme-primary mb-2" },
              "children": [{ "id": "arttsp1", "tag": "span", "attrs": {"data-text-node":"true"}, "children": [], "text": "{{title}}" }]
            },
            {
              "id": "artdesc1",
              "tag": "p",
              "attrs": { "class": "text-theme-secondary text-sm leading-relaxed" },
              "children": [{ "id": "artdsp1", "tag": "span", "attrs": {"data-text-node":"true"}, "children": [], "text": "{{description}}" }]
            }
          ]
        }
      ]
    }
  ]
}
```
