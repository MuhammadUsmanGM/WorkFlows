# LinkedIn Lead Generation Machine - Setup Guide

## 🚀 Overview

This n8n workflow automates your entire LinkedIn lead generation process:
- ✅ Finds verified email addresses
- ✅ Enriches leads with company data
- ✅ AI-powered lead scoring (0-100 points)
- ✅ Generates personalized outreach messages
- ✅ Saves to CRM automatically
- ✅ Notifies your team on hot leads

**Expected Results:**
- Save 20+ hours/week on manual prospecting
- 3x higher response rates with AI personalization
- Automatic qualification and prioritization

---

## 📋 Prerequisites

### Required Services & API Keys

1. **n8n** (self-hosted or cloud)
   - Version 1.0.0 or higher
   - Download: https://n8n.io

2. **Prospeo.io** (Email Finder)
   - Get API key: https://prospeo.io
   - Free tier: 75 credits/month
   - Paid: $49/month for 2,500 credits
   - **What it does:** Finds verified emails from LinkedIn URLs

3. **Apollo.io** (Data Enrichment)
   - Get API key: https://apollo.io
   - Free tier: 50 credits/month
   - Paid: $49/month for 10,000 credits
   - **What it does:** Enriches with company size, industry, job details

4. **Anthropic Claude API** (AI Personalization)
   - Get API key: https://console.anthropic.com
   - Pay-as-you-go: ~$0.03 per message
   - **What it does:** Generates personalized outreach messages

5. **Airtable** (CRM/Database)
   - Get API key: https://airtable.com/create/tokens
   - Free tier: Unlimited bases
   - **What it does:** Stores and manages your leads

6. **Slack** (Optional - Team Notifications)
   - Create app: https://api.slack.com/apps
   - Free tier available
   - **What it does:** Alerts team on hot leads

---

## 🛠️ Step-by-Step Setup

### Step 1: Import the Workflow

1. Open your n8n instance
2. Click **Workflows** in the sidebar
3. Click **Import from File**
4. Upload `linkedin-lead-gen-workflow.json`
5. Click **Import**

### Step 2: Set Up Airtable Base

1. Go to https://airtable.com and create a new base
2. Name it **"Lead Database"**
3. Create a table called **"Leads"** with these fields:

| Field Name | Field Type | Description |
|------------|------------|-------------|
| first_name | Single line text | Lead's first name |
| last_name | Single line text | Lead's last name |
| email | Email | Verified email address |
| company | Single line text | Company name |
| title | Single line text | Job title |
| linkedin_url | URL | LinkedIn profile URL |
| lead_score | Number | Score 0-100 |
| priority | Single select | Options: Hot, Warm, Medium, Low |
| personalized_message | Long text | AI-generated message |
| status | Single select | Options: Ready to Contact, Contacted, Replied, Nurture |
| date_added | Date | When lead was added |

4. Copy your **Base ID** from the URL:
   - URL looks like: `https://airtable.com/app[BASE_ID]/tbl...`
   - Example: `appXYZ123ABC`

5. Create an API token:
   - Go to https://airtable.com/create/tokens
   - Click **Create new token**
   - Name: "n8n Lead Gen"
   - Add scope: `data.records:write` and `data.records:read`
   - Add your base to the token
   - **Copy and save the token**

### Step 3: Configure API Credentials in n8n

#### 3.1 Prospeo API (Email Finder)

1. In n8n, go to **Settings** → **Credentials**
2. Click **Create New Credential**
3. Select **Header Auth**
4. Fill in:
   - **Name:** `Prospeo API`
   - **Credential Data:**
     - Header Name: `X-KEY`
     - Header Value: `YOUR_PROSPEO_API_KEY`
5. Click **Save**

#### 3.2 Apollo API (Data Enrichment)

1. Create new credential
2. Select **Header Auth**
3. Fill in:
   - **Name:** `Apollo API`
   - **Credential Data:**
     - Header Name: `api_key`
     - Header Value: `YOUR_APOLLO_API_KEY`
4. Click **Save**

#### 3.3 Anthropic Claude API

1. Create new credential
2. Select **Anthropic API**
3. Fill in:
   - **Name:** `Anthropic API`
   - **API Key:** `YOUR_ANTHROPIC_API_KEY`
4. Click **Save**

#### 3.4 Airtable API

