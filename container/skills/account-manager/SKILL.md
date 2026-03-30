---
name: account-manager
description: Handle account creation, login, and credential storage for all services using secure Keychain storage and Telegram for manual steps.
---

# Account & Credential Management Agent

## Purpose
Handle account creation, login, and credential storage for all services used by the Frontier digital product business. When the agent needs to create accounts or authenticate, it follows secure credential practices and uses Telegram to communicate with the user for any manual steps (2FA, CAPTCHA, confirmation).

## When to Use
- Agent encounters a login wall while executing another skill
- Agent needs to create an account on a new service (Etsy, Gumroad, FilterGrade, etc.)
- Agent needs to store or retrieve credentials
- Any skill requires authentication to proceed

## Identity Defaults

When creating accounts or filling registration forms:

```
Email: info@fcgok.com
Username (first choice): FCGOK
Username (fallback): FrontierCG
Display Name: Frontier Consulting Group
Company: Frontier Consulting Group LLC
Website: fcgok.com
Location: Broken Bow, OK
```

## Credential Storage — MANDATORY RULES

### NEVER do this:
- Never store passwords in SKILL.md files, BRAND.json, or any workspace file
- Never store passwords in environment variables in plain text
- Never send passwords via WhatsApp or Telegram messages
- Never log passwords in agent logs or transcripts
- Never hardcode credentials anywhere in the NanoClaw codebase

### ALWAYS do this:

**Option A: macOS Keychain (Preferred)**
Store credentials in the macOS Keychain using the `security` CLI tool. 
The Keychain is encrypted at rest and protected by your Mac login.

To store:
```bash
security add-generic-password \
  -a "info@fcgok.com" \
  -s "service-name-here" \
  -w "password-here" \
  -T "" \
  /Users/$(whoami)/Library/Keychains/login.keychain-db
```

To retrieve:
```bash
security find-generic-password \
  -a "info@fcgok.com" \
  -s "service-name-here" \
  -w \
  /Users/$(whoami)/Library/Keychains/login.keychain-db
```

The agent uses the service name as a lookup key. Examples:
- `-s "etsy"` for Etsy credentials
- `-s "gumroad"` for Gumroad
- `-s "dynamic-mockups"` for Dynamic Mockups API key
- `-s "mockuuups-studio"` for Mockuuups Studio API key
- `-s "filtergrade"` for FilterGrade

**Option B: Chrome Profile (Alternative)**
If the user prefers credentials stored in Chrome under the FCG profile,
the agent can use browser automation to save credentials during login.
Chrome's password manager syncs with Google account and encrypts locally.

When using Chrome profile:
1. Open Chrome with the FCG profile: `open -a "Google Chrome" --args --profile-directory="FCG"`
2. Navigate to the login page
3. Fill credentials and submit
4. Chrome auto-prompts to save — accept the prompt
5. Credentials are now in Chrome's password manager under the FCG profile

### Password Generation

When a service requires a password and the user hasn't provided one:

1. Generate a strong random password:
```bash
# 20 characters: uppercase, lowercase, numbers, symbols
openssl rand -base64 24 | tr -d '/+=' | head -c 20
```

2. Store it in macOS Keychain immediately (before using it)
3. Notify user via Telegram: "Created account on [service]. Credentials stored in Keychain under service name '[service]'. Username: info@fcgok.com"
4. Do NOT include the password in the Telegram message

## Account Creation Workflow

### Step 1: Check if account already exists
Before creating, try to log in:
```
1. Navigate to service login page
2. Enter info@fcgok.com
3. Click "forgot password" or check if account exists
4. If account exists → retrieve password from Keychain → log in
5. If account doesn't exist → proceed to creation
```

### Step 2: Create the account
```
1. Navigate to signup page
2. Fill registration form:
   - Email: info@fcgok.com
   - Username: FCGOK (or FrontierCG if taken)
   - Password: generate strong random password
   - Name/Company: Frontier Consulting Group
   - Website: fcgok.com
3. Store password in Keychain BEFORE submitting
4. Submit registration
```

### Step 3: Handle verification
Most services require email verification:
```
1. Notify user via Telegram:
   "I just created an account on [service] with info@fcgok.com. 
   Check your email for a verification link and click it. 
   Let me know when done."
2. Wait for user confirmation
3. If Gmail MCP is connected, check for verification email directly
4. Once verified, proceed with original task
```

