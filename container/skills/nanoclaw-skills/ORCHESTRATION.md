# Agent Orchestration — Master Setup Guide
## OpenClaw / NanoClaw Configuration for Digital Product Business

---

## Overview

Five agents working together to automate the research, monitoring, content creation, and intelligence gathering for your digital product portfolio across Etsy, Framer, Whop, and other marketplaces.

| Agent | Purpose | Schedule | Priority |
|-------|---------|----------|----------|
| STR Tax Researcher | Research and compile tax rates for 50+ STR markets | On-demand + quarterly refresh | HIGH — builds the core product |
| Etsy Competitor Monitor | Track competitors, pricing, reviews, keyword rankings | Daily brief + weekly deep scan | HIGH — ongoing intelligence |
| Content Prep Bot | Transform raw data into product-ready content | On-demand (triggered after research) | MEDIUM — accelerates production |
| Etsy Listing Generator | Write optimized titles, descriptions, tags for Etsy | On-demand (when launching products) | MEDIUM — one-time per product |
| Framer Market Scout | Monitor Framer marketplace gaps and opportunities | Weekly scan | LOW — strategic planning |
| Brand Asset Engine | Generate mockups, Etsy images, PDFs, social graphics | On-demand (product launches) | HIGH — visual pipeline |
| Account Manager | Handle logins, signups, credential storage via Keychain | On-demand (when blocked) | HIGH — unblocks everything |

---

## Directory Structure

Create this folder structure before installing skills:

```bash
mkdir -p ~/Documents/{str-tax-data,etsy-intel/{daily,weekly,competitors,keywords,alerts},product-content/{sheets-data,templates/{landscaping,hvac,plumbing,electrical},social-media,preset-descriptions,ready-to-use},framer-intel/{weekly,categories,competitors,cross-platform,opportunities}}
```

---

## Cron Schedule (OpenClaw)

### MCP Server Configuration

Add these to your OpenClaw or NanoClaw MCP config for mockup generation:

```json
{
  "mcpServers": {
    "dynamic-mockups": {
      "command": "npx",
      "args": ["-y", "@dynamic-mockups/mcp"],
      "env": {
        "DYNAMIC_MOCKUPS_API_KEY": "YOUR_KEY_HERE"
      }
    },
    "mockuuups": {
      "url": "https://api.mockuuups.studio/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_KEY_HERE"
      }
    }
  }
}
```

### Cron Jobs

Add to your OpenClaw cron configuration:

```json
{
  "crons": [
    {
      "name": "etsy-daily-brief",
      "schedule": "0 7 * * *",
      "prompt": "Run the Etsy Competitor Monitor daily scan. Check my listing stats and top 3 competitors per niche. Send the morning briefing.",
      "channel": "whatsapp"
    },
    {
      "name": "etsy-weekly-deep-scan",
      "schedule": "0 8 * * 0",
      "prompt": "Run the Etsy Competitor Monitor weekly deep scan across all niches. Generate the full weekly report with pricing analysis and keyword opportunities.",
      "channel": "whatsapp"
    },
    {
      "name": "framer-weekly-scout",
      "schedule": "0 6 * * 1",
      "prompt": "Run the Framer Market Scout weekly scan. Check all target categories, compare with Webflow, score opportunities, and update the build queue.",
      "channel": "whatsapp"
    },
    {
      "name": "str-tax-quarterly-refresh",
      "schedule": "0 9 1 1,4,7,10 *",
      "prompt": "Run the STR Tax Researcher quarterly refresh. Re-research all markets in the master file, flag any rate changes, and send me a summary of what changed.",
      "channel": "whatsapp"
    }
  ]
}
```

### NanoClaw Equivalent (HEARTBEAT.md)

If using NanoClaw, add scheduled tasks to your HEARTBEAT.md:

```markdown
# Scheduled Tasks

## Every day at 7:00 AM CT
Run the Etsy Competitor Monitor daily scan. Check listing stats and send morning briefing via WhatsApp.

## Every Sunday at 8:00 AM CT
Run the full Etsy Competitor Monitor weekly deep scan. Save report to ~/Documents/etsy-intel/weekly/

## Every Monday at 6:00 AM CT
Run the Framer Market Scout weekly marketplace scan. Update the build queue.

## First day of Jan, Apr, Jul, Oct at 9:00 AM CT
Run the STR Tax Researcher quarterly refresh across all markets.
```