1. Create new credential
2. Select **Airtable Personal Access Token API**
3. Fill in:
   - **Name:** `Airtable API`
   - **Access Token:** `YOUR_AIRTABLE_TOKEN`
4. Click **Save**

#### 3.5 Slack API (Optional)

1. Create a Slack App:
   - Go to https://api.slack.com/apps
   - Click **Create New App** → **From scratch**
   - Name: "Lead Alerts"
   - Select your workspace

2. Add permissions:
   - Go to **OAuth & Permissions**
   - Add scope: `chat:write`
   - Click **Install to Workspace**
   - **Copy the Bot User OAuth Token**

3. In n8n:
   - Create new credential
   - Select **Slack API**
   - **Access Token:** `YOUR_SLACK_BOT_TOKEN`
   - Click **Save**

### Step 4: Configure Workflow Nodes

Open the imported workflow and update these nodes:

#### 4.1 "Find Email" Node
- Click the node
- Select credential: **Prospeo API**
- No other changes needed

#### 4.2 "Enrich Lead Data" Node
- Click the node
- Select credential: **Apollo API**
- No other changes needed

#### 4.3 "AI Message Personalization" Node
- Click the node
- Select credential: **Anthropic API**
- **IMPORTANT:** Customize the prompt with your value proposition:
  ```
  Our value proposition: [YOUR UNIQUE VALUE PROP]
  ```
  Example: "We help SaaS companies automate customer onboarding and reduce churn by 40%"

#### 4.4 "Save to CRM (Airtable)" Node
- Click the node
- Select credential: **Airtable API**
- Select Base: **Lead Database**
- Select Table: **Leads**
- Verify field mappings are correct

#### 4.5 "Save Warm/Medium Leads" Node
- Click the node
- Select credential: **Airtable API**
- Select Base: **Lead Database**
- Select Table: **Leads**

#### 4.6 "Notify Team (Slack)" Node (Optional)
- Click the node
- Select credential: **Slack API**
- **Channel:** Enter your Slack channel ID or name (e.g., `#sales-alerts`)
- Customize the message format if desired

### Step 5: Customize Lead Scoring

The workflow includes an AI-powered scoring algorithm. Customize it for your ideal customer profile (ICP):

1. Click on **"AI Lead Scoring"** node
2. Edit the JavaScript code:

```javascript
// Customize these values based on YOUR ideal customer

// Job Titles to prioritize
const seniorTitles = ['CEO', 'CTO', 'VP', 'Director', 'Head of', 'Chief', 'Founder'];

// Target Industries
const targetIndustries = ['SaaS', 'Technology', 'Software', 'E-commerce', 'Fintech'];

// Company Size (adjust scoring)
if (companySize >= 200) score += 25;  // Enterprise
if (companySize >= 50) score += 15;   // Mid-market
if (companySize >= 10) score += 10;   // Small business
```

3. Adjust scoring thresholds:
```javascript
if (score >= 70) priority = 'Hot';      // Immediate outreach
if (score >= 50) priority = 'Warm';     // Outreach within 24h
if (score >= 30) priority = 'Medium';   // Add to nurture campaign
```

---

## 🎯 How to Use the Workflow

### Method 1: Manual Webhook Input (Recommended for Testing)

1. Click on **"Webhook (Manual Lead Input)"** node
2. Copy the **Production URL**
   - Example: `https://your-n8n.com/webhook/linkedin-lead`

3. Send a POST request with lead data:

```bash
curl -X POST https://your-n8n.com/webhook/linkedin-lead \
  -H "Content-Type: application/json" \
  -d '{
    "linkedin_url": "https://linkedin.com/in/johndoe",
    "first_name": "John",
    "last_name": "Doe",
    "company": "Acme Corp",
    "title": "VP of Sales"
  }'
```

Or use this simple HTML form:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Lead Input</title>
</head>
<body>
    <h2>Add LinkedIn Lead</h2>
    <form id="leadForm">
        <input type="text" name="linkedin_url" placeholder="LinkedIn URL" required><br>
        <input type="text" name="first_name" placeholder="First Name" required><br>
        <input type="text" name="last_name" placeholder="Last Name" required><br>
        <input type="text" name="company" placeholder="Company" required><br>
        <input type="text" name="title" placeholder="Job Title" required><br>
        <button type="submit">Process Lead</button>
    </form>

    <script>
        document.getElementById('leadForm').addEventListener('submit', async (e) => {
            e.preventDefault();
            const formData = new FormData(e.target);
            const data = Object.fromEntries(formData);
            
            const response = await fetch('YOUR_WEBHOOK_URL', {
                method: 'POST',
                headers: {'Content-Type': 'application/json'},
                body: JSON.stringify(data)
            });
            
            const result = await response.json();
            alert('Lead processed! Score: ' + result.lead_score + '/100');
        });
    </script>
