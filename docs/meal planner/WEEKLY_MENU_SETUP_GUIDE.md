# Weekly Family Menu Generator - Setup Guide

**Workflow ID:** `fnLgg88KLhFRlu3T`
**Status:** ✅ Created and Ready for Configuration
**Location:** Your n8n instance at https://n8n.prtsr.nl

---

## 📋 Quick Overview

This workflow automatically generates a weekly family dinner menu every Friday at 2 PM (14:00), creates a shopping list, and optionally populates your Picnic grocery cart. It emails you a beautifully formatted HTML menu with:

- 7 dinner recipes (Monday-Sunday)
- At least 3 quick meals (15-20 min prep)
- Healthy, kid-friendly, weight-loss conscious meals
- Consolidated shopping list organized by category
- Picnic cart integration (requires setup)
- Meal history tracking to avoid repetition

---

## 🚀 Quick Start (Essential Steps)

### Step 1: Access the Workflow
1. Go to https://n8n.prtsr.nl/workflow/fnLgg88KLhFRlu3T
2. The workflow is currently **inactive** (safe for setup)

### Step 2: Configure Essential Credentials

You need to set up 3-4 credentials before the workflow can run:

#### ✅ Required Credentials:
1. **OpenAI API Key** (for menu generation)
2. **Gmail OAuth2** (for sending emails)
3. **JSONBin.io API Key** (for meal history tracking)

#### ⚙️ Optional Credential:
4. **Picnic Login** (for automatic cart population)

---

## 🔑 Credential Setup Instructions

### 1. OpenAI API Key

**Purpose:** Powers the AI that generates your weekly menu

**Steps:**
1. Go to https://platform.openai.com/api-keys
2. Sign in or create an account
3. Click "Create new secret key"
4. Name it "n8n Weekly Menu"
5. Copy the API key (starts with `sk-...`)

**In n8n:**
1. Go to **Credentials → Add Credential**
2. Search for "OpenAI"
3. Select "OpenAI API"
4. Paste your API key
5. Name it "OpenAI API - Menu Generator"
6. Click "Save"

**Link to Workflow Node:**
- Go to the "Generate Weekly Menu" node
- Select your OpenAI credential from the dropdown

**Cost:** ~$0.15 per menu generation (4x per month = ~$0.60/month)

---

### 2. Gmail OAuth2

**Purpose:** Sends you the weekly menu email

**Steps:**

#### A. Google Cloud Console Setup
1. Go to https://console.cloud.google.com
2. Create a new project (or use existing)
   - Project name: "n8n Weekly Menu"
3. Enable Gmail API:
   - Go to **APIs & Services → Library**
   - Search for "Gmail API"
   - Click "Enable"
4. Create OAuth 2.0 credentials:
   - Go to **APIs & Services → Credentials**
   - Click "Create Credentials" → "OAuth client ID"
   - Application type: "Web application"
   - Name: "n8n Gmail Integration"
   - Add authorized redirect URI:
     ```
     https://n8n.prtsr.nl/rest/oauth2-credential/callback
     ```
   - Click "Create"
5. Copy your:
   - Client ID (looks like: `xxxxx.apps.googleusercontent.com`)
   - Client Secret (looks like: `GOCSPX-xxxxx`)

#### B. n8n Setup
1. In n8n: **Credentials → Add Credential**
2. Search for "Gmail"
3. Select "Gmail OAuth2 API"
4. Enter:
   - **Client ID:** (from Google Cloud Console)
   - **Client Secret:** (from Google Cloud Console)
5. Click "Connect my account"
6. Authorize n8n to send emails
7. Name it "Gmail - Family Menu"
8. Click "Save"

**Link to Workflow Node:**
- Go to the "Send Email" node
- Select your Gmail OAuth2 credential

---

### 3. JSONBin.io API Key

**Purpose:** Stores meal history to avoid recipe repetition

**Steps:**

#### A. Create JSONBin Account
1. Go to https://jsonbin.io
2. Sign up for free account (10,000 requests/month)
3. Verify your email

#### B. Create a Bin (Storage)
1. Click "Create Bin"
2. Name it: "n8n Weekly Menu History"
3. Paste this initial content:
   ```json
   {
     "meals": []
   }
   ```
