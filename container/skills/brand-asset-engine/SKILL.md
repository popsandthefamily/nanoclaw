---
name: brand-asset-engine
description: Automate creation of branded visual assets — Etsy listing images, mockups, PDFs, social graphics — using BRAND.json and Puppeteer rendering.
---

# Brand Asset Engine

## Purpose
Automate the creation of branded visual assets for digital products: Etsy listing images, product mockups, PDF covers, whitepapers, social media graphics, and promotional materials. All outputs use the Frontier brand system defined in BRAND.json. Connects to Dynamic Mockups and Mockuuups Studio via MCP for device mockups, uses Puppeteer/Playwright for screenshots and PDF generation, and uses OpenPencil for custom design work when available.

## When to Use
- User says "generate listing images for [product]"
- User says "make mockups for [product]"
- User says "create PDF for [product name]"
- User says "brand this" or "make it branded"
- User says "social images for [product]"
- User says "etsy images" or "product photos"
- After a new product is ready and needs listing visuals
- After the Content Prep Bot generates copy that needs visual packaging

## Dependencies

### Required
- **BRAND.json** — Must exist at `~/Documents/brand/BRAND.json` (or workspace skills directory). Contains all colors, fonts, logos, and style preferences.
- **Logo files** — Must be placed at paths specified in BRAND.json
- **Node.js** — For Puppeteer screenshot/PDF generation
- **Puppeteer or Playwright** — `npm install -g puppeteer` (likely already available via OpenClaw)

### MCP Servers (Configure in OpenClaw/NanoClaw)

**Dynamic Mockups** — For product mockups using PSD templates:
```json
{
  "mcpServers": {
    "dynamic-mockups": {
      "command": "npx",
      "args": ["-y", "@dynamic-mockups/mcp"],
      "env": {
        "DYNAMIC_MOCKUPS_API_KEY": "YOUR_KEY_HERE"
      }
    }
  }
}
```
Get API key (free tier available): https://dynamicmockups.com

**Mockuuups Studio** — For device/browser frame mockups:
```json
{
  "mcpServers": {
    "mockuuups": {
      "url": "https://api.mockuuups.studio/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_KEY_HERE"
      }
    }
  }
}
```
Get API key (50 free credits): https://developer.mockuuups.studio

### Optional
- **OpenPencil** — For AI-driven custom design work (canvas-based layouts, complex compositions)
- **ImageMagick** — `brew install imagemagick` — For image compositing, resizing, watermarking without a browser
- **wkhtmltopdf** — Alternative PDF renderer if Puppeteer is overkill for simple docs

## Brand Config Loading

Every function in this skill starts by loading BRAND.json:
```
Load ~/Documents/brand/BRAND.json
Extract: colors, fonts, logo paths, copy_style, mockup_preferences, pdf_style, social_media sizes
Validate: logo files exist at specified paths
If missing: warn user, continue with text-only outputs
```

## Capabilities

---

### 1. Etsy Listing Image Generation

**Command:** `"Generate Etsy images for [product name]"`

**Process:**

**Step 1: Screenshot the product**
If the product is a Google Sheets spreadsheet:
- Open the published/shared URL in Puppeteer at 1920x1080
- Navigate to the most visually interesting tab (the dashboard, calculator, or summary view)
- Screenshot at 2x resolution (3840x2160) for crisp output
- Crop to content area (remove browser chrome, Google Sheets toolbar)
- Save to `~/Documents/brand/screenshots/[product]-raw.png`

If the product is a Canva template:
- User must provide a screenshot or export from Canva (agent cannot log into Canva)
- Or: user provides the Canva share link and agent screenshots it via Puppeteer

If the product is a PDF bundle:
- Render page 1 of the PDF as an image at 300 DPI
- Use ImageMagick: `convert -density 300 document.pdf[0] -quality 95 preview.png`

**Step 2: Create device mockups**
Use Mockuuups Studio MCP to generate 3-4 device mockup variations:

```
Mockup 1: MacBook Pro — straight-on view, product filling the screen
Mockup 2: iPad Pro — angled view, lifestyle desk scene
Mockup 3: iMac 27" — front view, clean white background
Mockup 4: iPhone 15 — if product has mobile relevance
```

For each: send the cropped screenshot + request specific scene from BRAND.json mockup_preferences.

**Step 3: Create branded info-graphic images**
Generate HTML templates for the remaining Etsy listing slots (aiming for 8-10 images total):

