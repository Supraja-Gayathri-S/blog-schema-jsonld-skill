---
name: blog-schema-jsonld
description: Generates complete, CMS-ready JSON-LD schema markup for any blog post on any website. Use this skill whenever a user provides a blog URL, blog draft content, or asks to create schema markup, structured data, or JSON-LD for a blog post. Also triggers when the user mentions "schema for this blog", "generate schema", "schema markup", "rich results", "structured data", or "SEO schema". Works for any blog platform (WordPress, custom CMS, Drupal, etc.) and any organization. All details like author, publisher, dates, and organization info are collected from the user or extracted from the page.
---

# Blog Schema Generator

Generates complete, production-ready JSON-LD schema markup for any blog post — including BlogPosting, BreadcrumbList, Organization, and FAQPage — as a single `<script type="application/ld+json">` block ready to paste into any CMS.

---

## Workflow

### Step 1 — Gather inputs

**If the user provides a live URL:**
Fetch the page and auto-extract as many fields as possible. Then ask only for what's missing.

**If the user provides a draft or pasted content:**
Extract what you can from the content, then ask for the rest.

**Never guess or use placeholders silently.** If a required field is missing, ask for it clearly before generating.

---

#### Fields to auto-extract from the page/draft:

- **Headline** — exact H1 text (never the browser tab title, which often appends the site name)
- **Description** — 1–2 sentence summary from the opening paragraph (not the meta title)
- **Blog URL** — full canonical URL
- **Author name** — as shown on the byline
- **Author profile URL** — link to the author's page if present
- **Article tags / keywords** — from tag/category labels on the page
- **Blog banner image URL** — the featured/hero image
- **Read time** — if shown (e.g. "5 Min Read" → `PT5M`)
- **Blog section / category** — e.g. "General", "Engineering", "Product"
- **Blog series name and URL** — the parent blog listing page

---

#### Fields to always ask the user for:

These are rarely available on the page and must come from the user:

| Field | What to ask |
|---|---|
| `datePublished` | "What is the published date and time? (Check your CMS 'Authored on' or 'Published' field)" |
| `dateModified` | "Was this post updated after publishing? If yes, what date and time?" |
| Author job title | "What is the author's job title?" |
| Author profile URL | Ask if not found on the page |
| Publisher / organization name | "What is the name of the organization or company publishing this blog?" |
| Publisher website URL | "What is the organization's website URL?" |
| Publisher logo URL | "Do you have a direct URL to the organization's logo image (.png or .jpg)?" |
| Organization social profiles | "Do you have links to the organization's social media profiles? (Twitter/X, LinkedIn, Facebook, etc.)" |
| Timezone | "What timezone should the dates use?" (default to UTC if unsure) |

Ask all missing fields in a single message — do not ask one at a time.

---

### Step 2 — Build the four schema types

Always combine all four into a single `@graph`. Never output them as separate blocks.

---

#### 1. BlogPosting

```json
{
  "@type": "BlogPosting",
  "@id": "{canonical URL}#blogpost",
  "headline": "{exact H1 — not browser tab title}",
  "description": "{1–2 sentence summary from opening paragraph}",
  "url": "{canonical URL}",
  "datePublished": "{YYYY-MM-DDTHH:MM:SS+TZ:TZ}",
  "dateModified": "{YYYY-MM-DDTHH:MM:SS+TZ:TZ}",
  "inLanguage": "{language code, e.g. en-US}",
  "timeRequired": "PT{N}M",
  "author": {
    "@type": "Person",
    "name": "{author full name}",
    "url": "{author profile URL}",
    "jobTitle": "{author job title}",
    "worksFor": { "@id": "{publisher website URL}/#organization" }
  },
  "publisher": { "@id": "{publisher website URL}/#organization" },
  "image": {
    "@type": "ImageObject",
    "url": "{banner image URL — must be a direct image file, not a webpage}"
  },
  "mainEntityOfPage": {
    "@type": "WebPage",
    "@id": "{canonical URL}"
  },
  "articleSection": "{section/category name}",
  "keywords": ["{tag1}", "{tag2}", "{tag3}"],
  "isPartOf": {
    "@type": "Blog",
    "name": "{blog series/listing name}",
    "url": "{blog listing URL}"
  },
  "about": [
    { "@type": "Thing", "name": "{primary topic}" },
    { "@type": "Thing", "name": "{secondary topic}" }
  ]
}
```

**Key rules:**
- `headline` must be the page H1 exactly — browser tab titles often append the site name (e.g. "| Company Name") and must not be used
- `author @type` is always `Person` — never `Organization`, even if the name sounds like a team
- `publisher` uses only an `@id` reference — the full org object lives in the Organization block
- `timeRequired` format: "5 Min Read" → `PT5M`, "1 Hour Read" → `PT60M`
- `image.url` must be a direct image file URL ending in `.png`, `.jpg`, `.webp`, etc. — never a webpage URL
- If `datePublished` == `dateModified`, only use the same value if the user confirms no update occurred