</body>
</html>
```

### Method 2: Scheduled Automation

The workflow includes a daily trigger at 9 AM. To use it:

1. Connect it to a LinkedIn scraper or lead source:
   - **PhantomBuster** (recommended): https://phantombuster.com
   - **LinkedIn Sales Navigator** exports
   - **Apollo.io** list exports
   - Custom Chrome extension

2. Export leads to a Google Sheet or webhook endpoint
3. Modify the **"Schedule Trigger"** node to read from your source

### Method 3: Zapier/Make.com Integration

1. Use the webhook URL from Method 1
2. Create a Zap/Scenario that:
   - Triggers when new lead is added to your source
   - Sends data to the n8n webhook

---

## 🧪 Testing the Workflow

### Test with Sample Data

1. Open the workflow
2. Click **Execute Workflow** (top right)
3. Click on **"Webhook (Manual Lead Input)"**
4. Click **Listen for Test Event**
5. Send a test request (use curl command above)
6. Watch the workflow execute node by node

**Sample Test Lead:**
```json
{
  "linkedin_url": "https://linkedin.com/in/satyanadella",
  "first_name": "Satya",
  "last_name": "Nadella",
  "company": "Microsoft",
  "title": "CEO"
}
```

### Verify Output

Check that:
- ✅ Email was found (check "Find Email" node output)
- ✅ Data was enriched (check "Enrich Lead Data" node output)
- ✅ Lead score was calculated (should be high for CEO)
- ✅ AI message was generated (check "AI Message Personalization" output)
- ✅ Lead appears in Airtable
- ✅ Slack notification sent (if configured)

---

## 🎨 Customization Ideas

### 1. Add More Data Sources

**LinkedIn Sales Navigator Scraper:**
```javascript
// Add this as a new trigger node
// Use PhantomBuster or Apify to scrape Sales Navigator
// Then connect to the "Prepare Data" node
```

### 2. Multi-Touch Outreach Sequence

Add these nodes after "Save to CRM":
1. **Wait** node (2 days)
2. **Check if replied** (query Airtable)
3. **Send follow-up** (via Gmail or LinkedIn API)
4. **Wait** (5 days)
5. **Final follow-up**

### 3. A/B Test Messages

Modify the "AI Message Personalization" to generate 2 variants:
```javascript
// Generate message A (problem-focused)
// Generate message B (solution-focused)
// Randomly assign to leads
// Track response rates in Airtable
```

### 4. Lead Nurture Campaign

For "Medium" and "Low" priority leads:
1. Add to email marketing tool (Mailchimp, HubSpot)
2. Create drip campaign
3. Auto-upgrade to "Hot" when they engage

### 5. Company-Level Intelligence

Add after "Enrich Lead Data":
- Scrape company website for recent news
- Check Crunchbase for funding rounds
- Monitor competitor job postings
- Add context to AI personalization

---

## 💰 Pricing Breakdown

### Estimated Monthly Costs

For **500 leads/month:**

| Service | Free Tier | Paid Plan | Cost |
|---------|-----------|-----------|------|
| n8n | Self-hosted | Cloud | $0 - $50 |
| Prospeo | 75 leads | 2,500 leads | $49 |
| Apollo | 50 leads | 10,000 leads | $49 |
| Anthropic | $5 free | Pay-per-use | ~$15 |
| Airtable | Unlimited | Pro features | $0 - $20 |
| Slack | Free | - | $0 |
| **Total** | - | **$113-183/month** | |

**ROI Calculation:**
- Manual time saved: 20 hours/week = 80 hours/month
- At $50/hour: **$4,000 saved**
- Cost: $150/month
- **Net savings: $3,850/month**

---

## 🚨 Troubleshooting

### Issue: Email not found

**Solution:**
- Prospeo has limits on certain domains
- Try alternative: Hunter.io, RocketReach
- Fallback to generic email patterns (first.last@company.com)

### Issue: Lead score always low

**Solution:**
- Check that Apollo is returning company_size data
- Verify your ICP matches the scoring criteria
- Lower the "Hot" threshold from 70 to 50

### Issue: AI messages too generic

**Solution:**
- Add more context to the prompt
- Include recent company news
- Reference specific pain points
- Use GPT-4 instead of Claude if preferred

### Issue: Airtable not saving

**Solution:**
- Verify Base ID is correct
- Check field names match exactly (case-sensitive)
- Ensure token has write permissions
- Test with manual execution first

### Issue: Workflow times out

**Solution:**
- Process leads in smaller batches
- Add error handling nodes
- Increase workflow timeout in settings
- Use async processing for large volumes

---

## 📈 Scaling Strategies

### For 1,000+ Leads/Month

1. **Use Batch Processing:**
   - Split workflow into batches of 50
   - Add queue system with Redis
   - Process during off-peak hours

2. **Optimize API Calls:**
   - Cache enrichment data
   - Use bulk API endpoints where available
   - Implement rate limiting

3. **Database Upgrade:**
   - Move from Airtable to PostgreSQL
   - Use n8n's built-in database nodes
   - Add indexing for faster queries

4. **Multi-Channel Output:**
   - Send to HubSpot, Salesforce, Pipedrive
   - Sync with email marketing tools
   - Export to Google Sheets for analysis

---

## 🛡️ Best Practices & Compliance

### GDPR & Data Privacy

- ✅ Only store necessary data
- ✅ Implement data retention policies (delete after 90 days)
- ✅ Provide unsubscribe mechanism
- ✅ Document data sources and consent
- ✅ Add privacy policy link to outreach

### LinkedIn Terms of Service

- ⚠️ LinkedIn prohibits automated scraping
- ✅ Use official Sales Navigator API when possible
- ✅ Manual exports are generally acceptable
- ✅ Space out requests (1-2 per minute max)
- ✅ Don't scrape public profiles without consent

### Email Deliverability

- ✅ Verify emails before sending
- ✅ Warm up new email domains
- ✅ Keep bounce rate under 3%
- ✅ Include unsubscribe link
- ✅ Monitor spam complaints

---

## 🎓 Training Resources

### Learn n8n
- Official docs: https://docs.n8n.io
- YouTube: n8n official channel
- Community: https://community.n8n.io

### Lead Generation Best Practices
- "Predictable Revenue" by Aaron Ross
- "Fanatical Prospecting" by Jeb Blount
- Cold Email Playbook: https://www.coldiq.com

### AI Prompt Engineering
- Anthropic's prompt library: https://docs.anthropic.com/prompts
- OpenAI best practices: https://platform.openai.com/docs/guides/prompt-engineering

---

## 💬 Support

### Need Help?

1. **n8n Community:** https://community.n8n.io
2. **GitHub Issues:** Report bugs and request features
3. **Documentation:** Check official API docs for each service

### Selling This Workflow?

If you're selling this as a service:
- Charge setup fee: $1,500-3,000
- Monthly retainer: $200-500 for maintenance
- Add 50% markup on API costs
- Offer custom integrations at hourly rate

---

## 📝 Changelog

**v1.0.0** (2025-01-08)
- Initial release
- Core workflow with email finding, enrichment, scoring
- AI personalization with Claude
- Airtable integration
- Slack notifications

**Planned Features:**
- Multi-language support
- A/B testing framework
- Reply detection and tracking
- Automated follow-up sequences
- Chrome extension for 1-click lead capture

---

## ✅ Quick Start Checklist

- [ ] Import workflow to n8n
- [ ] Create Airtable base with correct fields
- [ ] Get API keys for all services
- [ ] Configure credentials in n8n
- [ ] Update Airtable Base ID in nodes
- [ ] Customize lead scoring criteria
- [ ] Add your value proposition to AI prompt
- [ ] Test with sample lead
- [ ] Verify lead appears in Airtable
- [ ] Check Slack notification (if enabled)
- [ ] Activate workflow
- [ ] Process first real lead
- [ ] Monitor and optimize

**Estimated setup time:** 45-60 minutes

---

**Ready to 10x your lead generation? Start importing the workflow now! 🚀**

For questions or custom development, reach out to your n8n consultant or community.
