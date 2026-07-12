# Portfolio Content Authoring Guide

Everything you need to write the markdown that powers this site. Content lives in
the `local/pages/profile-content/` folder — you never need to touch the React code to add or change a
section.

The shortcodes are built on [`remark-directive`](https://github.com/remarkjs/remark-directive),
so the syntax is standard markdown directives.

---

## Table of contents

1. [How content loads](#1-how-content-loads)
2. [Frontmatter](#2-frontmatter)
3. [Variables `{{ }}`](#3-variables--)
4. [Line breaks & vertical spacing](#4-line-breaks--vertical-spacing)
5. [Standard markdown](#5-standard-markdown)
6. [Directives — the rules](#6-directives--the-rules)
7. [Directive reference](#7-directive-reference)
8. [Shared concepts: colours, icons, width & align](#8-shared-concepts)
9. [Cross-references (citations)](#9-cross-references-citations)
10. [Gotchas & limitations](#10-gotchas--limitations)
11. [A complete example file](#11-a-complete-example-file)
12. [What is *not* content-editable](#12-what-is-not-content-editable)

---

## 1. How content loads

- Each **section of the page is one `.md` file** inside `local/pages/profile-content/`.
- `local/pages/profile-content/index.json` is a JSON array listing the files to load:

  ```json
  ["overview.md", "education.md", "projects.md"]
  ```

  A file only appears on the site if it is listed here.
- **Display order is decided by the `rank` frontmatter field** (ascending), *not*
  by the order in `index.json`.
- The **section's id** is the filename without `.md` (`education.md` → `education`).
  This id is used by the nav and by cross-references (`:ref{to="education"}`).
- The dev server serves these files live. While `npm run dev` is running, editing a
  content file hot-reloads the page automatically.

---

## 2. Frontmatter

Optional metadata block at the very top of a file, fenced by `---` lines:

```md
---
label: Education
rank: 2
icon: school
calendar: :icon{name="Calendar Today"}
---
```

Rules:

- Must be the **first thing in the file**, opening with `---` on line 1 and closing
  with a line that is exactly `---`.
- Each entry is `key: value`, split on the **first** colon (so values may contain
  colons — including inline directives).
- **Value casting:** `true`/`false` become booleans, a run of digits (`2`, `15`)
  becomes a number, everything else stays a string.

Recognised keys:

| Key      | Meaning                                                              |
|----------|---------------------------------------------------------------------|
| `label`  | The text shown in the sidebar nav for this section.                 |
| `rank`   | Sort order (number). Lower = higher on the page.                    |
| `icon`   | Material Symbol name for the sidebar nav (e.g. `school`).           |

Any **other** key becomes a reusable [variable](#3-variables--).

> The collapsed (narrow) sidebar shows each section's `icon`.

---

## 3. Variables `{{ }}`

Any frontmatter key other than `label`/`rank`/`icon` defines a variable you can
reuse in the body with `{{name}}`. The value can be **any text, including a
directive** — which makes it perfect for an icon you repeat a lot.

```md
---
calendar: :icon{name="Calendar Today"}
books: :icon{name="Library Books"}
---

### {{calendar}} Aug 2025 – May 2027

:::toggle
## {{books}} Coursework
...
:::
```

- Variable names are word characters (`[A-Za-z0-9_]`).
- An unknown `{{var}}` is replaced with nothing (empty string).

---

## 4. Line breaks & vertical spacing

Use one blank line between Markdown blocks. For a hard line break inside the same
paragraph, use `<br>`.

For intentional vertical spacing between blocks, use `::space` on its own line:

```md
First block.

::space{size="md"}

Second block.
```

Use `::space{size="sm"}`, `::space{size="md"}`, or `::space{size="lg"}` for
consistent spacing.

---

## 5. Standard markdown

Full **GitHub-Flavored Markdown** is supported, plus raw HTML and code highlighting.

### Text & structure
- Headings `#` … `####` (h1–h4 are styled; the first `#` is the biggest).
- `**bold**`, `*italic*`, `~~strikethrough~~`.
- Links `[text](https://…)`, images `![alt](image-name.jpg)`.
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
- **Inline code** like `` `value` `` renders wrapped in literal backtick marks — that
  is an intentional style choice.

### Raw HTML
Safe prose HTML is allowed. Scripts, event handlers, raw iframes, unsafe URLs, and arbitrary styles are removed. For a block
of justified prose, wrap it in a `<div>` with blank lines around the inner markdown:

```md
<div style="text-align: justify">

Body text that should be justified, with **markdown** still working inside.

</div>
```

---

## 6. Directives — the rules

Directives are the custom building blocks (callouts, cards, galleries, etc.). The
**number of leading colons** decides the kind:

| Kind          | Marker              | Closes with          | Where it goes         | Examples                                      |
|---------------|---------------------|----------------------|-----------------------|-----------------------------------------------|
| **Container** | `:::name{…}` (3+ colons) | a colon-only fence `:::` | On its own lines  | `toggle`, `callout`, `columns`, `col`, `card` |
| **Leaf**      | `::name{…}` (2 colons)   | self-closing         | On its own line       | `gallery`, `embed`, `bookmark`, `space`       |
| **Inline**    | `:name{…}` (1 colon)     | self-closing         | Inside a line of text | `icon`, `badge`, `ref`, `anchor`              |

### Syntax

```
:::name{prop="value" prop2="value2"}
content
:::
```

- **Container** and **leaf** directives must be **alone on their line**, with blank
  lines around them for readable source.
- An **inline** directive appears within text and **always requires the `{ }`**,
  e.g. `I use :badge{label="React"} daily.`
- A directive with no props can drop the braces (`:::columns`, `:::col`).

### Props
- Format is `key="value"` or `key='value'` (double or single quotes).
- Numbers (`size="20"`) and booleans (`fill="true"`) are auto-converted.
- Props are **named** — there are no positional arguments.
- Use single quotes around a value that itself contains double quotes (the gallery
  JSON, for example).

### Nesting containers
- A container is closed by a line that is only colons (`:::`), matching the colon
  count it opened with.
- When containers **nest**, the outer one must use **more colons** than the inner
  one. Work from the inside out: the innermost container uses 3 colons, each
  enclosing level adds one.

```md
:::::toggle{default="open"}
## Summary

::::columns
:::col
Left column.
:::
:::col
Right column.
:::
::::
:::::
```

Here `col` uses 3 colons, `columns` (which contains them) uses 4, and `toggle` (which
contains `columns`) uses 5. Leaf/inline directives inside a container never need a
closing fence.

---

## 7. Directive reference

### Inline directives

#### `:icon` — a Material Symbols icon
```md
:icon{name="Rocket Launch" weight="bold" fill="true" color="tip" size="22"}
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

#### `:badge` — an inline pill / tag
```md
I work with :badge{label="PyTorch" color="danger" icon="local_fire_department"}.
```

| Prop    | Default          | Notes                                          |
|---------|------------------|------------------------------------------------|
| `label` | — (required)     | The text inside the pill.                      |
| `color` | `secondary`      | Colour token or raw CSS colour ([§8](#8-shared-concepts)). |
| `icon`  | none             | Optional Material Symbol shown before the label. |

#### `:ref` — a clickable cross-reference (see [§9](#9-cross-references-citations))
```md
See :ref{to="education" label="my education" icon="north_east"}.
```

| Prop    | Default      | Notes                                                              |
|---------|--------------|--------------------------------------------------------------------|
| `to`    | — (required) | Target id: a **section id** (filename) or an **`:anchor` id**.     |
| `label` | = `to`       | Text shown for the link.                                           |
| `icon`  | none         | Optional Material Symbol shown after the label.                    |

#### `:anchor` — a cross-reference target (see [§9](#9-cross-references-citations))
```md
My favourite course is :anchor{id="fav-course" label="Applied Machine Learning"}.
```

| Prop    | Default      | Notes                                                              |
|---------|--------------|--------------------------------------------------------------------|
| `id`    | — (required) | Unique id used as a `:ref` target. Don't clash with section ids.   |
| `label` | none         | Text rendered inline; it gets highlighted when jumped to.          |

---

### Container directives

#### `:::toggle` — collapsible section
The **first** line/element inside is the clickable header; everything after is the
collapsible body.

```md
:::toggle{default="open"}
### {{books}} Coursework
| Course | Status |
| ------ | ------ |
| Applied ML | Completed |
:::
```

| Prop      | Default   | Notes                                            |
|-----------|-----------|--------------------------------------------------|
| `default` | collapsed | Set `default="open"` to start expanded.          |

#### `:::callout` — coloured info box
```md
:::callout{type="tip" icon="auto_awesome"}
A helpful tip with **markdown** inside.
:::
```

| Prop   | Default | Notes                                                              |
|--------|---------|--------------------------------------------------------------------|
| `type` | `note`  | `info` `tip` `warning` `danger` `note` — sets the colour.          |
| `icon` | per type| Defaults: info→`info`, tip→`lightbulb`, warning→`warning`, danger→`error`, note→`note`. Override with any Material Symbol. |

#### `:::columns` + `:::col` — side-by-side layout
`columns` holds one or more `col` blocks. Columns are equal width and **stack
vertically on mobile** (≤ 767px). Remember the [nesting rule](#nesting-containers):
`columns` needs one more colon than its `col` children.

```md
::::columns{separator="|"}
:::col{align="center"}
Left column content.
:::
:::col
Right column content.
:::
::::
```

| Prop (columns) | Default | Notes                                             |
|----------------|---------|---------------------------------------------------|
| `separator`    | none    | A character shown between columns (e.g. `"|"`).   |
| `style`        | none    | Raw CSS applied to the grid (e.g. `"gap: 0.5em"`).|

| Prop (col) | Default | Notes                                             |
|------------|---------|---------------------------------------------------|
| `width`    | `1fr`   | Track width: a CSS size (`"1.5em"`), or `fit`/`content`/`full`. |
| `align`    | `left`  | `left` `center` `right` text alignment.           |

Put anything inside a `col` — including cards.

#### `:::card` — a bordered tile (optionally a link)
```md
:::card{title="Machine Learning" icon="neurology" href="https://example.com"}
Model training, evaluation, and deployment.
:::
```

| Prop    | Default | Notes                                                                 |
|---------|---------|-----------------------------------------------------------------------|
| `title` | none    | Bold title row.                                                       |
| `icon`  | none    | Material Symbol shown beside the title.                               |
| `href`  | none    | Makes the whole card a link. If it starts with `http`, opens in a new tab. |

Cards have a subtle hover lift. Combine with `columns`/`col` to make a card grid.

---

### Leaf directives (self-closing — no fence)

#### `::space` — fixed vertical spacing
```md
::space{size="sm"}
::space{size="md"}
::space{size="lg"}
```

| Prop   | Default | Notes                                           |
|--------|---------|-------------------------------------------------|
| `size` | `md`    | `xs`, `sm`, `md`, `lg`, `xl`, or any CSS size. |

#### `::gallery` — image carousel

Section image filenames default to `media/sections/` in the profile bucket. Use only the lowercase filename and extension, such as `exp1.png`. To use another profile-bucket directory, provide a bucket-root-relative path such as `media/projects/project-01.png`.

```md
::gallery{images='["gallery-01.jpg", "gallery-02.jpg", "gallery-03.jpg"]' width="100%" aspect="16/9" fit="contain" align="center"}
```

| Prop     | Default | Notes                                                                  |
|----------|---------|------------------------------------------------------------------------|
| `images` | — (required) | A JSON **array of image URL strings**, wrapped in **single quotes**. |
| `width`  | full    | Max width: a number (px) or any CSS size. Use `"100%"` or `"full"` for full width. |
| `height` | auto    | Fixed carousel height: a number (px) or CSS size (`"360px"`, `"45vh"`). |
| `aspect` | auto    | Carousel aspect ratio (`"16/9"`, `"4/3"`, `"1/1"`). Ignored if `height` is set. |
| `fit`    | `cover` | Image fit inside the frame: `cover`, `contain`, `fill`, `scale-down`, or `none`. |
| `align`  | `left`  | `left` `center` `right`.                                               |

- Shows prev/next arrows and dot indicators when there is more than one image.

#### `::embed` — responsive video / iframe (16:9)
```md
::embed{type="youtube" id="jNQXAC9IVRw" title="Demo" width="560" align="center"}
```

| Prop    | Default  | Notes                                                                  |
|---------|----------|------------------------------------------------------------------------|
| `type`  | —        | Use `youtube` together with `id`.                                      |
| `id`    | —        | YouTube video id (the part after `v=`).                                |
| `src`   | —        | Alternative to `type`/`id`: an HTTPS iframe URL rendered in a restricted sandbox. |
| `title` | generic  | Accessible title for the iframe.                                       |
| `width` | `640`    | Max width: number (px) or CSS size. Use `width="100%"` for full width. |
| `align` | `left`   | `left` `center` `right`.                                               |

#### `::bookmark` — a link preview card
```md
::bookmark{href="https://github.com/you" title="My GitHub" description="Open-source work." image="github-thumbnail.jpg"}
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

### `width`, `height`, `aspect` & `align` (media blocks)
On `::gallery`, `::embed`, and `::bookmark`:
- `width`: a bare number is treated as **pixels** (`width="500"` → 500px); or pass any
  CSS length/percent (`"80%"`, `"400px"`). The block stays fluid up to that cap, so it
  scales down on small screens.
- `align`: `left` (default), `center`, or `right`.

On `::gallery` only:
- `height`: fixes the carousel frame height.
- `aspect`: sets the carousel frame shape when height is not fixed.
- `fit`: controls whether images crop (`cover`) or fully fit inside the frame (`contain`).

---

## 9. Cross-references (citations)

Link from one spot to another on the page, PDF-citation style: clicking a `:ref`
smooth-scrolls to its target and briefly **highlights** it.

There are two valid targets:

1. **A whole section** — use its id (the filename without `.md`):
   ```md
   Jump to :ref{to="education" label="my education"}.
   ```
2. **A specific spot in the text** — drop an `:anchor` there, then point a `:ref` at
   its `id`:
   ```md
   ... my favourite course is :anchor{id="fav-course" label="Applied Machine Learning"}.

   (elsewhere) As mentioned in :ref{to="fav-course" label="my favourite course"} ...
   ```

Rules:
- `:anchor` ids must be **unique** across the whole page and should not collide with
  section ids.
- The jump respects reduced-motion settings (instant, no flash) for accessibility.

---

## 10. Gotchas & limitations

- **Container/leaf directives must be alone on their line.** Don't put text on the
  same line as a directive fence.
- **Nesting needs escalating colons** — the outer container always has more colons
  than the inner one, and a closing fence (`:::`) matches its opening colon count.
- **Inline directives require braces** — `:icon{…}`, `:badge{…}`, `:ref{…}`,
  `:anchor{…}`. `:icon` with no braces won't render.
- **Quoting:** use single quotes around a value that itself contains double quotes
  (the gallery JSON). Otherwise double quotes are conventional.
- **Every file must be in `local/pages/profile-content/index.json`**, and ordering comes from `rank`.
- **Keep sections about one screen tall** for the cleanest full-page snap scrolling.
  Sections grow automatically if their content is taller.
- A `{{variable}}` that isn't defined in frontmatter renders as empty.

---

## 11. A complete example file

`local/pages/profile-content/projects.md`:

````md
---
label: Projects
rank: 3
icon: rocket_launch
star: :icon{name="Star" fill="true" color="warning"}
---

# {{star}} Selected Projects
### Things I'm proud of

:::callout{type="info"}
These are a few highlights — see :ref{to="overview" label="the intro"} for context.
:::

::::columns
:::col
:::card{title="Vision Model" icon="visibility" href="https://github.com/you/vision"}
Real-time object detection in PyTorch.

Stack: :badge{label="PyTorch" color="danger"} :badge{label="OpenCV" color="info"}
:::
:::

:::col
:::card{title="Data Pipeline" icon="database"}
Batch + streaming ETL on the cloud.

Stack: :badge{label="SQL" color="tip"} :badge{label="Airflow" color="note"}
:::
:::
::::

## A short demo

```python
model = load_model("vision.pt")
print(model.predict(frame))
```

::gallery{images='["project-01.jpg", "project-02.jpg"]' width="640" align="center"}

::embed{type="youtube" id="jNQXAC9IVRw" title="Project walkthrough" width="560" align="center"}

::bookmark{href="https://github.com/you" title="More on GitHub" description="All my repositories." image="github-thumbnail.jpg"}

## Notes

:::toggle
## {{star}} Why this matters
A collapsible explanation goes here.
:::
````

Note how, in the columns example, `col` uses 3 colons, `columns` uses 4, and the card
inside each `col` uses 3 (it has no directive children of its own).

---

## 12. What is *not* content-editable

These are part of the app UI, not markdown — change them in code, not `local/pages/profile-content/`:

- **Your name** — `src/assets/name.svg` (rendered in `src/components/Sidebar.jsx`).
- **Avatar image** — served through the proxy in `src/components/Sidebar.jsx`.
- **Sidebar social links / résumé** — `src/components/Links.jsx`.
- **Theme colours, fonts, motion, accent & syntax palettes** — `src/index.css`
  (the `:root`, `.dark`, and `.light` blocks).
- **The list of sections** and their order — `local/pages/profile-content/index.json` + each file's `rank`.

---
