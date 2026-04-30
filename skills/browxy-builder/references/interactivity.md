# HTML Builder — Interactivity Reference
## dynamicHandlers & eventContext

---

## Architecture Overview

The builder has two separate systems that work together to make pages interactive:

```
eventContext        →   shared state & helper objects (defined once, reused everywhere)
dynamicHandlers     →   event listeners attached to DOM elements by HTML id
```

Both live at the root of the `html` object, alongside `treeNode` and `styles`:

```json
{
  "treeNode": { ... },
  "styles": { ... },
  "enableEvents": true,
  "dynamicHandlers": { ... },
  "eventContext": { ... }
}
```

`enableEvents: true` must be set for handlers to execute. In edit mode they are
disabled — they only run in preview/live mode.

---

## dynamicHandlers

### Structure

```json
"dynamicHandlers": {
  "<element-html-id>": {
    "<eventType>": "<javascript code string>"
  }
}
```

- The outer key is the **HTML `id` attribute** of the target element (`attrs.id` in the VNode),
  **not** the VNode's own `id` field.
- The inner key is the event type (see supported events below).
- The value is a **raw JavaScript string** — executed in a sandboxed context.

### Supported event types

| Event | Trigger |
|-------|---------|
| `click` | Mouse click / tap |
| `input` | Input value changes (each keystroke) |
| `change` | Input value committed (blur / select) |
| `focus` | Element receives focus |
| `blur` | Element loses focus |
| `keydown` | Key pressed down |
| `keyup` | Key released |
| `submit` | Form submission |
| `mouseover` | Mouse enters element |
| `mouseout` | Mouse leaves element |
| `load` | Element loaded (runs once on mount in preview) |

### Execution context — always available in handler code

| Variable | Type | Description |
|----------|------|-------------|
| `event` | `Event` | The native DOM event |
| `element` | `HTMLElement` | The element that triggered the event |
| `document` | `Document` | The page document |
| `window` | `Window` | The browser window (sandboxed — use `utils.*` instead of `window.*`) |
| `utils` | object | Built-in utility functions (notifications, navigation, scroll, storage) |
| `helpers` | object | Built-in DOM & data helpers (classes, content, visibility, forms) |
| `modal` | object | Built-in modal/dialog system |
| `api` | object | Built-in HTTP fetch helpers (GET, POST, PUT, PATCH, DELETE) |
| `service` | object | Built-in compiler service integration |

Plus any variables defined in `eventContext` (see below).

> **Important:** `window` is sandboxed — direct calls like `window.location.href` or
> `window.open()` are blocked. Always use the built-in helpers instead:
> `utils.redirect()`, `utils.openInNewTab()`, `utils.scrollTo()`, etc.
> `localStorage` is also blocked — use `utils.toLocalStorage()` / `utils.fromLocalStorage()`.
> Direct `fetch()` is blocked — use `api.get()`, `api.post()`, etc.

---

## Built-in Event Context

These five objects are **always injected** into every handler automatically.
No `eventContext` entry needed. They cover the vast majority of interactive needs.

---

### `utils` — Core utilities

#### Notifications

| Call | Description |
|------|-------------|
| `utils.toast.success(msg, duration?)` | Green success toast |
| `utils.toast.error(msg, duration?)` | Red error toast |
| `utils.toast.warning(msg, duration?)` | Yellow warning toast |
| `utils.toast.info(msg, duration?)` | Blue info toast |
| `utils.showToast(msg, type?, duration?)` | Generic toast. `type`: `"info"` `"success"` `"warning"` `"error"`. Default 4000 ms |
| `utils.notify(msg)` | Browser `alert()` — avoid in production |

```js
utils.toast.success('Guardado correctamente', 3000);
utils.toast.error('Error al enviar');
utils.showToast('Procesando...', 'info', 2000);
```

#### Navigation

| Call | Description |
|------|-------------|
| `utils.redirect(url)` | Navigate to URL |
| `utils.openInNewTab(url)` | Open URL in new tab (noopener, noreferrer) |
| `utils.reload()` | Reload the page |

```js
utils.redirect('/dashboard');
utils.openInNewTab('https://docs.example.com');
```

#### Scrolling & timing

| Call | Description |
|------|-------------|
| `utils.scrollTo(selector)` | Smooth scroll to any CSS selector |
| `utils.scrollToTop()` | Smooth scroll to top |
| `utils.wait({ fn, ms })` | Delay a function call by `ms` milliseconds |

