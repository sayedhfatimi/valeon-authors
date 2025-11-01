# Valeon Authors

This repository is the **authors submodule** for **[Valeon](https://valeon.blog)**. It stores each author's profile data and related media. It is **content-only** — no build/deploy logic, no Node/npm.  
**Posts in `valeon-posts` must reference an existing author entry from this repo.**

> 📌 **TL;DR**
> 1) Add an **author file** at `src/content/authors/[author-slug].json` (or `src/content/authors/[author-slug]/author.json`)  
> 2) Follow the **exact schema** (`name`, optional `email|bio|website|socials`, `publications[]`)  
> 3) Place **book covers** under the same author folder and reference via relative path in `publications[].cover`  
> 4) Open a PR. If a post depends on this profile, **link** the PR in `valeon-posts`

---

## 1) Where things live

The site’s Content Collections loader looks here:

```
src/content/
  authors/**/*.json
```

You can structure each author as either a **single file** or a **folder**:

```
# Option A: single file
src/content/authors/[author-slug].json

# Option B: folder (recommended when you have covers/assets)
src/content/authors/[author-slug]/
  author.json
  assets/
    books/
      [book-slug]/
        cover.jpg
```

> The **author slug** equals the **file or folder name**:  
> - `src/content/authors/[author-slug].json` → slug `[author-slug]`  
> - `src/content/authors/[author-slug]/author.json` → slug `[author-slug]`

---

## 2) Author schema (as implemented in `content.config.ts`)

```ts
type StoreLink = { name: string; url: string };

type Publication = {
  slug?: string;                 // optional custom slug
  title: string;                 // required
  subtitle?: string;
  slogan?: string;
  description: string;           // required (HTML allowed)
  pubDate: Date;                 // required (ISO accepted; coerced to Date)
  isbn?: string;
  cover?: Image;                 // optional; local image via Astro image()
  series?: { name: string; part?: number|string };
  stores?: {
    ebooks?: StoreLink[];
    print?: StoreLink[];
    audiobooks?: StoreLink[];
  };                             // default: {}
  reedsy?: string;               // url
  goodreads?: string;            // url
  audioSample?: string;          // url or relative path
};

type Author = {
  name: string;                  // required
  email?: string;                // optional
  bio?: string;                  // optional
  website?: string;              // url, optional
  socials?: {                    // all keys optional
    twitter?: string;            // url
    github?: string;             // url
    linkedin?: string;           // url
    bluesky?: string;            // url
    instagram?: string;          // url
  };
  publications: Publication[];   // default: []
};
```

**Dates:** `pubDate` accepts ISO 8601 strings (e.g. `"2025-04-10T09:00:00Z"`) and is coerced to `Date`. Invalid dates fail validation.  
**Images:** `cover` uses Astro’s `image()` validator. Use a **local relative path** from the author file (see examples).

---

## 3) Examples (JSON only)

### A) Single-file author

```jsonc
// src/content/authors/[author-slug].json
{
  "name": "[Public Name]",
  "email": "author@example.com",
  "bio": "Short 1–2 sentence bio.",
  "website": "https://example.com",
  "socials": {
    "twitter": "https://x.com/[handle]",
    "linkedin": "https://www.linkedin.com/in/[handle]"
  },
  "publications": [
    {
      "slug": "[book-slug]",
      "title": "[Book Title]",
      "subtitle": "[Optional Subtitle]",
      "slogan": "[Optional Slogan]",
      "description": "Short description of the book. HTML is allowed.",
      "pubDate": "2025-08-11T00:00:00Z",
      "isbn": "978-1-23456-789-7",
      "cover": "./assets/books/[book-slug]/cover.jpg",
      "series": { "name": "[Series Name]", "part": 1 },
      "stores": {
        "ebooks": [{ "name": "Amazon Kindle", "url": "https://amazon.example/[book]" }],
        "print": [{ "name": "Amazon Paperback", "url": "https://amazon.example/[book-paperback]" }],
        "audiobooks": [{ "name": "Audible", "url": "https://audible.example/[book]" }]
      },
      "goodreads": "https://www.goodreads.com/book/show/[id]",
      "reedsy": "https://reedsy.example/[book]",
      "audioSample": "https://media.example.com/audio/[book-slug]-sample.mp3"
    }
  ]
}
```

> When using the single-file layout, keep asset paths **relative** to the JSON file. If your images live in a sibling `assets/` folder, `./assets/...` is ideal.

---

### B) Folder-based author (recommended for assets)

```jsonc
// src/content/authors/[author-slug]/author.json
{
  "name": "[Public Name]",
  "bio": "Short 1–2 sentence bio.",
  "publications": [
    {
      "title": "[Book Title]",
      "description": "Short description of the book.",
      "pubDate": "2025-08-11T00:00:00Z",
      "cover": "./assets/books/[book-slug]/cover.jpg"
    }
  ]
}
```

**Folder structure**

```
src/content/authors/[author-slug]/
  author.json
  assets/
    books/
      [book-slug]/
        cover.jpg
```

This keeps `cover` paths short and portable (`./assets/...`).

---

## 4) Validation checklist (PR-ready)

```markdown
- [ ] JSON file at `src/content/authors/[author-slug].json` OR `src/content/authors/[author-slug]/author.json`.
- [ ] `[author-slug]` is lowercase-hyphenated (stable; do not rename once used).
- [ ] `name` present (required).
- [ ] If set, `email` is a valid email; `website` and `socials.*` are valid URLs.
- [ ] Each `publications[]` item has `title`, `description`, and valid `pubDate` (ISO string).
- [ ] `cover` (if set) points to a local image within the repo and path resolves.
- [ ] `stores.*[].url` values are valid URLs; `name` provided for each store link.
- [ ] No extra fields outside the schema (keep data clean and predictable).
```

---

## 5) How posts reference authors

In **valeon-posts**, the post frontmatter references this collection. Ensure the **author slug** here matches the value used there:

```yaml
author: [author-slug]
```

If the slug isn’t found in this repo, the build will fail or the site will reject the post.

---

## 6) Conventions & commits

**Branch names**
- New author: `chore/author: add-[author-slug]`
- Update author: `fix/author: [author-slug]`

**Commit messages**
- `chore(author): add [author-slug] profile`
- `fix(author): update links & cover paths for [author-slug]`
- `docs(authors): clarify publications schema`

---

## 7) What *not* to include

- No compiled assets, binaries, or giant PSD/AI files
- No secrets/tokens or private contact info
- No site build scripts or dependency files (keep this repo **content-only**)

---

## 8) Tips

- Keep `bio` short (140–240 chars).  
- Prefer the **folder layout** if you have multiple covers.  
- Use **ISO 8601 UTC** for dates (e.g., `"2025-04-10T09:00:00Z"`).  
- For series, `part` can be numeric or Roman (`1` or `"I"`).  
- `description` can contain simple HTML if needed.

---

Thanks for contributing to Valeon!