---

## Agent Chaining (How They Work Together)

### Chain 1: New Market Research → Product Content
```
You: "Add Fredericksburg TX to the tax calculator"
    ↓
[STR Tax Researcher] → Researches all tax components for Fredericksburg
    ↓
[Content Prep Bot] → Formats data into Google Sheets-ready rows and formulas
    ↓
Output: CSV row ready to paste + formatted market tab content
```

### Chain 2: New Product Launch
```
You: "Launch the HVAC contractor bundle on Etsy"
    ↓
[Content Prep Bot] → Generates estimate, invoice, agreement content for HVAC
    ↓
[Etsy Listing Generator] → Writes optimized title, description, 13 tags
    ↓
[Etsy Competitor Monitor] → Baseline competitor snapshot for HVAC niche
    ↓
Output: Complete listing content + competitive context for pricing
```

### Chain 3: Weekly Intelligence Loop
```
[Cron: Sunday 8AM] → Etsy deep scan runs automatically
[Cron: Monday 6AM] → Framer scout runs automatically
    ↓
You check WhatsApp Monday morning for both reports
    ↓
You: "Interesting. Write a listing for that glamping template gap you found"
    ↓
[Etsy Listing Generator] → New listing content
```

### Chain 4: Full Product Launch (End-to-End)
```
You: "Full launch kit for the landscaping bundle"
    ↓
[Content Prep Bot] → Generates all document content (estimates, invoices, SOPs)
    ↓
[Etsy Listing Generator] → Writes listing title, description, 13 tags
    ↓
[Brand Asset Engine] → Screenshots product → generates 4 device mockups via MCP
    ↓
[Brand Asset Engine] → Renders 6 branded infographic images (What's Included, How It Works, etc.)
    ↓
[Brand Asset Engine] → Creates branded Quick Start Guide PDF
    ↓
[Brand Asset Engine] → Generates social media announcement images
    ↓
[Etsy Competitor Monitor] → Baseline competitor snapshot for pricing context
    ↓
Output: Complete folder with product files + listing copy + 10 Etsy images + PDF guide + social images
```

### Chain 5: Brand Refresh
```
You: "I updated the logo and colors in BRAND.json"
    ↓
[Brand Asset Engine] → "Refresh all Etsy images" — regenerates all listing images for all products
    ↓
[Brand Asset Engine] → Regenerates all PDF documents with new branding
    ↓
Output: Every visual asset updated to new brand, ready to re-upload
```

---

## Skill Installation

### OpenClaw
Copy skill folders to your OpenClaw workspace:

```bash
cp -r skills/str-tax-researcher ~/.openclaw/workspace/skills/
cp -r skills/etsy-competitor-monitor ~/.openclaw/workspace/skills/
cp -r skills/content-prep-bot ~/.openclaw/workspace/skills/
cp -r skills/etsy-listing-generator ~/.openclaw/workspace/skills/
cp -r skills/framer-market-scout ~/.openclaw/workspace/skills/
cp -r skills/brand-asset-engine ~/.openclaw/workspace/skills/
cp -r skills/account-manager ~/.openclaw/workspace/skills/
cp skills/BRAND.json ~/Documents/brand/BRAND.json
```

### NanoClaw
Copy skill folders to your NanoClaw workspace:

```bash
cp -r skills/str-tax-researcher ~/nanoclaw/workspace/skills/
cp -r skills/etsy-competitor-monitor ~/nanoclaw/workspace/skills/
cp -r skills/content-prep-bot ~/nanoclaw/workspace/skills/
cp -r skills/etsy-listing-generator ~/nanoclaw/workspace/skills/
cp -r skills/framer-market-scout ~/nanoclaw/workspace/skills/
cp -r skills/brand-asset-engine ~/nanoclaw/workspace/skills/
cp -r skills/account-manager ~/nanoclaw/workspace/skills/
cp skills/BRAND.json ~/Documents/brand/BRAND.json
```

