# Portfolio Content Authoring Guide

Everything you need to write the markdown that powers this site. Content lives in
the `content/` folder — you never need to touch the React code to add or change a
section.

---

## Table of contents

1. [How content loads](#1-how-content-loads)
2. [Frontmatter](#2-frontmatter)
3. [Variables `{{ }}`](#3-variables--)
4. [Vertical spacing](#4-vertical-spacing)
5. [Standard markdown](#5-standard-markdown)
6. [Shortcodes — the rules](#6-shortcodes--the-rules)
7. [Shortcode reference](#7-shortcode-reference)
8. [Shared concepts: colours, icons, width & align](#8-shared-concepts)
9. [Cross-references (citations)](#9-cross-references-citations)
10. [Gotchas & limitations](#10-gotchas--limitations)
11. [A complete example file](#11-a-complete-example-file)
12. [What is *not* content-editable](#12-what-is-not-content-editable)

---

## 1. How content loads

- Each **section of the page is one `.md` file** inside `content/`.
- `content/index.json` is a JSON array listing the files to load:

  ```json
  ["overview.md", "education.md", "playground.md"]
  ```

  A file only appears on the site if it is listed here.
- **Display order is decided by the `rank` frontmatter field** (ascending), *not*
  by the order in `index.json`.
- The **section's id** is the filename without `.md` (`education.md` → `education`).
  This id is used by the nav and by cross-references (`::ref{to="education"}`).
- The dev server serves these files live. While `npm run dev` is running, editing a
  content file hot-reloads the page automatically.

---

## 2. Frontmatter

Optional metadata block at the very top of a file, fenced by `---` lines:

```md
---
label: Education
rank: 2
$calendar: ::icon{name="Calendar Today" weight="bold" fill="true"}
---
```

Rules:

- Must be the **first thing in the file**, opening with `---` on line 1 and closing
  with a line that is exactly `---`.
- Each entry is `key: value`, split on the **first** colon (so values may contain
  colons).
- **Value casting:** `true`/`false` become booleans, a run of digits (`2`, `15`)
  becomes a number, everything else stays a string.

Recognised keys:

| Key      | Meaning                                                              |
|----------|---------------------------------------------------------------------|
| `label`  | The text shown in the sidebar nav for this section.                 |
| `rank`   | Sort order (number). Lower = higher on the page.                    |
| `$name`  | Defines a **variable** named `name` (see next section).             |

> The collapsed (narrow) sidebar shows the **first letter** of each `label`.

---

## 3. Variables `{{ }}`

Any frontmatter key starting with `$` defines a variable you can reuse in the body
with `{{name}}` (drop the `$`). The value can be **any text, including a shortcode** —
which makes it perfect for an icon you repeat a lot.

```md
---
$calendar: ::icon{name="Calendar Today" weight="bold" fill="true"}
$books:    ::icon{name="Library Books" weight="bold" fill="true"}
---

### {{calendar}} Aug 2025 – May 2027

::toggle
### {{books}} Coursework
...
::end
```

- Variable names are word characters (`[A-Za-z0-9_]`).
- An unknown `{{var}}` is replaced with nothing (empty string).

---

## 4. Vertical spacing

A normal blank line between blocks gives the standard paragraph gap. **Add extra
blank lines to insert empty vertical space:**

- 1 blank line → normal gap.
- Each **additional** blank line adds **1.5em** of space.
  (Formula: for `N` consecutive newlines where `N ≥ 3`, the gap is `(N − 2) × 1.5em`.)

So two blank lines ≈ 1.5em, three blank lines ≈ 3em, and so on. Useful for breathing
room inside a tall, sparse section.

---

## 5. Standard markdown

Full **GitHub-Flavored Markdown** is supported, plus raw HTML and code highlighting.

### Text & structure
- Headings `#` … `####` (h1–h4 are styled; the first `#` is the biggest).
- `**bold**`, `*italic*`, `~~strikethrough~~`.
- Links `[text](https://…)`, images `![alt](/path.jpg)`.
- Blockquotes `> …`, horizontal rule `---` (on its own line, with blank lines around it).

### Lists & tables
- Bulleted (`- `), numbered (`1. `), and **task lists** (`- [ ]` / `- [x]`).
- GFM tables:

  ```md
  | Code       | Course                   | Status    |
  | ---------- | ------------------------ | --------- |
  | CSCI-P 556 | Applied Machine Learning | Completed |
  ```

### Code blocks (with syntax highlighting)

Fence with a language for highlighting (powered by highlight.js):

````md
```python
def greet(name: str) -> str:
    return f"Hello, {name}!"
```
````

- Languages auto-detect even without a label, but labelling is more reliable.
- Supported languages are highlight.js's common set (Python, JS/TS, JSON, bash,
  SQL, etc.).
- **Inline code** like `` `value` `` renders wrapped in literal backtick marks — that
  is an intentional style choice.

### Raw HTML
Raw HTML is allowed. You can drop in elements when markdown isn't enough:

```md
<div style="height: 2em"></div>
<br />
```

---

## 6. Shortcodes — the rules

Shortcodes are the custom building blocks (callouts, cards, galleries, etc.). There
are **three kinds**:

| Kind      | Needs `::end`? | Where it goes              | Examples                                  |
|-----------|----------------|----------------------------|-------------------------------------------|
| **Block** | ✅ Yes          | On its own lines           | `toggle`, `callout`, `columns`, `col`, `card` |
| **Void**  | ❌ No (self-closing) | On its own line       | `gallery`, `embed`, `bookmark`            |
| **Inline**| ❌ No            | Inside a line of text      | `icon`, `badge`, `ref`, `anchor`          |

### Syntax

```
::name{prop="value" prop2="value2"}
```

- A **block/void** shortcode must be **alone on its line**: `::name{…}` and the
  closing `::end` each on their own line. (The renderer auto-pads them with blank
  lines, but keeping blank lines around them yourself keeps the source readable.)
- An **inline** shortcode appears within text and **always requires the `{ }`**,
  e.g. `I use ::badge{label="React"} daily.`
- A block/void shortcode with no props can drop the braces (`::columns`, `::col`).

### Props
- Format is strictly `key="value"` or `key='value'` (double or single quotes).
- Numbers (`size="20"`) and booleans (`fill="true"`) are auto-converted.
- To put a literal double quote inside a double-quoted value, escape it: `\"`.
  Or use single quotes around the value (this is how gallery JSON works).
- **No `}` is allowed anywhere inside a shortcode's braces.** This is the single most
  important limitation — see [§10](#10-gotchas--limitations).

### `::end` and nesting
- Every **block** shortcode is closed by a line that is exactly `::end`.
- Blocks **nest** — a `::card` inside a `::col` inside `::columns`, each with its own
  `::end`. **Void** shortcodes inside a block don't need (and must not have) an `::end`.

---

## 7. Shortcode reference

### Inline shortcodes

#### `::icon` — a Material Symbols icon
```md
::icon{name="Rocket Launch" weight="bold" fill="true" color="tip" size="22"}
```

| Prop     | Default        | Notes                                                                 |
|----------|----------------|-----------------------------------------------------------------------|
| `name`   | — (required)   | Material Symbol name. Spaces/caps are fine ("Calendar Today").        |
| `size`   | `20`           | Font size in px.                                                      |
| `weight` | `400`          | `thin` `extralight` `light` `regular` `medium` `semibold` `bold`, or a number 100–700. |
| `fill`   | `false`        | `true` for the filled variant, `false` for outline.                  |
| `grad`   | `0`            | Grade: `low` `normal` `high`, or a number.                           |
| `opsz`   | auto           | Optical size; defaults to your `size` clamped to 20–48.              |
| `color`  | inherits text  | A colour token or raw CSS colour (see [§8](#8-shared-concepts)).      |
| `gap`    | `6`            | Right-hand margin in px (space before following text).               |

#### `::badge` — an inline pill / tag
```md
I work with ::badge{label="PyTorch" color="danger" icon="local_fire_department"}.
```

| Prop    | Default          | Notes                                          |
|---------|------------------|------------------------------------------------|
| `label` | — (required)     | The text inside the pill.                      |
| `color` | `secondary`      | Colour token or raw CSS colour ([§8](#8-shared-concepts)). |
| `icon`  | none             | Optional Material Symbol shown before the label. |

#### `::ref` — a clickable cross-reference (see [§9](#9-cross-references-citations))
```md
See ::ref{to="education" label="my education" icon="north_east"}.
```

| Prop    | Default      | Notes                                                              |
|---------|--------------|--------------------------------------------------------------------|
| `to`    | — (required) | Target id: a **section id** (filename) or an **`::anchor` id**.    |
| `label` | = `to`       | Text shown for the link.                                           |
| `icon`  | none         | Optional Material Symbol shown after the label.                    |

#### `::anchor` — a cross-reference target (see [§9](#9-cross-references-citations))
```md
My favourite course is ::anchor{id="fav-course" label="Applied Machine Learning"}.
```

| Prop    | Default      | Notes                                                              |
|---------|--------------|--------------------------------------------------------------------|
| `id`    | — (required) | Unique id used as a `::ref` target. Don't clash with section ids.  |
| `label` | none         | Text rendered inline; it gets highlighted when jumped to.          |

---

### Block shortcodes (need `::end`)

#### `::toggle` — collapsible section
The **first** line/element inside is the clickable header; everything after is the
collapsible body.

```md
::toggle{default="open"}
### {{books}} Coursework
| Course | Status |
| ------ | ------ |
| Applied ML | Completed |
::end
```

| Prop      | Default   | Notes                                            |
|-----------|-----------|--------------------------------------------------|
| `default` | collapsed | Set `default="open"` to start expanded.          |

#### `::callout` — coloured info box
```md
::callout{type="tip" icon="auto_awesome"}
A helpful tip with **markdown** inside.
::end
```

| Prop   | Default | Notes                                                              |
|--------|---------|--------------------------------------------------------------------|
| `type` | `note`  | `info` `tip` `warning` `danger` `note` — sets the colour.          |
| `icon` | per type| Defaults: info→`info`, tip→`lightbulb`, warning→`warning`, danger→`error`, note→`note`. Override with any Material Symbol. |

#### `::columns` + `::col` — side-by-side layout
`::columns` holds one or more `::col` blocks. Columns are equal width and **stack
vertically on mobile** (≤ 767px).

```md
::columns

::col
Left column content.
::end

::col
Right column content.
::end

::end
```

Neither takes props. Put anything inside a `::col` — including cards.

#### `::card` — a bordered tile (optionally a link)
```md
::card{title="Machine Learning" icon="neurology" href="https://example.com"}
Model training, evaluation, and deployment.
::end
```

| Prop    | Default | Notes                                                                 |
|---------|---------|-----------------------------------------------------------------------|
| `title` | none    | Bold title row.                                                       |
| `icon`  | none    | Material Symbol shown beside the title.                               |
| `href`  | none    | Makes the whole card a link. If it starts with `http`, opens in a new tab. |

Cards have a subtle hover lift. Combine with `::columns`/`::col` to make a card grid.

---

### Void shortcodes (self-closing — no `::end`)

#### `::gallery` — image carousel
```md
::gallery{images='["/img/a.jpg", "/img/b.jpg", "/img/c.jpg"]' width="640" align="center"}
```

| Prop     | Default | Notes                                                                  |
|----------|---------|------------------------------------------------------------------------|
| `images` | — (required) | A JSON **array of image URL strings**, wrapped in **single quotes**. |
| `width`  | full    | Max width: a number (px) or any CSS size (`"80%"`, `"500px"`).         |
| `align`  | `left`  | `left` `center` `right`.                                               |

- Shows prev/next arrows and dot indicators when there is more than one image.
- ⚠️ Because `}` is forbidden in props, the **object form** (`{"src":…,"alt":…}`)
  does **not** work — use a plain array of URL strings.

#### `::embed` — responsive video / iframe (16:9)
```md
::embed{type="youtube" id="jNQXAC9IVRw" title="Demo" width="560" align="center"}
```

| Prop    | Default  | Notes                                                                  |
|---------|----------|------------------------------------------------------------------------|
| `type`  | —        | Use `youtube` together with `id`.                                      |
| `id`    | —        | YouTube video id (the part after `v=`).                                |
| `src`   | —        | Alternative to `type`/`id`: a full iframe URL for any provider.        |
| `title` | generic  | Accessible title for the iframe.                                       |
| `width` | `640`    | Max width: number (px) or CSS size. Use `width="100%"` for full width. |
| `align` | `left`   | `left` `center` `right`.                                               |

#### `::bookmark` — a link preview card
```md
::bookmark{href="https://github.com/you" title="My GitHub" description="Open-source work." image="https://…/thumb.jpg"}
```

| Prop          | Default | Notes                                                  |
|---------------|---------|--------------------------------------------------------|
| `href`        | — (required) | Link destination (opens in a new tab if external).|
| `title`       | none    | Bold title.                                            |
| `description` | none    | Up to two lines, then truncated.                       |
| `image`       | none    | Thumbnail URL shown on the right.                      |
| `width`       | full    | Number (px) or CSS size.                               |
| `align`       | `left`  | `left` `center` `right`.                               |

---

## 8. Shared concepts

### Colour tokens
Anywhere a `color` (or badge colour) is accepted, you can use:

| Value                              | Result                                  |
|------------------------------------|-----------------------------------------|
| `primary`                          | Main text colour (theme-aware).         |
| `secondary`                        | Muted text colour (theme-aware).        |
| `info` `tip` `warning` `danger` `note` | The accent colours (also used by callouts). |
| Any raw CSS colour                 | e.g. `#ff6600`, `tomato`, `var(--color-fg-primary)`. |

All tokens automatically adapt to light/dark theme.

### Material Symbols icon names
Icons use Google's **Material Symbols (Outlined)**. Browse and search names at
<https://fonts.google.com/icons>. You can write the name with spaces and any case
(`"Rocket Launch"`) or in snake_case (`rocket_launch`) — both work.

### `width` & `align` (media blocks)
On `::gallery`, `::embed`, and `::bookmark`:
- `width`: a bare number is treated as **pixels** (`width="500"` → 500px); or pass any
  CSS length/percent (`"80%"`, `"400px"`). The block stays fluid up to that cap, so it
  scales down on small screens.
- `align`: `left` (default), `center`, or `right`.

---

## 9. Cross-references (citations)

Link from one spot to another on the page, PDF-citation style: clicking a `::ref`
smooth-scrolls to its target and briefly **highlights** it.

There are two valid targets:

1. **A whole section** — use its id (the filename without `.md`):
   ```md
   Jump to ::ref{to="education" label="my education"}.
   ```
2. **A specific spot in the text** — drop an `::anchor` there, then point a `::ref` at
   its `id`:
   ```md
   ... my favourite course is ::anchor{id="fav-course" label="Applied Machine Learning"}.

   (elsewhere) As mentioned in ::ref{to="fav-course" label="my favourite course"} ...
   ```

Rules:
- `::anchor` ids must be **unique** across the whole page and should not collide with
  section ids.
- The jump respects reduced-motion settings (instant, no flash) for accessibility.

---

## 10. Gotchas & limitations

- **No `}` inside shortcode props.** The parser reads everything between `{` and the
  first `}`. This means:
  - Gallery `images` must be a plain URL array, not objects.
  - Avoid `}` in any prop value (raw CSS like `var(--x)` is fine; object literals are not).
- **Block markers must be alone on their line** (`::name{…}` and `::end`). Don't put
  text on the same line as `::end`.
- **Inline shortcodes require braces** — `::icon{…}`, `::badge{…}`, `::ref{…}`,
  `::anchor{…}`. `::icon` with no braces won't render.
- **Quoting:** use single quotes around a value that itself contains double quotes
  (the gallery JSON). Otherwise double quotes are conventional.
- **Every file must be in `content/index.json`**, and ordering comes from `rank`.
- **Keep sections about one screen tall** for the cleanest full-page snap scrolling.
  Sections grow automatically if their content is taller, but a very tall section can
  feel slightly "pull-y" with snap scrolling.
- A `{{variable}}` that isn't defined in frontmatter renders as empty.

---

## 11. A complete example file

`content/projects.md`:

````md
---
label: Projects
rank: 3
$star: ::icon{name="Star" weight="bold" fill="true" color="warning"}
---

# {{star}} Selected Projects
### Things I'm proud of


::callout{type="info"}
These are a few highlights — see ::ref{to="overview" label="the intro"} for context.
::end


::columns

::col

::card{title="Vision Model" icon="visibility" href="https://github.com/you/vision"}
Real-time object detection in PyTorch.

Stack: ::badge{label="PyTorch" color="danger"} ::badge{label="OpenCV" color="info"}
::end

::end

::col

::card{title="Data Pipeline" icon="database"}
Batch + streaming ETL on the cloud.

Stack: ::badge{label="SQL" color="tip"} ::badge{label="Airflow" color="note"}
::end

::end

::end


## A short demo

```python
model = load_model("vision.pt")
print(model.predict(frame))
```


::gallery{images='["/img/proj1.jpg", "/img/proj2.jpg"]' width="640" align="center"}


::embed{type="youtube" id="jNQXAC9IVRw" title="Project walkthrough" width="560" align="center"}


::bookmark{href="https://github.com/you" title="More on GitHub" description="All my repositories." image="/img/gh.jpg"}


## Notes

::toggle
### {{star}} Why this matters
A collapsible explanation goes here.
::end
````

---

## 12. What is *not* content-editable

These are part of the app UI, not markdown — change them in code, not `content/`:

- **Your name** ("DHRUV GUPTA") — `src/components/Sidebar.jsx`.
- **Avatar image** — `src/assets/avatar.*` (png/jpg/webp/svg/avif).
- **Sidebar social links / résumé** — `src/components/Links.jsx` (and `public/Resume.pdf`).
- **Theme colours, fonts, motion, accent & syntax palettes** — `src/design.config.js`.
- **The list of sections** and their order — `content/index.json` + each file's `rank`.
