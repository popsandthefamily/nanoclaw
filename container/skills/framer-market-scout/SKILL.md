---
name: framer-market-scout
description: Monitor the Framer Templates Marketplace for gaps, trends, pricing, and opportunities across target template categories.
---

# Framer Market Scout Agent

## Purpose
Monitor the Framer Templates Marketplace for gaps, trends, and opportunities. Track which template categories are underserved, what's selling well, and where to position new templates for maximum visibility. Also monitors Webflow marketplace for cross-platform intelligence.

## When to Use
- User says "scout Framer" or "check Framer marketplace"
- User says "what should I build next on Framer"
- User says "Framer competition check"
- Scheduled via cron: weekly scan every Monday at 6:00 AM CT

## Target Categories to Monitor

### Primary (Build templates for these)
1. **Short-term rental / vacation rental** — Direct booking sites for cabin owners, STR operators
2. **Tourism / local directory** — Business listing directories, local guides, visitor bureaus
3. **Photography portfolio** — Photographer booking sites, gallery showcases
4. **Property management** — PM company sites, owner portals, guest resources
5. **Restaurant / food business** — Menu sites, catering, food trucks

### Secondary (Track for opportunity signals)
6. **Real estate** — Agent sites, listing pages, investor landing pages
7. **Construction / trades** — Contractor sites, project showcases
8. **Wellness / spa** — Booking sites for retreats, spas, wellness centers
9. **Campground / RV park** — Reservation and info sites
10. **Wedding venue** — Venue showcase and inquiry sites

## Workflow

### Weekly Marketplace Scan

1. **Navigate to Framer Marketplace** (https://www.framer.com/marketplace/templates/)

2. **For each target category**, search and record:
   - Total number of templates matching the category
   - Top 5 templates by apparent popularity (featured, reviews, etc.)
   - Price range (min, median, max)
   - Quality assessment (1-5 scale based on screenshot/preview):
     - Design polish
     - Mobile responsiveness shown in preview
     - Completeness (multi-page vs single page)
     - Industry appropriateness
   - Creator names (track who the repeat players are)

3. **Search these specific terms** and record result count:
   - "vacation rental"
   - "airbnb"
   - "cabin"
   - "tourism"
   - "restaurant"
   - "photography"
   - "property management"
   - "real estate"
   - "wedding"
   - "directory"
   - "listing"
   - "booking"

4. **Check "New" and "Popular" sections** for any templates in target categories

5. **Framer Creator Program page** (https://www.framer.com/creators/) — check for any program changes, new commission structures, or featured creator spotlights

### Cross-Platform Intelligence

Also check:
- **Webflow marketplace** — Same categories. What exists on Webflow that doesn't exist on Framer? These are immediate opportunities.
- **Squarespace templates** — What industries does Squarespace serve that Framer doesn't have templates for?
- **Wix templates** — Same analysis

### Opportunity Scoring

For each category, calculate an opportunity score:

```
Opportunity Score = (Search Demand × 3) + (Gap Size × 4) + (Price Potential × 2) + (Your Expertise × 1)

Search Demand (1-10): How often would someone search for this type of template?
Gap Size (1-10): How few quality templates exist? (10 = almost none)
Price Potential (1-10): Can you charge $59+ for this? (based on complexity and buyer type)
Your Expertise (1-10): How well do you know this industry's needs?
```

### Weekly Report Format

Save to `~/Documents/framer-intel/weekly/[date]-framer-report.md`

```markdown
# Framer Marketplace Intelligence — [Date]

## Category Snapshot

| Category | Templates Found | Avg Price | Quality (1-5) | Opportunity Score |
|----------|----------------|-----------|---------------|-------------------|
| Vacation Rental | 12 | $59 | 3.2 | 87/100 |
| Tourism Directory | 4 | $69 | 2.8 | 92/100 |
| Photography | 45 | $49 | 3.8 | 61/100 |
| Property Management | 2 | $55 | 2.0 | 95/100 |
| Restaurant | 28 | $49 | 3.5 | 68/100 |

## Top Opportunities (Score > 80)
1. **Property Management** (95) — Only 2 templates, both low quality. 
   You run Frontier PM. Build this immediately.
2. **Tourism Directory** (92) — 4 templates, none with proper listing/directory 
   functionality. Your Hocha.town experience is the blueprint.
3. **Vacation Rental** (87) — 12 templates but most are generic portfolios, 
   not purpose-built for STR direct booking.

## New Templates This Week
- [Creator] launched [template name] in [category] at $[price]
- [Analysis: threat level, quality assessment, differentiation]

## Pricing Intelligence
- Highest-priced template this week: $[X] — [name] by [creator]
- New pricing trend: [any shifts in median pricing]

## Webflow Cross-Reference
Templates that exist on Webflow but NOT on Framer:
- [Category]: [X templates on Webflow, 0 on Framer]
- Opportunity: Port the concept to Framer

## Recommended Next Build
Based on this week's data, your next Framer template should be:
**[Category]** — [Specific template concept]
- Gap size: [X]
- Comparable Webflow templates priced at: $[X]
- Estimated build time: [X days]
- Unique angle: [What makes yours different]
```

## On-Demand Commands

**"Scout Framer [category]"** — Deep dive on one specific category

**"Compare Framer vs Webflow for [category]"** — Side-by-side template availability

**"Who's winning on Framer?"** — Top 10 creators by template count and estimated sales

**"Framer pricing for [category]"** — Price analysis with recommendation for your template

**"Framer Partner Program update"** — Check current commission rates and any program changes

## Data Storage

```
~/Documents/framer-intel/
├── weekly/
│   └── [date]-framer-report.md
├── categories/
│   └── [category-name].csv          # Historical template count tracking
├── competitors/
│   └── [creator-name].md            # Profile on active Framer creators
├── cross-platform/
│   └── [date]-webflow-comparison.md
└── opportunities/
    └── build-queue.md                # Prioritized list of templates to build
```

## Build Queue Management

Maintain a running build queue at `~/Documents/framer-intel/opportunities/build-queue.md`:

```markdown
# Framer Template Build Queue

## Next Up
1. [ ] Property Management Company ($69) — Opp Score: 95
   - Est. build: 5-7 days
   - Key pages: Services, Properties, Owner Portal, Guest Resources, Team
   - Differentiator: Built by someone who actually runs a PM company

## In Progress
- [ ] STR Direct Booking ($69) — Started [date]

## Completed
- [x] STR Direct Booking ($69) — Launched [date], [X] sales in first month

## Ideas (Not Yet Scored)
- Campground/RV park booking site
- Wedding venue showcase
- Coworking space
```

## Quality Rules
- Never interact with competitor templates beyond viewing public previews
- Track data over time to identify trends, not just snapshots
- Flag when Framer changes marketplace features, search algorithm, or creator terms
- Always cross-reference Webflow — the platforms share buyer demographics
- Prioritize categories where you have real industry experience (STR, photography, PM, tourism)