```
Image 5: "What's Included" — Checklist layout
  - Brand background color (light)
  - Each item with a check icon
  - Product title at top
  - "Instant Download" badge at bottom

Image 6: "How It Works" — 3-step visual
  - Step 1: Purchase & Download
  - Step 2: Open in [Google Sheets / Canva]
  - Step 3: Customize & Use
  - Numbered circles in brand accent color

Image 7: "Features" — Grid layout
  - 4-6 key features with icons
  - Brand colors, clean typography

Image 8: "Who It's For" — Target audience
  - 3-4 audience descriptions
  - Simple icons or emoji representations

Image 9: "What You Get" — File format details
  - Google Sheets icon + description
  - Canva icon + description
  - PDF icon + description
  - Show actual file count

Image 10: "Review / Social Proof" — (once reviews exist)
  - Star rating graphic
  - Pull quote from review
  - Brand footer with logo
```

**Step 4: Render info-graphic images**
For each HTML template:
- Render via Puppeteer at 2000x2000px (Etsy optimal)
- Use brand fonts loaded via Google Fonts URL from BRAND.json
- Apply brand colors from BRAND.json
- Include logo per BRAND.json placement settings
- Save to `~/Documents/brand/etsy-images/[product]/`

**Step 5: Optimize and deliver**
- Compress all PNGs with `pngquant` or ImageMagick (target < 1MB each for fast Etsy loading)
- Create a numbered set: `01-hero.png`, `02-macbook-mockup.png`, etc.
- Report to user: "Generated 10 Etsy listing images for [product]. Saved to ~/Documents/brand/etsy-images/[product]/"

---

### 2. Product Mockup Generation (Standalone)

**Command:** `"Mockup [product] on [device]"` or `"Make mockups for [product]"`

**Using Dynamic Mockups MCP:**
```
1. Browse available mockup templates: call list_mockups or search_mockups
2. Filter by device type from BRAND.json mockup_preferences
3. For each preferred device scene:
   a. Upload product screenshot as the design asset
   b. Set background color from BRAND.json if the template supports it
   c. Render the mockup
   d. Download the rendered image
4. Save all renders to ~/Documents/brand/mockups/[product]/
```

**Using Mockuuups Studio MCP:**
```
1. Provide the product URL or screenshot
2. Request specific scene types: "laptop on desk", "tablet in hand", etc.
3. Specify resolution (default: 2000px wide for Etsy, 4000px for print)
4. Download and save
```

**Batch mode:** `"Mockup all products"` — Iterates through all products in the portfolio, generates a standard set of 4 mockups each.

---

### 3. Branded PDF Generation

**Command:** `"Create PDF for [document name]"` or `"Make a branded PDF"`

**Use cases:**
- Product guides ("Quick Start Guide" included with bundles)
- Whitepapers / lead magnets
- One-pagers for Whop or Gumroad product pages
- Report covers
- Checklists formatted as professional PDFs

**Process:**

**Step 1: Load brand config**
Read BRAND.json for colors, fonts, logo path, pdf_style settings.

**Step 2: Generate HTML template**
Create a responsive HTML document using brand settings:

```html
<!DOCTYPE html>
<html>
<head>
  <link href="[google_fonts_url from BRAND.json]" rel="stylesheet">
  <style>
    @page {
      size: [page_size from BRAND.json];
      margin: [margin_inches]in;
    }
    body {
      font-family: '[body font]', sans-serif;
      font-weight: [body_weight];
      color: [text_primary];
      line-height: 1.6;
    }
    h1, h2, h3 {
      font-family: '[heading font]', sans-serif;
      font-weight: [heading_weight];
      color: [primary];
    }
    .header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding-bottom: 12px;
      border-bottom: 2px solid [primary];
      margin-bottom: 24px;
    }
    .header img { height: 40px; }
    .footer {
      position: fixed;
      bottom: 0;
      width: 100%;
      font-size: 10px;
      color: [text_secondary];
      display: flex;
      justify-content: space-between;
    }
    .cover-page {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      background: linear-gradient(135deg, [primary], [dark]);
      color: [text_on_dark];
      text-align: center;
    }
    .cover-page h1 {
      font-size: 42px;
      color: [text_on_dark];
      margin-bottom: 8px;
    }
    .cover-page .subtitle {
      font-size: 18px;
      color: [accent];
      margin-bottom: 40px;
    }
    .highlight-box {
      background: [light];
      border-left: 4px solid [accent];
      padding: 16px 20px;
      margin: 20px 0;
      border-radius: 0 8px 8px 0;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin: 20px 0;
    }
    th {
      background: [primary];
      color: [white];
      padding: 10px 12px;
      text-align: left;
    }
    td {
      padding: 10px 12px;
      border-bottom: 1px solid #E0E0E0;
    }
    tr:nth-child(even) { background: [light]; }
  </style>
</head>
<body>
  <!-- Header on every page -->
  <div class="header">
    <img src="[logo_path]" alt="[company_name]">
    <span style="color: [text_secondary]; font-size: 12px;">[document title]</span>
  </div>

  <!-- Content injected here -->

  <!-- Footer on every page -->
  <div class="footer">
    <span>[footer_text from BRAND.json]</span>
    <span>Page <span class="pageNumber"></span></span>
  </div>
</body>
</html>
```