---

## Quick Command Reference

Send these via WhatsApp/Telegram to trigger skills:

### Tax Research
- `"Research taxes for Destin Florida"` — Single market
- `"Do all 50 markets"` — Full run
- `"Update taxes"` — Quarterly refresh
- `"Add [city] to the tax sheet"` — New market addition

### Etsy Monitoring
- `"Etsy scout"` — Run daily brief now
- `"Check Etsy competition"` — Deep scan now
- `"Scout landscaping templates"` — Single niche
- `"Price check STR spreadsheets"` — Pricing analysis
- `"What's new in real estate investor tools?"` — Recent competitor activity

### Content Prep
- `"Prep the data"` — Format latest research for Sheets
- `"Build HVAC bundle content"` — Generate all document content for HVAC
- `"Make social posts for landscaping bundle"` — 10 Instagram/Facebook posts
- `"Write preset descriptions for cabin photography pack"` — Product copy

### Etsy Listings
- `"Write Etsy listing for STR tax calculator"` — Full listing content
- `"Tags for BRRRR calculator"` — 13 optimized tags
- `"A/B title for landscaping bundle"` — Two title variations to test
- `"Seasonal refresh"` — Update listings for current season

### Framer
- `"Scout Framer"` — Full weekly scan now
- `"Scout Framer property management"` — Single category deep dive
- `"Compare Framer vs Webflow for restaurants"` — Cross-platform gap analysis
- `"What should I build next on Framer?"` — Top opportunity recommendation

### Brand Assets & Mockups
- `"Generate Etsy images for landscaping bundle"` — Full 10-image set for one product
- `"Mockup the STR tax calculator on a MacBook"` — Single device mockup
- `"Make mockups for [product]"` — Standard 4-device mockup set
- `"Create PDF for landscaping quick start guide"` — Branded PDF document
- `"Social images for STR tax calculator"` — Instagram, Facebook, Pinterest images
- `"Hero image for BRRRR calculator"` — Product page banner
- `"Full launch kit for [product]"` — Everything: mockups + Etsy images + PDF + social
- `"Refresh all Etsy images"` — Regenerate all listing images (after brand update)
- `"Export brand kit"` — ZIP of all brand assets for reference

### Accounts & Credentials (responses via Telegram)
- `"Sign up for Etsy seller account"` — Creates account, stores creds in Keychain
- `"Log into Gumroad"` — Retrieves creds from Keychain, authenticates
- `"List all stored accounts"` — Shows which services have stored credentials
- `"Create accounts for all marketplace channels"` — Batch signup for Etsy, Gumroad, FilterGrade, etc.

---

## Security Notes

- All agents only access publicly available information (Etsy search results, Framer marketplace, government tax websites)
- No agents log into any accounts on your behalf
- No agents make purchases, leave reviews, or simulate buyer behavior
- Tax research comes from .gov sources — agent never fabricates rates
- If using NanoClaw, each agent runs in its own container with filesystem isolation
- API keys (for any integrations) should be stored in environment variables, never in SKILL.md files

---

## Estimated API Costs

Based on typical usage with Claude as the model:

| Agent | Frequency | Est. Tokens/Run | Monthly Cost |
|-------|-----------|-----------------|--------------|
| STR Tax Researcher | On-demand (~4x/mo) | ~50K tokens | ~$3 |
| Etsy Daily Brief | Daily | ~15K tokens | ~$14 |
| Etsy Weekly Deep Scan | Weekly | ~80K tokens | ~$10 |
| Content Prep Bot | On-demand (~8x/mo) | ~30K tokens | ~$7 |
| Etsy Listing Generator | On-demand (~4x/mo) | ~20K tokens | ~$2 |
| Framer Market Scout | Weekly | ~40K tokens | ~$5 |
| Brand Asset Engine | On-demand (~6x/mo) | ~25K tokens + MCP calls | ~$8 + mockup credits |
| **TOTAL** | | | **~$49/month + mockup credits** |

Browser automation (web scraping for Etsy/Framer) adds some overhead but stays well within typical OpenClaw usage budgets of $30-70/month total.