```js
utils.scrollTo('#features');
utils.scrollToTop();
utils.wait({ fn: () => helpers.hide('#overlay'), ms: 500 });
```

#### Page & user info

| Call | Description |
|------|-------------|
| `utils.getUser()` | Returns `{ id, email, isAdmin }` or `null` |
| `utils.getLocaleLang()` | Returns `"en"` or `"es"` |
| `utils.getAlias()` | Returns current page alias |
| `utils.getProjectId()` | Returns project ID |
| `utils.getAliasShareKey` | Share key constant |
| `utils.getJsonEncoded(jsonStr)` | Encode JSON string for URL usage |

```js
const user = utils.getUser();
if (!user) { utils.redirect('/login'); }
else { helpers.setText('#greeting', 'Hola, ' + user.email); }

const lang = utils.getLocaleLang(); // 'en' | 'es'
```

#### Local storage (builder-namespaced)

| Call | Description |
|------|-------------|
| `utils.toLocalStorage(key, value)` | Save to `localStorage` under `builder-{key}` |
| `utils.fromLocalStorage(key)` | Read from `localStorage` under `builder-{key}` |

```js
utils.toLocalStorage('step', '2');
const step = utils.fromLocalStorage('step'); // '2'
```

---

### `helpers` — DOM & data manipulation

#### Class manipulation

| Call | Description |
|------|-------------|
| `helpers.toggleClass(el, className)` | Toggle a class. `el`: selector string or HTMLElement |
| `helpers.addClass(el, className)` | Add a class |
| `helpers.removeClass(el, className)` | Remove a class |
| `helpers.setClasses(el, toAdd[], toRemove[])` | Add and remove classes atomically |

```js
helpers.toggleClass('#mobile-menu', 'hidden');
helpers.toggleClass(element, 'active');
helpers.setClasses('#btn', ['opacity-50', 'cursor-not-allowed'], ['bg-theme-accent']);
helpers.addClass('#nav', 'scrolled');
```

#### Content

| Call | Description |
|------|-------------|
| `helpers.setText(el, text)` | Set `textContent` |
| `helpers.setHTML(el, html)` | Set `innerHTML` |
| `helpers.appendHTML(el, html)` | Inject HTML at end of element |
| `helpers.prependHTML(el, html)` | Inject HTML at start of element |
| `helpers.clearContent(el)` | Empty text content |
| `helpers.clearFileInput(input)` | Reset a `<input type="file">` |

```js
helpers.setText('#count-display', String(count));
helpers.setHTML('#results', items.map(i => '<li>'+i.name+'</li>').join(''));
helpers.appendHTML('#log', '<p>Step completed</p>');
helpers.clearContent('#error-msg');
```

#### Visibility

| Call | Description |
|------|-------------|
| `helpers.hide(selector)` | Set `display: none` |
| `helpers.show(selector, displayType?)` | Set `display: block` (or custom type) |
| `helpers.toggle(selector)` | Toggle between `none` / `block` |

```js
helpers.hide('#loading-spinner');
helpers.show('#results', 'flex');
helpers.toggle('#dropdown');
```

#### Forms & clipboard

| Call | Description |
|------|-------------|
| `helpers.getFormData(form)` | Returns `{ fieldName: value }` from a `<form>` element |
| `helpers.copyToClipboard(text)` | Copy to clipboard + auto success/error toast |

```js
// In a submit handler:
event.preventDefault();
const data = helpers.getFormData(element);
// { name: 'Ana', email: 'ana@...', message: '...' }

helpers.copyToClipboard(document.getElementById('code-output').textContent);
```

#### Timing & data

| Call | Description |
|------|-------------|
| `helpers.debounce(fn, wait)` | Debounced function (for eventContext objects, not inline strings) |
| `helpers.throttle(fn, wait)` | Throttled function (same) |
| `helpers.formatDate(date?)` | `date.toLocaleDateString()`, defaults to today |
| `helpers.randomId()` | Random 7-char alphanumeric string |

```js
// Inside an eventContext object:
// search: ({ handler: helpers.debounce(function(q){ ... }, 300) })
helpers.formatDate(); // '4/29/2025'
helpers.randomId();   // 'a3bx9c1'
```

---

### `modal` — Dialog system

