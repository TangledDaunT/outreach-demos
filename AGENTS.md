# Codex Instructions for Demo Site Building

## Overview
You are building **static demo websites** for small business outreach. Each demo is a single folder under `/<slug>/` containing only static files. These demos are deployed to Render via git push.

## Critical Rules (MUST FOLLOW)

### Output Structure
- **One folder per lead**: `/<slug>/` (slug = lowercase business name, spaces→hyphens, special chars removed)
- **Files allowed**: `index.html`, `style.css` (optional), images as **inline SVG only** or free CDN
- **NO build step**, **NO backend**, **NO frameworks to install**
- **Tailwind via CDN is allowed** (use `<script src="https://cdn.tailwindcss.com"></script>`)

### Performance & Quality
- Mobile-first, **under 150 KB total**
- Lighthouse mobile performance target **90+**
- Accessible: alt text, contrast, labels, semantic HTML
- **No console errors**

### Content Rules - NEVER INVENT
Use ONLY facts from `lead.json`:
- Business name, phone, address, hours, services
- Public reviews summary (rating, count only - no quote text)
- **NEVER invent**: awards, years in business, prices, testimonials, staff names, certifications
- If data missing: use clear placeholders like "Your hours here" and **report them in output**

### Required Sections
1. **Hero**: Business name + primary CTA (Call / Book Now)
2. **Services**: List from lead.json
3. **Trust**: Only real data (rating, review count, years if known)
4. **Hours/Location**: With Google Maps link
5. **Contact**: Form using `mailto:` or form-to-email placeholder
6. **Footer**: Standard business info

### Mandatory Elements
- **Visible ribbon**: `Demo concept made for {Business Name}. Not an official site.`
- **Meta tag**: `<meta name="robots" content="noindex">`
- **JSON-LD**: `LocalBusiness` schema with all available data
- **SEO**: Title, meta description, Open Graph tags

### Template System
- Per-niche templates live in `templates/sites/<niche>/`
- **ALWAYS start from template** and fill it
- Do NOT redesign from scratch (burns quota)
- Template provides structure; you fill in lead-specific content

## Build Flow (per lead)

1. Read `lead.json` from the working directory
2. Load niche template from `templates/sites/<niche>/`
3. Generate `/<slug>/index.html` (and `style.css` if needed)
4. Run QA checks (see below)
5. Output the generated files

## QA Checks (Auto-fail if any)
- Placeholder text like `{{` or `}}` left in output
- Broken layout (overlapping elements, horizontal scroll on mobile)
- Console errors (JS errors, failed resources)
- Missing primary CTA (Call/Book button)
- Total size > 150 KB
- Lighthouse mobile performance < 90 (if measurable)

## Retry Policy
- Max **2 auto-fix retries** if QA fails
- After 2 failures: mark `demo_failed`, fall back to text-only pitch

## Example lead.json Structure
```json
{
  "name": "ABC Plumbing",
  "slug": "abc-plumbing",
  "niche": "plumbing",
  "phone": "+1-555-123-4567",
  "address": "123 Main St, Colorado Springs, CO 80901",
  "hours": "Mon-Fri 8am-6pm, Sat 9am-2pm, Sun Closed",
  "services": ["Drain Cleaning", "Pipe Repair", "Water Heater Install", "Emergency 24/7"],
  "rating": 4.7,
  "review_count": 127,
  "website": null,
  "domain": null,
  "email": "abcplumbing@example.com",
  "source": "overpass"
}
```

## Output Format
On success, print JSON to stdout:
```json
{
  "success": true,
  "slug": "abc-plumbing",
  "files_created": ["abc-plumbing/index.html", "abc-plumbing/style.css"],
  "placeholders_used": ["hours", "services"],
  "size_kb": 45
}
```

On failure:
```json
{
  "success": false,
  "error": "Description of failure",
  "retries_attempted": 2
}
```