4. Set visibility to "Private"
5. Click "Create"
6. **Copy the Bin ID** from the URL (looks like: `65abc123def456789`)

#### C. Get API Key
1. Go to **Account → API Keys**
2. Copy your "Master Key" (starts with `$2a$...`)

#### D. n8n Setup
1. In n8n: **Credentials → Add Credential**
2. Search for "HTTP Header Auth"
3. Select "Header Auth (generic)"
4. Enter:
   - **Name:** "JSONBin API Key"
   - **Header Name:** `X-Master-Key`
   - **Header Value:** (paste your Master Key)
5. Click "Save"

#### E. Update Workflow Nodes
You need to replace `YOUR_BIN_ID` in two nodes:

1. **"Get Meal History" node:**
   - Open the node
   - Find the URL field
   - Replace `YOUR_BIN_ID` with your actual Bin ID
   - URL should look like: `https://api.jsonbin.io/v3/b/65abc123def456789/latest`

2. **"Update Meal History" node:**
   - Open the node
   - Find the URL field
   - Replace `YOUR_BIN_ID` with your actual Bin ID
   - URL should look like: `https://api.jsonbin.io/v3/b/65abc123def456789`

3. **Link credentials** to both HTTP Request nodes:
   - Select "Header Auth (generic)" as authentication type
   - Choose your "JSONBin API Key" credential

---

### 4. Picnic Login (Optional)

**Purpose:** Automatically adds ingredients to your Picnic cart