| Call | Description |
|------|-------------|
| `modal.create(options)` | Create and show a custom HTML modal |
| `modal.confirm(options)` | Show confirm dialog with onConfirm / onCancel callbacks |
| `modal.alert(message, title?, variant?)` | Show alert dialog |
| `modal.open(selector)` | Open existing modal by CSS selector |
| `modal.close(selector)` | Close existing modal by CSS selector |
| `modal.toggle(selector)` | Toggle existing modal |

```js
// Confirm before destructive action:
modal.confirm({
  title: '¿Eliminar?',
  message: 'Esta acción no se puede deshacer.',
  confirmText: 'Sí, eliminar',
  cancelText: 'Cancelar',
  confirmVariant: 'danger',   // 'primary' | 'danger' | 'success' | 'warning'
  onConfirm: () => { api.delete('/api/item/' + id).then(() => utils.toast.success('Eliminado')); },
  onCancel:  () => { utils.toast.info('Cancelado'); }
});

// Custom content modal:
modal.create({
  title: 'Detalles',
  content: '<p class="text-sm">Completá todos los campos.</p>',
  size: 'md',        // 'sm' | 'md' | 'lg' | 'xl' | 'full'
  animation: 'zoom', // 'fade' | 'slide' | 'zoom'
  onClose: () => { helpers.show('#form'); }
});

// Simple alert:
modal.alert('Guardado correctamente', 'Éxito', 'success');
// variant: 'info' | 'success' | 'warning' | 'error'
```

---

### `api` — HTTP fetch helpers

All methods serialize body as JSON, parse response as JSON, and show an error toast
automatically on failure. Direct `fetch()` in handler code is blocked — always use `api.*`.

| Call | Description |
|------|-------------|
| `api.get(url, options?)` | GET → parsed JSON |
| `api.post(url, data?, options?)` | POST with JSON body → parsed JSON |
| `api.put(url, data?, options?)` | PUT with JSON body → parsed JSON |
| `api.patch(url, data?, options?)` | PATCH with JSON body → parsed JSON |
| `api.delete(url, options?)` | DELETE → parsed JSON |
| `api.fetch(url, options?)` | Full control fetch → parsed JSON |

```js
// Load data on element mount:
// load handler:
"api.get('/api/posts').then(posts=>{ helpers.setHTML('#list', posts.map(p=>'<li>'+p.title+'</li>').join('')); });"

// Form submission:
"event.preventDefault(); const d=helpers.getFormData(element); api.post('/api/contact', d).then(()=>{ utils.toast.success('Enviado'); helpers.hide('#form'); helpers.show('#success'); }).catch(()=>{});"

// Delete with confirm:
"modal.confirm({ message:'¿Eliminar?', confirmVariant:'danger', onConfirm:()=>{ api.delete('/api/items/'+element.dataset.id).then(()=>{ element.closest('.card').remove(); utils.toast.success('Eliminado'); }); } });"

// Custom headers (e.g. auth token from storage):
// api.post('/api/secure', data, { headers: { 'Authorization': 'Bearer ' + utils.fromLocalStorage('token') } })
```

---

### `service` — Compiler service integration

For running backend services (WebSocket or HTTP) registered in the compiler pipeline.
Used in advanced integrations — not needed for standard pages.

| Call | Description |
|------|-------------|
| `service.run(payloadService)` | Run a registered service → output asset |
| `service.execute(contextService, payloadService, dispatch)` | Run with dispatch |
| `service.uploadFile(options)` | Upload file via XHR with progress callback |
| `service.getUploadId(uploadArgs)` | Get upload ID for multi-step upload |
| `service.createWebSocketPayload(typeMsg, method, args, socketProps?)` | Build WS payload |
| `service.createHttpPayload(type, method, args, socketProps?)` | Build HTTP payload |

```js
// File upload with progress bar:
"service.uploadFile({ form: element, url: '/upload', onProgress: (p)=>{ helpers.setText('#progress', p+'%'); }, onUploadSuccess: (d)=>{ utils.toast.success('Archivo subido'); }, onUploadError: (e)=>{ utils.toast.error(e); } });"
```

---

### Built-in quick cheatsheet

