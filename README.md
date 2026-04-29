# Blog Schema JSON-LD Skill for Claude

A Claude skill that generates complete, production-ready **JSON-LD schema markup** for blog posts — structured for SEO, GEO (Generative Engine Optimization), and rich results in Google Search.

---

## What It Does

This skill takes a blog post (URL or draft content) and outputs a single `<script type="application/ld+json">` block containing four schema types:

- **BlogPosting** — headline, author, dates, keywords, read time, and more
- **BreadcrumbList** — 3-level navigation trail for search engines
- **Organization** — publisher identity with logo and social links
- **FAQPage** — 6–9 Q&A pairs derived from the blog's section headings, eligible for Google's FAQ rich results

All four are assembled into one combined `@graph` block — ready to paste directly into your CMS.

---

## Why JSON-LD Schema Matters

- Helps Google understand your content structure and display **rich results**
- Strengthens **E-E-A-T signals** (Experience, Expertise, Authoritativeness, Trustworthiness)
- Makes your content more discoverable by **AI crawlers and LLMs** (GEO)
- Improves click-through rates with FAQ snippets and breadcrumb trails in SERPs

---

## How to Install

1. Download [`blog-schema-jsonld.skill`](./blog-schema-jsonld.skill)
2. Go to [claude.ai](https://claude.ai) → Settings → Skills
3. Upload the `.skill` file
4. The skill is now active in your Claude workspace

---

## How to Use

Once installed, just share a blog post with Claude — either a live URL or a draft — and say:

> *"Generate schema markup for this blog"*

Claude will ask for a few details (publish date, author job title) and output the complete `<script>` block.

### What Claude will need from you:
- `datePublished` — exact date and time from your CMS
- `dateModified` — date and time of the last update
- Author job title (if not visible on the page)

---

## Output Format

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@graph": [
    { BlogPosting },
    { BreadcrumbList },
    { Organization },
    { FAQPage }
  ]
}
</script>
```

Paste this into your CMS's `<head>` section or custom HTML/script field.

---

## After Adding to CMS

✅ Validate using [Google Rich Results Test](https://search.google.com/test/rich-results)  
✅ Confirm the banner image URL is correct once the blog is live  
✅ If schema doesn't appear in View Source immediately, it may be CDN cache lag — Google will still detect it from the server

---

## About

Built by **Supraja Gayathri S** — [GitHub](https://github.com/Supraja-Gayathri-S)

---

*Licensed under [MIT](./LICENSE)*