⚠️ **IMPORTANT:** This requires additional setup and the workflow will work WITHOUT it (you'll just get the shopping list via email instead).

**Steps:**

#### A. Install picnic-api npm Package

**On your n8n server:**
```bash
# SSH into your n8n server
ssh user@your-server

# Navigate to n8n directory
cd ~/.n8n/nodes

# Install picnic-api package
npm install picnic-api
```

**Or, if using n8n Docker:**
```bash
docker exec -it n8n npm install -g picnic-api
```

#### B. Enable npm Module Loading in n8n
1. Go to **Settings → Security**
2. Enable "Allow loading external npm modules"
3. Restart n8n

#### C. Create Picnic Credential in n8n
1. **Credentials → Add Credential**
2. Select "Generic Credential Type"
3. Name it "Picnic Login"
4. Add these fields manually:
   ```json
   {
     "email": "your-picnic-email@example.com",
     "password": "your-picnic-password"
   }
   ```
5. Click "Save"

#### D. Update "Add to Picnic Cart" Node
1. Open the "Add to Picnic Cart" code node
2. Uncomment the Picnic integration code (remove `/*` and `*/`)
3. Update credential reference:
   ```javascript
   const picnicEmail = $credentials.picnicApi.email;
   const picnicPassword = $credentials.picnicApi.password;
   ```
4. Link the credential to the node

**⚠️ Limitations:**
- Picnic API is unofficial (reverse-engineered)
- Can add items to cart but **CANNOT place orders**
- You must manually confirm your order in the Picnic app
- May require 2FA verification (manual step)

---

## ⚙️ Configuration & Customization

### Change Email Recipients

1. Open the "Combine Email Data" node
2. Find the `to` field assignment
3. Replace `your-email@example.com` with your actual email
4. For multiple recipients, use comma-separated emails:
   ```
   parent1@example.com,parent2@example.com
   ```

### Add Dietary Restrictions

1. Open the "Prepare OpenAI Prompt" node
2. Find this section in the code:
   ```
   DIETARY RESTRICTIONS/PREFERENCES:
   [USER TO CONFIGURE: e.g., "No shellfish, prefer Mediterranean flavors, lactose-free milk"]
   ```
3. Replace the placeholder with your actual restrictions:
   ```
   DIETARY RESTRICTIONS/PREFERENCES:
   - No shellfish or peanuts (allergies)
   - Prefer Mediterranean and Asian cuisines
   - Use lactose-free milk instead of regular milk
   - Limit red meat to 1-2 times per week
   - Include at least 2 vegetarian meals
   ```

### Change Schedule

**Current:** Every Friday at 2:00 PM (14:00) CET

**To change:**
1. Open the "Weekly Menu Trigger" node
2. Modify the cron expression:
   - **Sunday 10 AM:** `0 10 * * 0`
   - **Wednesday 6 PM:** `0 18 * * 3`
   - **Twice a week (Wed & Sat):** `0 18 * * 3,6`
3. Update timezone if needed (currently `Europe/Amsterdam`)

### Adjust Meal History Lookback

**Current:** Last 21 meals (3 weeks)

**To change:**
1. Open "Prepare OpenAI Prompt" node
2. Find: `previousMeals.slice(-21)`
3. Change number:
   - Last 2 weeks: `-14`
   - Last 4 weeks: `-28`
   - Last 6 weeks: `-42`

---

## 🧪 Testing Before Friday

### Test 1: Manual Trigger (Recommended First Test)

1. Click the "Execute Workflow" button at the bottom
2. **Expected behavior:**
   - Workflow runs through all nodes
   - OpenAI generates 7 recipes
   - Email is sent to your address
   - History is updated in JSONBin
3. **Check:**
   - Your email inbox for the menu
   - JSONBin.io for updated meal history
   - Picnic cart (if configured)

### Test 2: Individual Node Testing

**Test OpenAI Generation:**
1. Click on "Generate Weekly Menu" node
2. Click "Execute Node" (play button)
3. Verify JSON output contains 7 recipes

**Test Email Formatting:**
1. After running the workflow once
2. Check "Format Menu HTML" node output
3. Copy HTML and paste into https://htmledit.squarefree.com to preview

**Test Picnic Integration:**
1. Run "Add to Picnic Cart" node individually
2. Check Picnic app/website for added items
3. **Important:** Clear your cart after testing!

### Test 3: Validate Workflow

Run validation to check for any configuration issues:
```bash
# Use n8n MCP tool
n8n_validate_workflow --id fnLgg88KLhFRlu3T
```

Or in the n8n UI:
- Some warnings are expected (they're informational)
- Focus on fixing any **errors** (shown in red)

---

## ✅ Activation Checklist

Before activating the workflow, ensure:

- [ ] OpenAI API credential configured and linked
- [ ] Gmail OAuth2 credential configured and linked
- [ ] JSONBin.io credential configured with Bin ID updated in both HTTP nodes
- [ ] Email recipient address updated in "Combine Email Data" node
- [ ] Dietary restrictions added to OpenAI prompt (if needed)
- [ ] Workflow tested manually at least once
- [ ] Test email received successfully
- [ ] Meal history stored in JSONBin
- [ ] (Optional) Picnic integration tested and cleared
- [ ] Schedule is correct (Friday 2 PM by default)

---

## 🎯 Activation

Once all credentials are set up and tested:

1. Go to the workflow page
2. Toggle the **"Active"** switch in the top-right
3. Workflow will now run automatically every Friday at 2 PM

**First Run:** This Friday at 14:00 (2 PM) CET

---

## 📧 What to Expect

### Every Friday at 2 PM:

1. **Workflow triggers automatically**
2. **Retrieves your meal history** (last 3 weeks)
3. **AI generates 7 new dinner recipes:**
   - At least 3 quick meals (15-20 min)
   - Healthy, kid-friendly, weight-loss conscious
   - Avoids recent repetition
4. **Consolidates shopping list:**
   - Merges duplicate ingredients
   - Organizes by category (Proteins, Vegetables, etc.)
5. **(Optional) Adds items to Picnic cart**
6. **Sends you a beautiful HTML email with:**
   - Week overview (quick meals, avg calories, prep time)
   - 7 recipe cards with ingredients and instructions
   - Color-coded quick meals (green)
   - Nutritional info (calories, protein)
   - Kid-friendly ratings
   - Complete shopping list by category
   - Picnic cart status (items added/not found)
7. **Updates meal history** for next week

### Sample Email Subject:
```
🍽️ Your Weekly Menu is Ready! Week of 2026-01-06 - 3 Quick Meals Inside
```

---

## 🔧 Troubleshooting

### Problem: Workflow fails with "OpenAI error"

**Solutions:**
- Check OpenAI API credit balance
- Verify API key is correct
- Check OpenAI status page: https://status.openai.com
- Reduce `maxTokens` in OpenAI node if hitting limits

### Problem: Email not received

**Solutions:**
- Check Gmail spam folder
- Verify Gmail OAuth2 credential is not expired (re-authenticate if needed)
- Check Gmail API quotas in Google Cloud Console
- Test Gmail node individually
- Check n8n execution log for errors

### Problem: "Cannot find module 'picnic-api'"

**Solutions:**
- Install picnic-api: `npm install picnic-api`
- Enable "Allow loading external npm modules" in n8n settings
- Restart n8n after installation
- Or skip Picnic integration (workflow works without it)

### Problem: Picnic authentication fails

**Solutions:**
- Verify Picnic credentials in n8n
- Try manual login on Picnic website/app
- Check if Picnic requires 2FA (may need manual intervention)
- Use fallback: disable Picnic integration, rely on email shopping list

### Problem: Too many similar recipes week-after-week

**Solutions:**
- Increase history lookback (change `-21` to `-35` or higher)
- Manually clear meal history in JSONBin if needed
- Add more specific cuisine preferences in dietary restrictions
- Check that meal history is being saved correctly

### Problem: Recipes don't match dietary restrictions

**Solutions:**
- Review dietary restrictions in "Prepare OpenAI Prompt" node
- Make restrictions more explicit and detailed
- Add examples of preferred/avoided ingredients
- Adjust OpenAI temperature (lower = more consistent)

---

## 💰 Cost Breakdown

### Monthly Costs (4 executions):

| Service | Cost | Notes |
|---------|------|-------|
| OpenAI GPT-4o | ~$0.60/month | $0.15 per execution |
| JSONBin.io | $0.00 | Free tier (10K requests/month) |
| Gmail API | $0.00 | Free |
| Picnic API | $0.00 | Unofficial, free to use |
| **Total** | **~$0.60/month** | Primarily OpenAI costs |

### Cost Optimization:

**Option 1:** Use GPT-3.5-Turbo instead of GPT-4o
- Cost: ~$0.02 per execution (~$0.08/month)
- Savings: ~87%
- Trade-off: Slightly lower quality recipes, less structured output

**Option 2:** Run biweekly instead of weekly
- Cost: ~$0.30/month
- Savings: 50%
- Trade-off: Plan 2 weeks at once

---

## 📊 Monitoring & Maintenance

### Weekly Checks:
- [ ] Verify email received
- [ ] Check Picnic cart populated correctly (if using)
- [ ] Review "not found" items list
- [ ] Confirm no duplicate meals from history

### Monthly Checks:
- [ ] Review meal history (should have 20-30 entries)
- [ ] Check OpenAI API usage and costs
- [ ] Update dietary preferences if needed
- [ ] Review prompt effectiveness (quality of recipes)

### Quarterly Checks:
- [ ] Update picnic-api npm package: `npm update picnic-api`
- [ ] Test error handling scenarios
- [ ] Backup meal history data from JSONBin
- [ ] Review and optimize ingredient categorization logic
- [ ] Check Gmail OAuth token expiration

---

## 🆘 Support & Resources

### Documentation:
- **n8n Docs:** https://docs.n8n.io
- **OpenAI API:** https://platform.openai.com/docs
- **Picnic API Package:** https://github.com/MRVDH/picnic-api
- **JSONBin.io Docs:** https://jsonbin.io/api-reference

### Community:
- **n8n Community Forum:** https://community.n8n.io
- **n8n Discord:** https://discord.gg/n8n

### Workflow Details:
- **Workflow ID:** fnLgg88KLhFRlu3T
- **Direct Link:** https://n8n.prtsr.nl/workflow/fnLgg88KLhFRlu3T
- **Plan Document:** `/Users/link/.claude/plans/linked-chasing-lecun.md`

---

## 🎉 You're All Set!

Once you complete the setup and activate the workflow, you'll receive your first automated weekly menu this Friday at 2 PM!

**Benefits:**
- ✅ Save 2-3 hours per week on meal planning
- ✅ Healthier, balanced family dinners
- ✅ Kid-friendly meals your children will enjoy
- ✅ Weight-loss friendly for father's goals
- ✅ Quick meal options for busy weeknights
- ✅ No more recipe repetition
- ✅ Organized shopping list
- ✅ Optional automatic Picnic cart population

**Questions?** Check the troubleshooting section or refer to the comprehensive plan at `/Users/link/.claude/plans/linked-chasing-lecun.md`

Happy cooking! 🍽️👨‍🍳👩‍🍳