```js
// ── Toasts ───────────────────────────────────────────────
utils.toast.success('Ok!');
utils.toast.error('Falló');
utils.toast.warning('Atención');
utils.toast.info('Info');

// ── Navigation ───────────────────────────────────────────
utils.redirect('/path');
utils.openInNewTab('https://...');
utils.reload();

// ── Scroll & timing ──────────────────────────────────────
utils.scrollTo('#section');
utils.scrollToTop();
utils.wait({ fn: () => helpers.show('#el'), ms: 300 });

// ── User & page ──────────────────────────────────────────
utils.getUser();           // { id, email, isAdmin } | null
utils.getLocaleLang();     // 'en' | 'es'
utils.toLocalStorage('k', 'v');
utils.fromLocalStorage('k');

// ── Classes ──────────────────────────────────────────────
helpers.toggleClass('#el', 'hidden');
helpers.toggleClass(element, 'active');
helpers.setClasses('#el', ['active', 'font-bold'], ['inactive']);
helpers.addClass('#el', 'ring');
helpers.removeClass('#el', 'ring');

// ── Content ──────────────────────────────────────────────
helpers.setText('#el', 'texto');
helpers.setHTML('#el', '<b>bold</b>');
helpers.appendHTML('#list', '<li>item</li>');
helpers.clearContent('#el');

// ── Visibility ───────────────────────────────────────────
helpers.hide('#overlay');
helpers.show('#result', 'flex');
helpers.toggle('#menu');

// ── Forms ────────────────────────────────────────────────
const data = helpers.getFormData(element);
helpers.copyToClipboard('text');
helpers.clearFileInput('#file');

// ── Modals ───────────────────────────────────────────────
modal.confirm({ message: '¿Seguro?', confirmVariant: 'danger', onConfirm: () => {} });
modal.create({ title: 'Info', content: '<p>...</p>', size: 'md', animation: 'fade' });
modal.alert('Completado', 'Éxito', 'success');
modal.open('#my-modal');
modal.close('#my-modal');

// ── API ──────────────────────────────────────────────────
api.get('/api/resource').then(data => {});
api.post('/api/resource', { field: 'value' }).then(res => {});
api.put('/api/resource/1', updatedData).then(() => {});
api.delete('/api/resource/1').then(() => {});
```

### Simple examples

```json
"dynamicHandlers": {
  "menu-toggle": {
    "click": "const m=document.getElementById('mobile-menu');m.classList.toggle('hidden');"
  },
  "scroll-to-features": {
    "click": "document.getElementById('features').scrollIntoView({behavior:'smooth'});"
  },
  "contact-form": {
    "submit": "event.preventDefault(); alert('Mensaje enviado!');"
  },
  "email-input": {
    "blur": "if(!element.value.includes('@')){element.style.borderColor='red';}else{element.style.borderColor='';}"
  },
  "hero-section": {
    "load": "element.style.opacity='0'; setTimeout(()=>{element.style.transition='opacity 0.6s';element.style.opacity='1';},100);"
  }
}
```

### VNode wiring — the element must have a matching `attrs.id`

```json
{
  "id": "btn00001",
  "tag": "button",
  "attrs": {
    "id": "menu-toggle",
    "class": "px-4 py-2 rounded-lg ..."
  },
  "children": [...]
}
```

The `attrs.id` value (`"menu-toggle"`) is what links the VNode to its handler entry.
The VNode's own `id` field (`"btn00001"`) is internal to the builder and unrelated.

---

## eventContext

### Purpose

`eventContext` holds **shared state and helper objects** that are injected into
every handler's scope. Use it to:

- Store application state that persists across multiple event handlers
- Define helper functions called from several handlers
- Build rich stateful UI (calendars, multi-step forms, carousels, tabs, etc.)
  without external JS

### Structure in JSON

```json
"eventContext": {
  "<variableName>": "<serialized value>"
}
```

Each entry is a `key → string` pair. The builder evaluates the string at runtime
to reconstruct the actual value. The type determines how it's evaluated:

| Type in builder UI | Storage format | Runtime result |
|-------------------|----------------|----------------|
| `string` | JSON-stringified: `"\"hello\""` | `"hello"` |
| `number` | Raw literal: `"42"` | `42` |
| `boolean` | Raw literal: `"true"` | `true` |
| `function` | Source code: `"function greet(name){return 'Hi '+name;}"` | callable function |
| `object` | JS expression: `"({ state:{}, method(){} })"` | live object with methods |

### Writing object-type context (the recommended pattern)

For complex stateful UI, use the `object` type with an IIFE-style expression
wrapped in parentheses. This is the same pattern as the calendar example:

```
({
  myContext: ({
    state: {
      count: 0,
      items: []
    },
    increment() {
      this.state.count++;
      document.getElementById('counter').textContent = this.state.count;
    },
    addItem(name) {
      this.state.items.push(name);
    }
  })
})
```

The outer `({ })` wrapper is required — the builder evaluates it as a JS
expression with `safeEval("({...})")`.

### Accessing context in handlers

Once defined in `eventContext`, variables are available by name in all handlers:

```js
// In a dynamicHandler code string — myContext is from eventContext
myContext.increment();
myContext.addItem(element.value);
```

---

## Full Pattern: Stateful Calendar

This is the canonical example of a complex eventContext object.
The VNode tree provides the HTML skeleton; the context object owns all state and
render logic; the handlers wire DOM events to context methods.

### eventContext entry

Key: `calendar`, Type: `object`

```js
({
  calendar: ({
    state: {
      months: ['Enero','Febrero','Marzo','Abril','Mayo','Junio',
               'Julio','Agosto','Septiembre','Octubre','Noviembre','Diciembre'],
      availSlots: {
        1:  ['09:00','10:30','14:00'],
        2:  ['11:00','15:30'],
        5:  ['09:30','14:30'],
        8:  ['10:00','16:00'],
        9:  ['10:00'],
        14: ['09:00','11:00'],
        15: ['18:00'],
        16: ['09:30'],
        17: ['10:30','14:30'],
        21: ['08:00'],
        22: ['16:30'],
        23: ['14:30'],
        28: ['09:30']
      },
      selectedDate: null,
      selectedSlot: null
    },

    render(month, year) {
      const grid = document.getElementById('cal-grid');
      grid.dataset.month = month;
      grid.dataset.year = year;
      grid.innerHTML = '';

      const firstDay  = new Date(year, month, 1).getDay();
      const offset    = (firstDay + 6) % 7;
      const daysInMonth = new Date(year, month + 1, 0).getDate();

      for (let i = 0; i < offset; i++) {
        grid.appendChild(document.createElement('div'));
      }

      for (let d = 1; d <= daysInMonth; d++) {
        const btn     = document.createElement('button');
        const isAvail = this.state.availSlots[d] !== undefined;

        btn.textContent       = d;
        btn.style.width       = '100%';
        btn.style.aspectRatio = '1';
        btn.style.borderRadius = '50%';
        btn.style.fontSize    = '0.75rem';
        btn.style.border      = 'none';
        btn.style.cursor      = isAvail ? 'pointer' : 'default';
        btn.style.transition  = 'all 0.15s';
        btn.style.background  = isAvail ? '#F8EDED' : 'transparent';
        btn.style.color       = isAvail ? '#C65555' : '#999';
        btn.style.fontWeight  = isAvail ? '600' : '400';
        btn.style.opacity     = isAvail ? '1' : '0.35';

        btn.addEventListener('click', () => {
          if (!isAvail) return;

          // Reset all day buttons
          document.querySelectorAll('#cal-grid button').forEach(b => {
            const avail = this.state.availSlots[parseInt(b.textContent)] !== undefined;
            b.style.background  = avail ? '#F8EDED' : 'transparent';
            b.style.color       = avail ? '#C65555' : '#999';
            b.style.fontWeight  = avail ? '600' : '400';
          });

          // Highlight selected day
          btn.style.background  = '#D65C5C';
          btn.style.color       = '#fff';
          btn.style.fontWeight  = '700';

          this.state.selectedDate =
            this.state.months[month] + ' ' + d;
          document.getElementById('sel-summary-text').textContent =
            '📅 ' + this.state.selectedDate + ', ' + year;

          // Show time slots
          const hs = document.getElementById('horarios-section');
          hs.classList.remove('hidden');
          const hg = document.getElementById('horarios-grid');
          hg.innerHTML = '';

          const slots = this.state.availSlots[d] || [];
          slots.forEach(s => {
            const sb = document.createElement('button');
            sb.textContent        = s;
            sb.style.padding      = '0.5rem 0.75rem';
            sb.style.borderRadius = '0.5rem';
            sb.style.border       = '1.5px solid #E5E5E5';
            sb.style.fontSize     = '0.8rem';
            sb.style.fontWeight   = '500';
            sb.style.cursor       = 'pointer';
            sb.style.transition   = 'all 0.15s';
            sb.style.background   = 'transparent';
            sb.style.color        = '#222';

            sb.addEventListener('click', e => {
              document.querySelectorAll('#horarios-grid button').forEach(b => {
                b.style.background   = 'transparent';
                b.style.borderColor  = '#E5E5E5';
                b.style.color        = '#222';
              });
              e.target.style.background  = '#D65C5C';
              e.target.style.borderColor = '#D65C5C';
              e.target.style.color       = '#fff';

              this.state.selectedSlot = s;
              document.getElementById('sel-summary-text').textContent =
                '📅 ' + this.state.selectedDate + ' · ' + s + ' hs';

              setTimeout(() => {
                const sd = document.getElementById('step-datos');
                sd.classList.remove('hidden');
                sd.scrollIntoView({ behavior: 'smooth', block: 'start' });
              }, 300);
            });

            hg.appendChild(sb);
          });
        });

        grid.appendChild(btn);
      }
    }
  })
})
```

