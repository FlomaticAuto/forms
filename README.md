# Olympic Paints — Form Renderer (REPO 1)

Static single-page form renderer deployed to GitHub Pages. No build step, no server.

**Live URL:** `https://flomaticauto.github.io/forms`  
**Admin app (REPO 2):** `github.com/FlomaticAuto/olympic-paints-forms-admin`

---

## How it works

1. Someone visits `https://flomaticauto.github.io/forms?id=<form_id>`
2. `index.html` reads the `id` query parameter
3. Fetches the form schema from Supabase using the anon key (read-only, RLS enforced)
4. Renders all fields dynamically from the schema JSON
5. On submit, inserts a row into `form_submissions` directly in Supabase
6. Shows a confirmation screen — no page reload

If the form ID is missing, not found, expired, or archived, a clean "Form unavailable" message is shown.

---

## Setup after cloning

Open `index.html` and replace the two placeholder constants near the top of the `<script>` block:

```js
const SUPABASE_URL  = 'REPLACE_WITH_YOUR_SUPABASE_URL';
const SUPABASE_ANON = 'REPLACE_WITH_YOUR_SUPABASE_ANON_KEY';
```

Both values are found in your Supabase dashboard → **Project Settings → API**.

The anon key is safe to embed in this public file — Supabase RLS policies ensure it can only:
- SELECT active, non-archived forms
- INSERT into `form_submissions`

It cannot read submissions, modify schemas, or access any other data.

---

## Deploying to GitHub Pages

1. Create a repo named `forms` under the `FlomaticAuto` organisation
2. Push `index.html` (and `logo.jpg`) to the `main` branch
3. In repo **Settings → Pages**, set source to `main` branch, root `/`
4. GitHub Pages serves the site at `https://flomaticauto.github.io/forms`

No build workflow needed — `index.html` is already the final file.

### Logo

Copy `Olympic Paints Logo Digital.jpg` from the brand assets folder into this repo root and name it `logo.jpg`. It is referenced by `index.html` as a relative path. The `<img>` tag has an `onerror` fallback that hides the broken image if the file is missing.

---

## Supported field types

| Type | Renders as |
|---|---|
| `text` | Single-line text |
| `textarea` | Multi-line text |
| `number` | Numeric input |
| `email` | Email input |
| `tel` | Phone number input |
| `date` | Date picker |
| `select` | Dropdown |
| `radio` | Radio buttons |
| `checkbox` | Checkboxes (multi-select, stored as array) |

---

## Creating forms

Forms are created via the admin API — not from this repo. See the admin README for curl examples:

```bash
curl -s -X POST https://your-admin-app.vercel.app/api/forms/create \
  -H "Content-Type: application/json" \
  -H "x-admin-secret: YOUR_ADMIN_SECRET" \
  -d '{
    "title": "Store Visit Report",
    "schema": [
      { "id": "name", "type": "text", "label": "Your name", "required": true, "order": 1 },
      { "id": "store", "type": "select", "label": "Store", "required": true,
        "options": ["Lenasia", "Soweto", "Roodepoort"], "order": 2 }
    ]
  }'
```

The response contains the `public_url` to share.

---

## Files

```
index.html    — complete self-contained form renderer
logo.jpg      — Olympic Paints logo (copy from brand assets)
.env.example  — documents the two JS constants to replace in index.html
.gitignore
README.md
```