**Step 3: Inject content**
Insert the document content (from Content Prep Bot or user input) into the HTML template. Apply formatting:
- Headers → `<h1>`, `<h2>`, `<h3>` with brand heading styles
- Lists → styled unordered lists with brand accent bullets
- Tables → branded table styling from template
- Call-outs → `.highlight-box` styling
- Images → centered with brand border-radius

**Step 4: Render to PDF**
Use Puppeteer:
```javascript
const browser = await puppeteer.launch();
const page = await browser.newPage();
await page.setContent(htmlContent, { waitUntil: 'networkidle0' });
await page.pdf({
  path: outputPath,
  format: 'Letter',
  printBackground: true,
  margin: { top: '1in', right: '1in', bottom: '1in', left: '1in' },
  displayHeaderFooter: false // We use CSS-based headers instead
});
await browser.close();
```

**Step 5: Save and report**
Save to `~/Documents/brand/pdfs/[document-name].pdf`
Report: "Created branded PDF: [name]. [X] pages. Saved to [path]."

---

### 4. Social Media Image Generation

**Command:** `"Social images for [product]"` or `"Make [platform] posts for [product]"`

**Process:**

For each social platform size (from BRAND.json social_media):

**Template types:**

**Announcement Post** (new product launch):
```
- Background: brand primary color gradient
- Product mockup (from mockup library) centered
- Product name in heading font, white
- Price badge in accent color
- "Link in bio" or "Available now" CTA
- Logo in corner
```

**Feature Highlight Post:**
```
- Background: brand light color
- Left side: product screenshot or mockup
- Right side: 3 key features with check icons
- Brand colors throughout
- Logo bottom corner
```

**Testimonial Post** (once reviews exist):
```
- Background: dark brand color
- Large quotation marks in accent color
- Review text in white
- Star rating
- "Available on Etsy" badge
- Logo bottom corner
```

**Tip/Value Post** (for content marketing):
```
- Background: brand light color
- "Did you know?" or "Pro Tip:" header in accent
- Stat or tip text in dark, large font
- Subtle product mention at bottom
- Logo bottom corner
```

Render each via Puppeteer at the appropriate size from BRAND.json social_media dimensions.
Save to `~/Documents/brand/social/[product]/[platform]-[type].png`

---

### 5. Product Page Hero Images

**Command:** `"Hero image for [product]"` or `"Gumroad banner for [product]"`

For Gumroad, Whop, or personal product pages, generate a wider hero/banner image:

```
Dimensions: 1920x1080 (standard) or 2560x600 (banner)
Layout:
- Left 60%: Product description text
  - Product name (heading font, large)
  - 3-4 bullet features
  - Price badge
- Right 40%: Device mockup of the product
- Background: brand gradient or lifestyle photo with overlay
- Logo in top-left corner
```

---

### 6. Brand Asset Export Kit

**Command:** `"Export brand kit"` or `"Brand assets for [sub-brand]"`

Generates a ZIP containing all brand assets for handoff or personal reference:

```
frontier-brand-kit/
├── logos/
│   ├── frontier-logo-primary.png
│   ├── frontier-logo-white.png
│   ├── frontier-icon.png
│   └── frontier-logo.svg
├── colors/
│   └── color-palette.png (rendered swatch card)
├── fonts/
│   └── font-reference.png (rendered specimen sheet)
├── templates/
│   ├── pdf-template.html
│   ├── social-post-template.html
│   ├── etsy-infographic-template.html
│   └── email-signature.html
├── mockup-scenes/
│   └── [saved PSD template references]
└── BRAND.json
```

---

## Batch Operations

### "Generate all assets for [product]"
Full pipeline for a new product launch:
1. Screenshot the product (Sheets, Canva export, or PDF)
2. Generate 4 device mockups via MCP
3. Create 6 branded info-graphic images for Etsy
4. Generate social media images (Instagram post + story + Facebook)
5. Create product page hero image
6. Generate branded Quick Start Guide PDF
7. Report summary with file count and locations