### dynamicHandlers for the calendar

```json
"dynamicHandlers": {
  "cal-prev": {
    "click": "let m=parseInt(document.getElementById('cal-grid').dataset.month);let y=parseInt(document.getElementById('cal-grid').dataset.year);m--;if(m<0){m=11;y--;}document.getElementById('cal-month-label').textContent=calendar.state.months[m]+' '+y;calendar.render(m,y);"
  },
  "cal-next": {
    "click": "let m=parseInt(document.getElementById('cal-grid').dataset.month);let y=parseInt(document.getElementById('cal-grid').dataset.year);m++;if(m>11){m=0;y++;}document.getElementById('cal-month-label').textContent=calendar.state.months[m]+' '+y;calendar.render(m,y);"
  },
  "cal-grid": {
    "load": "const now=new Date();calendar.render(now.getMonth(),now.getFullYear());document.getElementById('cal-month-label').textContent=calendar.state.months[now.getMonth()]+' '+now.getFullYear();"
  }
}
```

### Required VNode ids (must exist in the tree as `attrs.id`)

| `attrs.id` | Element | Purpose |
|-----------|---------|---------|
| `cal-grid` | `div` | Grid container — render target + load trigger |
| `cal-prev` | `button` | Previous month button |
| `cal-next` | `button` | Next month button |
| `cal-month-label` | `span` | Displays "Enero 2025" |
| `horarios-section` | `div` | Hidden until a day is selected |
| `horarios-grid` | `div` | Slot buttons injected here |
| `sel-summary-text` | `span` | "📅 Enero 5 · 10:30 hs" |
| `step-datos` | `div` | Hidden form that appears after slot selected |

---

## More Patterns

### Tab switcher

```json
"eventContext": {
  "tabs": "({ active:'tab1', switch(id){ this.active=id; ['tab1','tab2','tab3'].forEach(t=>{ const btn=document.getElementById('tbtn-'+t); const pnl=document.getElementById('tpnl-'+t); if(t===id){ btn.style.borderBottomColor='#3b82f6';btn.style.color='#3b82f6'; pnl.classList.remove('hidden'); }else{ btn.style.borderBottomColor='transparent';btn.style.color='#6b7280'; pnl.classList.add('hidden'); } }); } })"
}
```

```json
"dynamicHandlers": {
  "tbtn-tab1": { "click": "tabs.switch('tab1');" },
  "tbtn-tab2": { "click": "tabs.switch('tab2');" },
  "tbtn-tab3": { "click": "tabs.switch('tab3');" },
  "tbtn-tab1": { "load": "tabs.switch('tab1');" }
}
```

### Counter with shared state

```json
"eventContext": {
  "counter": "({ state:{count:0}, update(){ document.getElementById('count-display').textContent=this.state.count; } })"
}
```

```json
"dynamicHandlers": {
  "btn-inc":   { "click": "counter.state.count++; counter.update();" },
  "btn-dec":   { "click": "counter.state.count--; counter.update();" },
  "btn-reset": { "click": "counter.state.count=0; counter.update();" },
  "count-display": { "load": "counter.update();" }
}
```

### Multi-step form

```json
"eventContext": {
  "stepper": "({ current:1, total:3, go(n){ const prev=document.getElementById('step-'+this.current); const next=document.getElementById('step-'+n); if(prev) prev.classList.add('hidden'); if(next){ next.classList.remove('hidden'); next.scrollIntoView({behavior:'smooth',block:'start'}); } this.current=n; document.getElementById('step-indicator').textContent='Step '+n+' of '+this.total; } })"
}
```

