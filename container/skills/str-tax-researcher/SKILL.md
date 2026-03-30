---
name: str-tax-researcher
description: Research and compile short-term rental tax rates across US markets, outputting structured data for the STR Tax Calculator product.
---

# STR Tax Rate Research Agent

## Purpose
Automatically research and compile short-term rental tax rates (state sales tax, county lodging tax, city hotel/motel tax, tourism development tax, special district assessments) for STR markets across the United States. Output structured data ready to paste into the STR Tax Calculator spreadsheet product.

## When to Use
- User says "research taxes for [city/state]"
- User says "update tax rates"
- User says "add a new market" or "add [city] to the tax sheet"
- Scheduled via cron to refresh rates quarterly

## Workflow

### Step 1: Identify Target Market
Parse the user's request for city, county, and state. If only a city is given, determine the county and state. If a state is given without cities, use this priority list of top STR markets per state:

**Priority Markets (research these first):**
- Oklahoma: Broken Bow/Hochatown, Oklahoma City, Tulsa
- Tennessee: Gatlinburg/Pigeon Forge, Nashville, Memphis
- Florida: Orlando/Kissimmee, Destin/30A, Miami Beach, Tampa, Panama City Beach
- Texas: Austin, San Antonio, Dallas, Houston, Fredericksburg
- North Carolina: Asheville, Outer Banks, Charlotte
- South Carolina: Charleston, Myrtle Beach, Hilton Head
- Georgia: Blue Ridge/Ellijay, Savannah, Helen
- Colorado: Denver, Breckenridge, Estes Park, Steamboat Springs
- Arizona: Scottsdale, Sedona, Tucson, Phoenix
- California: Joshua Tree, Big Bear, Lake Tahoe, Palm Springs, San Diego
- Missouri: Branson, Lake of the Ozarks
- Alabama: Gulf Shores/Orange Beach
- Arkansas: Hot Springs, Eureka Springs, Beaver Lake
- Michigan: Traverse City, South Haven
- Wisconsin: Wisconsin Dells, Door County
- Pennsylvania: Poconos
- Virginia: Virginia Beach, Shenandoah
- Oregon: Bend, Cannon Beach, Portland
- Washington: Leavenworth, San Juan Islands, Seattle
- Montana: Whitefish, Big Sky, Glacier area
- Utah: Moab, Park City, St. George
- Indiana: Indianapolis, Brown County
- Maine: Bar Harbor, Kennebunkport, Portland

### Step 2: Research Tax Rates
For each market, find these specific tax components by searching authoritative sources:

1. **State Sales Tax Rate** — Search: "[state] sales tax rate 2026"
2. **County/Parish Lodging Tax** — Search: "[county] [state] lodging tax rate" or "[county] transient occupancy tax"
3. **City Hotel/Motel Tax** — Search: "[city] hotel motel tax rate" or "[city] short term rental tax"
4. **Tourism/Convention Tax** — Search: "[city] tourism development tax" or "[city] convention center tax"
5. **Special District Tax** — Search: "[city] special taxing district short term rental" (some areas like Hochatown have fire district or improvement district taxes)
6. **State STR-Specific Tax** — Some states have a separate STR tax on top of sales tax. Search: "[state] short term rental specific tax"

**Source Priority (most reliable first):**
- State Department of Revenue / Comptroller website
- County tax assessor / collector website
- City finance department or clerk website
- State municipal league publications
- Avalara tax rate lookup
- AirDNA market reports (for verification)

**IMPORTANT:** Always note the source URL and date accessed. Tax rates change. Flag any rate that seems unusual (above 20% combined or below 5% combined) for manual verification.

### Step 3: Determine Applicability Rules
For each market, also research:
- **Registration required?** Does the city/county require STR registration or licensing?
- **Platform collection?** Does Airbnb/VRBO auto-collect any of these taxes? (Many states have voluntary collection agreements with platforms)
- **Threshold?** Any minimum night or revenue thresholds before taxes apply?
- **Inside vs Outside city limits?** Some areas (like Hochatown, OK) have different rates inside vs outside town limits

### Step 4: Output Structured Data
Format the results as a CSV-ready table:

```
Market,State,County,State_Sales_Tax,County_Lodging_Tax,City_Hotel_Tax,Tourism_Tax,Special_District_Tax,Total_Tax_Rate,Platform_Collects,Registration_Required,Source_URL,Date_Researched,Notes
"Broken Bow (Inside Hochatown)",OK,McCurtain,4.5,4.0,5.0,0,2.75,16.25,Partial,Yes,https://tax.ok.gov/...,2026-03-28,"Inside Hochatown town limits. Fire district tax applies."
"Broken Bow (Outside Hochatown)",OK,McCurtain,4.5,4.0,0,0,0.75,9.25,Partial,Yes,https://tax.ok.gov/...,2026-03-28,"Outside town limits. Lower combined rate."
```

### Step 5: Save Results
Save the CSV to `~/Documents/str-tax-data/tax-rates-[date].csv`
Also append to the master file `~/Documents/str-tax-data/all-markets-master.csv`

If updating an existing market, replace the old row (match on Market + State) and log the change:
```
~/Documents/str-tax-data/changelog.md
## 2026-03-28
- Updated Gatlinburg, TN: City hotel tax increased from 3% to 3.5% (source: ...)
```

## Scheduled Execution
When run as a cron job (quarterly refresh):
1. Load all-markets-master.csv
2. Re-research each market's rates
3. Flag any changes from previous values
4. Send summary to user via chat: "Quarterly tax rate refresh complete. 3 markets had rate changes: [list]. Full report saved."

## Error Handling
- If a rate cannot be found for a specific tax component, enter "VERIFY" in that cell and add a note
- If a government website is down, note "SOURCE_UNAVAILABLE" and retry next day
- If rates conflict between sources, note both values and flag for manual review
- Never guess. An empty cell with "VERIFY" is better than a wrong number.

## Example Interaction

**User:** "Research tax rates for Destin Florida"

**Agent:**
1. Identifies: Destin, Okaloosa County, Florida
2. Searches Florida DOR for state rate
3. Searches Okaloosa County tax collector for county TDT
4. Searches City of Destin for municipal taxes
5. Checks if Airbnb collects Florida taxes automatically
6. Outputs:
```
Market: Destin, FL (Okaloosa County)
State Sales Tax: 6.0%
County Surtax: 0.5%
County Tourist Development Tax: 5.0%
City Tax: 0%
Total: 11.5%
Platform Collection: Yes - Airbnb collects state + county
Registration: Yes - Okaloosa County vacation rental license required
Source: https://floridarevenue.com/taxes/...
```

**User:** "Do all 50 markets"

**Agent:** Runs through the full priority market list, researching each one sequentially, saving results incrementally. Sends progress updates every 10 markets. Full run takes approximately 2-3 hours.