### "Refresh all Etsy images"
Re-generates listing images for all products using latest brand config. Useful after a brand refresh or logo update.

### "New product launch kit for [product]"
Combines brand asset generation with content from other skills:
1. [Etsy Listing Generator] → writes the listing copy
2. [Brand Asset Engine] → generates all visual assets
3. [Content Prep Bot] → creates the Quick Start Guide content
4. [Brand Asset Engine] → renders the Quick Start Guide as branded PDF
5. Output: everything needed to list a new product, in one folder

---

## Output Directory Structure

```
~/Documents/brand/
├── BRAND.json                          # Master brand config
├── assets/                             # Logo files, brand marks
│   ├── frontier-logo-primary.png
│   ├── frontier-logo-white.png
│   ├── frontier-icon.png
│   └── frontier-logo.svg
├── screenshots/                        # Raw product screenshots
│   └── [product]-raw.png
├── mockups/                            # Device mockup renders
│   └── [product]/
│       ├── macbook-front.png
│       ├── ipad-angle.png
│       ├── imac-desk.png
│       └── iphone-hand.png
├── etsy-images/                        # Ready-to-upload Etsy listing images
│   └── [product]/
│       ├── 01-hero.png
│       ├── 02-macbook-mockup.png
│       ├── 03-ipad-mockup.png
│       ├── 04-whats-included.png
│       ├── 05-how-it-works.png
│       ├── 06-features.png
│       ├── 07-who-its-for.png
│       ├── 08-what-you-get.png
│       ├── 09-faq.png
│       └── 10-social-proof.png
├── social/                             # Social media images
│   └── [product]/
│       ├── instagram-post-announce.png
│       ├── instagram-story-announce.png
│       ├── facebook-announce.png
│       └── pinterest-pin.png
├── pdfs/                               # Branded PDF documents
│   ├── [product]-quick-start-guide.pdf
│   └── [whitepaper-name].pdf
├── heroes/                             # Product page hero/banner images
│   └── [product]-hero.png
└── templates/                          # Reusable HTML templates
    ├── pdf-base.html
    ├── etsy-infographic-base.html
    ├── social-post-base.html
    └── hero-base.html
```

---

## HTML Template System

All visual outputs use a shared HTML template system. Templates live in `~/Documents/brand/templates/` and are rendered by Puppeteer.

**Why HTML → PNG/PDF instead of Canva or Photoshop:**
- 100% automatable — no GUI interaction needed
- Brand config injects directly via CSS variables
- Agent can modify content, colors, layout programmatically
- Renders identically every time
- No software licenses required
- Works offline on your Mac Studio

**Template variables** (injected from BRAND.json at render time):
```css
:root {
  --brand-primary: var(--from-config);
  --brand-secondary: var(--from-config);
  --brand-accent: var(--from-config);
  --brand-dark: var(--from-config);
  --brand-light: var(--from-config);
  --font-heading: var(--from-config);
  --font-body: var(--from-config);
}
```

**Changing the brand = changing one JSON file.** Every template, every output, every asset regenerates with the new colors/fonts/logo automatically.

---

## Quality Rules

- All images must be crisp at their target resolution. Never upscale a low-res screenshot.
- All text must be anti-aliased and readable at the output size.
- Logo must maintain its aspect ratio. Never stretch or distort.
- Maintain minimum logo padding (padding_px from BRAND.json) from edges.
- If a mockup render fails (MCP error, API down), fall back to a clean branded frame (CSS border + shadow) around the screenshot rather than delivering nothing.
- Compress all PNGs to under 1MB for web use. Keep uncompressed originals in a /raw/ subfolder if needed for print.
- Never auto-post or auto-upload generated images anywhere. Always deliver to local folder for user review first.
- PDFs must embed fonts (Puppeteer does this by default) so they display correctly on any system.

## Error Handling

- **MCP server unavailable:** Fall back to Puppeteer-rendered CSS device frames (not as photorealistic but functional)
- **Logo file missing:** Render with company name text in heading font instead. Warn user: "Logo file not found at [path]. Using text fallback."
- **Google Fonts unavailable (offline):** Fall back to system fonts (Arial → Inter, Georgia → serif headings). Note in output.
- **Screenshot fails:** Retry once. If still failing, ask user to provide a screenshot manually.
- **Disk space low:** Warn before generating batch operations. Etsy image sets run ~15-20MB per product.