---

#### 2. BreadcrumbList

Reflect the actual navigation path to the article. Ask the user if the site structure is unclear.

```json
{
  "@type": "BreadcrumbList",
  "itemListElement": [
    {
      "@type": "ListItem",
      "position": 1,
      "name": "{root/home label, e.g. 'Home' or 'Blog'}",
      "item": "{root URL}"
    },
    {
      "@type": "ListItem",
      "position": 2,
      "name": "{category/section name}",
      "item": "{category URL}"
    },
    {
      "@type": "ListItem",
      "position": 3,
      "name": "{article headline}",
      "item": "{canonical URL}"
    }
  ]
}
```

If the blog has only two levels (root → article), use two items. Match the actual site navigation — do not invent levels.

---

#### 3. Organization

```json
{
  "@type": "Organization",
  "@id": "{publisher website URL}/#organization",
  "name": "{organization name}",
  "url": "{publisher website URL}",
  "logo": {
    "@type": "ImageObject",
    "url": "{logo image URL — must be a direct image file}",
    "width": {logo width in px},
    "height": {logo height in px}
  },
  "sameAs": [
    "{Twitter/X URL}",
    "{LinkedIn URL}",
    "{Facebook URL}",
    "{YouTube URL}"
  ]
}
```

If the user doesn't know logo dimensions, omit `width` and `height` rather than guessing. Only include `sameAs` entries the user actually provides.

---

#### 4. FAQPage

Derive 6–9 Q&A pairs from the blog's section headings and key content.

```json
{
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "{question a user would actually search for}",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "{concise, self-contained answer — 2–4 sentences}"
      }
    }
  ]
}
```

**FAQ guidelines:**
- Derive questions from section headings and key concepts in the blog
- Each answer must stand alone — do not reference "the article" or "as mentioned above"
- Cover: what it is, why it matters, how it works, who benefits, practical steps, common questions
- For how-to content: include step-by-step or tool-specific questions
- For product/feature posts: include "what is X", "how does X work", "how do I get started" questions
- Aim for 6–9 pairs — enough for Google rich results without bloating the schema

---

### Step 3 — Assemble and output

Output a **single code block** — the complete `<script>` tag wrapping the full `@graph`. Always include the opening and closing `<script>` tags. Never split into tabs, sections, or separate blocks unless the user explicitly requests it.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@graph": [
    { ...BlogPosting... },
    { ...BreadcrumbList... },
    { ...Organization... },
    { ...FAQPage... }
  ]
}
</script>
```

---

## Date and timezone rules

| Situation | Action |
|---|---|
| User provides date only (e.g. "April 22, 2026") | Ask for the time before generating — never silently use `T00:00:00` |
| User says "today" | Use today's actual date with the time the user provides |
| User says "updated today" without a time | Ask for the exact time |
| No update after publishing | Set `dateModified` == `datePublished` and confirm with user |
| Time from a CMS screenshot | Use exactly as shown; convert PM/AM to 24hr format |
| Timezone unknown | Default to UTC (`+00:00`) and tell the user to update it |

Always use the full ISO 8601 format: `YYYY-MM-DDTHH:MM:SS±HH:MM`
Example: `2026-04-22T14:00:00+05:30`

---

## Common mistakes to avoid

| Mistake | Correct approach |
|---|---|
| `headline` taken from the browser tab title | Use the page H1 only |
| `author @type: "Organization"` for a person | Always `@type: "Person"` for named individual authors |
| `publisher.logo.url` pointing to a webpage | Must be a direct image file URL (`.png`, `.jpg`, `.svg`) |
| Date without time: `"2026-04-22"` | Always include time and timezone offset |
| Schema output without `<script>` tags | Always wrap in `<script type="application/ld+json">` |
| Multiple separate schema blocks | Always one `@graph` block combining all types |
| Inventing breadcrumb levels that don't exist | Match actual site navigation — ask user if unsure |
| Adding `sameAs` links the user didn't provide | Only include social URLs explicitly confirmed by user |

---

## Post-generation checklist

After generating, remind the user to:

1. **Verify image URL** — confirm the banner image filename is correct, especially for blogs not yet live
2. **Confirm placeholder times** — if any time was estimated, update before publishing
3. **Validate** — paste into [Google Rich Results Test](https://search.google.com/test/rich-results) after adding to CMS
4. **Check source** — right-click → View Page Source → search `application/ld+json` to confirm it's live
5. **Cache note** — if the schema doesn't appear in View Source right away, it's a CDN cache lag. Google and LLM crawlers fetch server-side and will detect the schema regardless. Cache typically clears within a few hours.

---

## Handling updates to existing schema

If the user shares an existing schema block for correction or update:

1. Read it carefully and identify all issues before responding
2. Flag errors explicitly (wrong `@type`, invalid URLs, missing fields, bad date format)
3. Output the corrected full block — never just show a diff unless the user asks
4. Explain each change made so the user understands what was wrong