```json
"dynamicHandlers": {
  "step-1-next": { "click": "stepper.go(2);" },
  "step-2-back": { "click": "stepper.go(1);" },
  "step-2-next": { "click": "stepper.go(3);" },
  "step-3-back": { "click": "stepper.go(2);" },
  "step-1":      { "load": "stepper.go(1);" }
}
```

### Accordion

```json
"dynamicHandlers": {
  "acc-header-1": { "click": "const b=document.getElementById('acc-body-1');const i=document.getElementById('acc-icon-1');b.classList.toggle('hidden');i.textContent=b.classList.contains('hidden')?'▼':'▲';" },
  "acc-header-2": { "click": "const b=document.getElementById('acc-body-2');const i=document.getElementById('acc-icon-2');b.classList.toggle('hidden');i.textContent=b.classList.contains('hidden')?'▼':'▲';" },
  "acc-header-3": { "click": "const b=document.getElementById('acc-body-3');const i=document.getElementById('acc-icon-3');b.classList.toggle('hidden');i.textContent=b.classList.contains('hidden')?'▼':'▲';" }
}
```

### Toast / notification

```json
"eventContext": {
  "toast": "({ show(msg,type='success'){ const t=document.getElementById('toast'); t.textContent=msg; t.style.background=type==='error'?'#ef4444':'#22c55e'; t.style.color='#fff'; t.style.opacity='1'; t.style.transform='translateY(0)'; setTimeout(()=>{ t.style.opacity='0'; t.style.transform='translateY(-10px)'; },2500); } })"
}
```

```json
"dynamicHandlers": {
  "submit-btn": { "click": "toast.show('Form submitted successfully!');" },
  "error-btn":  { "click": "toast.show('Something went wrong','error');" }
}
```

---

## Constraints & Security

The builder runs all handler code and context evaluation through a sandbox
(`analyzeCode` + `safeEval`). The following are **blocked**:

- `eval`, `Function`, `setTimeout` / `setInterval` with string argument
- `XMLHttpRequest`, `fetch` (network calls from handlers)
- `localStorage`, `sessionStorage`, `indexedDB`
- `import`, `require`
- `__proto__`, `prototype` manipulation
- DOM access outside the current page (`window.top`, `parent`, `opener`)

`setTimeout` and `setInterval` with a **function argument** (not a string) are allowed:
```js
// ✓ allowed
setTimeout(() => { element.style.opacity = '1'; }, 300);

// ✗ blocked — string argument is treated like eval
setTimeout("element.style.opacity='1'", 300);
```

**Never use `console.log`** in handler code — use `console.error` only if logging
is strictly necessary (it's less likely to be stripped in sandboxed environments).

---

## eventContext Rules for JSON generation

When generating `eventContext` in a page JSON, the value is always a **raw string**
as the builder stores it (the same string the user types in the UI):

1. **Strings** → JSON-stringify the value: `"\"hello world\""` → evaluates to `"hello world"`
2. **Numbers / booleans** → raw literal: `"42"`, `"true"`
3. **Functions** → full function source: `"function greet(name){ return 'Hi '+name; }"`
4. **Objects with methods** → wrap in `({ })`:
   ```
   "({ state:{count:0}, increment(){ this.state.count++; } })"
   ```
5. **Multiple context objects** → wrap all in a single outer `({ })`:
   ```
   "({ calendar:({ ... }), stepper:({ ... }) })"
   ```
   Or use separate entries in the `eventContext` object — one key per context object.

### In JSON (what goes in the file)

```json
"eventContext": {
  "counter": "({ state:{count:0}, update(){ document.getElementById('count-display').textContent=this.state.count; } })",
  "appTitle": "\"DevSummit 2025\"",
  "maxItems": "10",
  "debugMode": "false"
}
```

---

## SKILL.md Integration Note

This file (`references/interactivity.md`) should be read when:
- The page request includes any interactive element (tabs, accordions, forms, calendars, carousels, steppers, counters, toggles, modals)
- The user asks to add `dynamicHandlers` or `eventContext` to an existing page
- Any element needs to show/hide, update text, change style, or track state in response to user input

Add to the SKILL.md reference table:
```
| `references/interactivity.md` | When the page has interactive elements — handlers, context, stateful UI |
```