### Step 4: Handle blockers
If the agent encounters something it can't handle:

**CAPTCHA:**
```
Telegram message: "I'm trying to sign up for [service] but hit a CAPTCHA. 
Can you complete it? I'll keep the browser window open."
```
Then the agent pauses and waits. The user can complete the CAPTCHA 
on the Mac Studio's screen (via screen sharing or physical access) 
or tell the agent to retry later.

**Phone verification / SMS:**
```
Telegram message: "I need to verify a phone number for [service]. 
They'll send an SMS to the number you provide. 
Reply with the phone number to use, then forward me the code when it arrives."
```

**Payment required for signup:**
```
Telegram message: "[Service] requires payment to create an account. 
Plan: [plan name], Price: [amount]. 
Should I proceed? You'll need to enter payment details yourself."
```
Agent NEVER enters payment information.

**Manual-only signup:**
```
Telegram message: "I can't auto-create an account on [service] — 
they block automated signups. Here's what you need to do:
1. Go to [URL]
2. Sign up with info@fcgok.com
3. Username: FCGOK
4. Use a strong password and save it in Chrome/Keychain
5. Let me know when done and I'll continue."
```

## Telegram as the Nudge Channel

Telegram is the communication channel for all credential-related interactions.
WhatsApp is for regular skill commands; Telegram is for sensitive/blocking items.

**Why Telegram over WhatsApp for this:**
- Telegram supports longer messages and better formatting
- Telegram bot messages can include inline buttons for quick responses
- Keeps sensitive auth discussions separate from regular workflow commands
- You can mute Telegram notifications differently than WhatsApp

**Nudge patterns:**

```
BLOCKING (need response to continue):
"⚠️ BLOCKED: I need your help to continue.
[Description of what's needed]
Reply here when done."

NON-BLOCKING (FYI):
"✅ Account created on [service] as FCGOK.
Credentials in Keychain. Continuing with [original task]."

ERROR:
"❌ Failed to create account on [service]. 
Error: [description]. 
Should I retry or skip this step?"
```

## Service-Specific Notes

### Etsy Seller Account
- Requires identity verification (ID + bank info)
- Agent can start the process but user MUST complete identity/banking sections
- Agent can fill: shop name, description, categories, policies
- Agent CANNOT fill: SSN, bank routing/account numbers, government ID upload

### Gumroad
- Simple email signup, no identity verification
- Agent can fully create and configure

### FilterGrade (Creator Account)
- Requires application review for creator accounts
- Agent can fill the application, user reviews before submit

### Creative Market
- Requires portfolio review for shop approval
- Agent can prepare the application

### Dynamic Mockups / Mockuuups Studio
- Simple API key signup
- Agent can fully create and store API keys in Keychain

### Framer (Creator Account)
- Requires Framer account + creator program application
- Agent can sign up for basic account
- Creator program may need portfolio submission

### Whop
- Simple signup
- Agent can fully create and configure

### monday.com (Developer Account)
- Requires developer application
- Agent can start, user may need to verify

## Credential Audit

**Command:** `"List all stored accounts"` or `"Credential check"`

The agent queries the Keychain for all entries with the info@fcgok.com account:
```bash
security dump-keychain /Users/$(whoami)/Library/Keychains/login.keychain-db 2>&1 | grep -A 3 "info@fcgok.com"
```

Reports (via Telegram, not WhatsApp):
```
Stored credentials for info@fcgok.com:
- etsy: ✅ stored
- gumroad: ✅ stored
- dynamic-mockups: ✅ stored (API key)
- mockuuups-studio: ✅ stored (API key)
- filtergrade: ❌ not found
- creative-market: ❌ not found
- framer: ❌ not found
- whop: ❌ not found
```

Never displays actual passwords in the audit — only confirms existence.

## Important Restrictions

1. Agent NEVER enters financial information (credit cards, bank accounts, SSN)
2. Agent NEVER shares passwords in chat messages (WhatsApp or Telegram)
3. Agent NEVER stores passwords in files, logs, or code
4. Agent always uses macOS Keychain or Chrome password manager for storage
5. Agent always notifies user via Telegram when accounts are created
6. Agent pauses and asks (via Telegram) when encountering CAPTCHAs, phone verification, or payment walls
7. Agent uses Telegram (not WhatsApp) for all credential-related communication
