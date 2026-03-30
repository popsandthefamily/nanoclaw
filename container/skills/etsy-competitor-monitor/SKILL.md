---
name: etsy-competitor-monitor
description: Monitor Etsy search results, competitor listings, pricing, and market gaps across target niches with daily and weekly automated scans.
---

# Etsy Competitor Monitor Agent

## Purpose
Monitor Etsy search results, competitor listings, pricing, reviews, and market gaps across target niches. Provide actionable intelligence for pricing, SEO optimization, and new product opportunities. Covers all product verticals in the execution plan: trade contractor bundles, STR spreadsheets, real estate investor tools, and photography presets.

## When to Use
- User says "check Etsy competition" or "Etsy scout"
- User says "how are my listings doing" or "check my Etsy"
- User says "find gaps in [niche]" or "what's selling in [category]"
- Scheduled via cron: daily check on own listings, weekly deep competitor scan

## Target Niches to Monitor

### Niche 1: Trade Contractor Bundles
**Search terms to track:**
- "landscaping estimate template"
- "landscaping business starter kit"
- "lawn care invoice template"
- "HVAC estimate template"
- "plumbing invoice template"
- "electrical contractor template"
- "contractor business bundle"
- "cleaning business template"

### Niche 2: STR / Airbnb Spreadsheets
**Search terms to track:**
- "Airbnb tax calculator"
- "short term rental spreadsheet"
- "vacation rental tax calculator"
- "STR investment calculator"
- "Airbnb host spreadsheet"
- "rental property tax worksheet"

### Niche 3: Real Estate Investor Tools
**Search terms to track:**
- "rental property analysis spreadsheet"
- "BRRRR calculator spreadsheet"
- "real estate investment spreadsheet"
- "Airbnb investment calculator"
- "rental property deal analyzer"
- "real estate pro forma template"

### Niche 4: Photography Presets
**Search terms to track:**
- "real estate photography presets"
- "cabin photography Lightroom presets"
- "elopement Lightroom presets"
- "outdoor wedding presets"
- "interior photography presets"
- "real estate HDR presets"

## Workflow

### Daily Scan (Automated — Own Listings)
Run every morning at 7:00 AM CT.

1. **Check own listing stats** (if Etsy API access is configured):
   - Views, favorites, orders in last 24h
   - Any new reviews (flag negative reviews immediately)
   - Listing quality score changes
   - Search ranking position for primary keywords

2. **Quick competitor check:**
   - For each niche, search the top 3 keywords
   - Record the #1 ranked listing: title, price, review count, star rating
   - Flag if a new competitor has appeared in the top 5
   - Flag if any top competitor changed their price

3. **Send morning briefing** via chat:
   ```
   📊 Etsy Daily Brief - March 28, 2026
   
   YOUR LISTINGS:
   - Landscaping Bundle: 12 views, 2 favorites, 0 orders
   - STR Tax Calculator: 28 views, 5 favorites, 1 order ($39)
   
   COMPETITOR MOVES:
   - ⚠️ New competitor in "landscaping estimate template" — "GreenBizTemplates" 
     listed at $24.99 (your price: $29). 4.8 stars, 12 reviews.
   - No changes in STR spreadsheet competitors
   
   ACTION ITEMS:
   - Consider matching $24.99 or adding a bonus item to justify premium
   ```

### Weekly Deep Scan (Automated — Sundays 8:00 AM CT)
Full competitive analysis across all niches.

For each search term in each niche:

1. **Search Etsy** for the term
2. **Record top 20 results:**
   - Listing title
   - Seller name
   - Price
   - Number of reviews
   - Star rating
   - Number of sales (if visible)
   - Whether it's an Etsy Ads result (sponsored)
   - Listing description keywords (first 200 chars)
   - Number of images
   - File format offered (PDF, Canva, Google Sheets, Excel, etc.)

3. **Analyze patterns:**
   - Average price across top 20
   - Price range (min/max)
   - Average review count
   - Most common file formats offered
   - Keywords appearing in 50%+ of titles
   - Keywords appearing in top 5 but NOT top 20 (differentiators)
   - Gaps: what's searched for but has few quality results?

4. **Save structured data:**
   ```
   ~/Documents/etsy-intel/weekly/[date]-[niche].csv
   ```
   Columns: search_term, rank, listing_id, seller, title, price, reviews, stars, format, sponsored, url

5. **Generate weekly report:**
   ```
   ~/Documents/etsy-intel/weekly/[date]-weekly-report.md
   ```

### Weekly Report Format

```markdown
# Etsy Competitive Intelligence — Week of [date]

## Market Summary

| Niche | Avg Price | Your Price | Your Rank | Top Competitor | Gap |
|-------|-----------|-----------|-----------|----------------|-----|
| Landscaping Bundle | $22.50 | $29.00 | #8 | GreenBiz ($18) | Premium justified by completeness |
| STR Tax Calc | $31.00 | $39.00 | #3 | TaxEasySheets ($27) | You have more markets covered |
| RE Deal Analyzer | $35.00 | $29.00 | #5 | InvestorTools ($49) | Underpriced vs competition |

## New Competitors This Week
- [seller]: [product] at [price] — [threat level: LOW/MED/HIGH]

## Keyword Opportunities
Search terms with high search suggestion frequency but fewer than 10 quality results:
- "glamping business template" — only 4 relevant results, avg price $19
- "cabin rental pricing spreadsheet" — 6 results, avg price $24

## Price Optimization Suggestions
- RE Deal Analyzer: You're $6 below market average. Test $34.99.
- Landscaping Bundle: Market is compressing. Hold at $29 but add 
  "includes pricing calculator" to title to justify premium.

## Review Analysis
- Competitors with negative reviews mentioning: [common complaints]
  → Opportunity: address these complaints in your listing description
```

## On-Demand Commands

**"Scout [keyword]"** — Immediately search Etsy for that term, return top 10 with prices and review counts.

**"Price check [niche]"** — Return current average pricing across all tracked terms in that niche.

**"New in [niche]"** — Show only listings that appeared in the last 7 days.

**"Review mine"** — Pull all reviews on your listings, highlight any below 4 stars.

**"Trending"** — Check Etsy trending/popular searches relevant to your niches.

## Data Storage

```
~/Documents/etsy-intel/
├── daily/
│   └── [date]-brief.md
├── weekly/
│   ├── [date]-landscaping.csv
│   ├── [date]-str-tax.csv
│   ├── [date]-re-investor.csv
│   ├── [date]-presets.csv
│   └── [date]-weekly-report.md
├── competitors/
│   └── [seller-name].md          # Profile on each key competitor
├── keywords/
│   └── keyword-tracking.csv       # Historical rank tracking
└── alerts/
    └── alert-log.md               # All alerts sent
```

## Error Handling
- If Etsy blocks or rate-limits, wait 60 seconds and retry with a different user agent
- If a listing is no longer available, mark as "DELISTED" and note the date
- If search returns fewer than 5 results, try alternate keyword variations
- Never interact with competitor listings (no clicking "favorite," no adding to cart)

## Privacy and Ethics
- Only collect publicly visible information from Etsy search results
- Do not attempt to access competitor analytics, private data, or Etsy internal metrics
- Do not automate purchases, reviews, or any interaction that simulates a buyer
- This is market research using publicly available data only
