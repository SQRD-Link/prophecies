# Weekly Family Menu Generator - Quick Reference Card

## 🔗 Quick Links

- **Workflow URL:** https://n8n.prtsr.nl/workflow/fnLgg88KLhFRlu3T
- **Workflow ID:** `fnLgg88KLhFRlu3T`
- **Full Setup Guide:** `WEEKLY_MENU_SETUP_GUIDE.md`
- **Detailed Plan:** `/Users/link/.claude/plans/linked-chasing-lecun.md`

---

## ✅ Setup Checklist (Essential)

### 1. OpenAI API Key
- [x] Get from: https://platform.openai.com/api-keys ✅ 2026-01-03
- [x] Add to n8n Credentials (type: "OpenAI API") ✅ 2026-01-03
- [x] Link to "Generate Weekly Menu" node ✅ 2026-01-03
- **Cost:** ~$0.60/month

### 2. Gmail OAuth2
- [x] Set up in Google Cloud Console ✅ 2026-01-03
- [x] Enable Gmail API ✅ 2026-01-03
- [x] Create OAuth 2.0 client ✅ 2026-01-03
- [x] Add redirect URI: `https://n8n.prtsr.nl/rest/oauth2-credential/callback` ✅ 2026-01-03
- [x] Add to n8n Credentials (type: "Gmail OAuth2") ✅ 2026-01-03
- [x] Link to "Send Email" node ✅ 2026-01-03

### 3. JSONBin.io (Meal History)
- [x] Create free account: https://jsonbin.io ✅ 2026-01-03
- [x] Create bin with: `{"meals": []}` ✅ 2026-01-03
- [x] Copy Bin ID ✅ 2026-01-03
- [x] Get Master API Key ✅ 2026-01-03
- [x] Add to n8n Credentials (type: "Header Auth") ✅ 2026-01-03
- [x] Update `YOUR_BIN_ID` in 2 HTTP nodes: ✅ 2026-01-03
  - "Get Meal History" node
  - "Update Meal History" node

### 4. Update Email Address
- [x] Open "Combine Email Data" node ✅ 2026-01-03
- [x] Change `your-email@example.com` to your actual email ✅ 2026-01-03

### 5. Test Workflow
- [ ] Click "Execute Workflow" button
- [ ] Check email inbox
- [ ] Verify JSONBin updated

### 6. Activate
- [ ] Toggle "Active" switch ON
- [ ] First run: This Friday at 2 PM

---

## 🔧 Common Configuration Tasks

### Add Dietary Restrictions

**Node:** "Prepare OpenAI Prompt"

**Find:** `[USER TO CONFIGURE: ...]`

**Replace with:**
```
- No shellfish or peanuts
- Lactose-free milk only
- Limit red meat to 1x per week
- Prefer Mediterranean cuisine
```

### Change Schedule

**Node:** "Weekly Menu Trigger"

**Current:** Friday 2 PM → `0 14 * * 5`

**Examples:**
- Sunday 10 AM: `0 10 * * 0`
- Wednesday 6 PM: `0 18 * * 3`

### Change History Lookback

**Node:** "Prepare OpenAI Prompt"

**Find:** `.slice(-21)` (3 weeks)

**Options:**
- 2 weeks: `.slice(-14)`
- 4 weeks: `.slice(-28)`
- 6 weeks: `.slice(-42)`

---

## 🛒 Picnic Integration (Optional)

**Status:** Requires additional setup

**Steps:**
1. Install package: `npm install picnic-api`
2. Enable npm modules in n8n settings
3. Create Picnic credential (email/password)
4. Uncomment code in "Add to Picnic Cart" node
5. Link credential

**Note:** Cart auto-populates but you must manually confirm order

---

## 🐛 Quick Troubleshooting

| Problem | Solution |
|---------|----------|
| No email | Check Gmail spam, verify OAuth2 credential |
| OpenAI error | Check API balance, verify key |
| Picnic error | Skip it (workflow works without) |
| Repeated meals | Increase history lookback number |
| Wrong email | Update "Combine Email Data" node |

---

## 📦 What You Get Every Friday

- ✉️ HTML email with 7 dinner recipes
- ⚡ At least 3 quick meals (15-20 min)
- 🥗 Healthy, kid-friendly, weight-loss focused
- 🛒 Consolidated shopping list by category
- 🎯 Nutrition info (calories, protein)
- 👶 Kid-friendly ratings
- 📝 Picnic cart status (if configured)

---

## 💡 Pro Tips

1. **First run:** Test manually before Friday
2. **Dietary needs:** Be specific in restrictions
3. **Picnic fallback:** Workflow works great with just email shopping list
4. **Cost savings:** Use GPT-3.5-Turbo instead of GPT-4o (~87% cheaper)
5. **Customization:** All code is editable in the nodes

---

## 📞 Need Help?

- **Setup Guide:** See `WEEKLY_MENU_SETUP_GUIDE.md`
- **Detailed Plan:** See plan file in `.claude/plans/`
- **n8n Community:** https://community.n8n.io
- **Workflow Direct Link:** https://n8n.prtsr.nl/workflow/fnLgg88KLhFRlu3T

---

**Status:** ✅ Workflow created and ready for your configuration!
