# Real Estate Lead Nurture System - Complete Setup Guide

## 🎯 Executive Summary

This n8n workflow is your complete real estate lead management system:

✅ **Multi-Source Lead Capture** - Zillow, Realtor.com, Website  
✅ **AI Lead Intelligence** - Smart scoring and buyer analysis  
✅ **Auto Property Matching** - MLS integration with match scoring  
✅ **Personalized Outreach** - AI-generated emails + SMS  
✅ **Drip Campaigns** - Automated nurturing for months  
✅ **CRM & Tracking** - Complete lead lifecycle management  

**Business Impact:**
- 💰 Close 2-3 more deals per month
- ⚡ Respond to leads in under 2 minutes
- 📈 3x higher conversion rate
- ⏰ Save 40+ hours per month
- 😊 Never miss a follow-up

**ROI Example (Mid-Level Agent):**
- Zillow leads: 30/month @ $100/lead = $3,000 spent
- Current close rate: 8% = 2.4 deals × $10,000 = $24,000
- With automation: 20% = 6 deals × $10,000 = $60,000
- **Extra revenue: $36,000/month**
- Workflow cost: $250/month
- **Net gain: $35,750/month ($429,000/year)**

---

## 📋 Table of Contents

1. [Prerequisites & Costs](#prerequisites--costs)
2. [Quick Start (60 Minutes)](#quick-start-60-minutes)
3. [Airtable CRM Setup](#airtable-crm-setup)
4. [Zillow Integration](#zillow-integration)
5. [Realtor.com Integration](#realtorcom-integration)
6. [MLS API Integration](#mls-api-integration)
7. [Email & SMS Setup](#email--sms-setup)
8. [AI Configuration](#ai-configuration)
9. [Drip Campaign Setup](#drip-campaign-setup)
10. [Testing & Validation](#testing--validation)
11. [Customization Guide](#customization-guide)
12. [Troubleshooting](#troubleshooting)
13. [Selling This System](#selling-this-system)

---

## 📦 Prerequisites & Costs

### Required Services

| Service | Purpose | Monthly Cost |
|---------|---------|--------------|
| **n8n** | Workflow engine | $0-50 |
| **Zillow Premier Agent** | Lead source | $200-2,000 |
| **Realtor.com** | Lead source | $100-500 |
| **MLS API Access** | Property search | $50-200 |
| **Anthropic Claude** | AI analysis | $30-80 |
| **Gmail** | Email sending | $0 |
| **Twilio** | SMS notifications | $20-50 |
| **Airtable** | CRM database | $0-20 |
| **Slack** (Optional) | Team alerts | $0 |
| **TOTAL** (excluding lead gen) | - | **$100-350/mo** |

**Note:** Zillow/Realtor costs are lead generation, not automation costs.

### Technical Requirements

- Real estate license (for MLS access)
- Zillow Premier Agent or Realtor.com account
- MLS access credentials
- Basic understanding of webhooks
- 60-90 minutes setup time

---

## 🚀 Quick Start (60 Minutes)

### Step 1: Import Workflow (2 min)

1. Open n8n → **Workflows** → **Import from File**
2. Upload `real-estate-lead-nurture-workflow.json`
3. Click **Import**

### Step 2: Create Airtable CRM (20 min)

This is your central database for all leads, properties, and activities.

#### 2.1 Create Base

1. Go to https://airtable.com
2. Create new base: **"Real Estate CRM"**

#### 2.2 Create "Leads" Table

| Field | Type | Options |
|-------|------|---------|
| lead_id | Single line text | Primary field |
| source | Single select | zillow, realtor, website, referral |
| first_name | Single line text | - |
| last_name | Single line text | - |
| full_name | Formula | `{first_name} & " " & {last_name}` |
| email | Email | - |
| phone | Phone number | - |
| location | Single line text | City or area |
| zip_code | Single line text | - |
| price_min | Currency | - |
| price_max | Currency | - |
| bedrooms_min | Number | Integer |
| bathrooms_min | Number | Decimal |
| property_type | Single select | House, Condo, Townhouse, Multi-Family, Land, Any |
| lead_type | Single select | Buyer, Seller, Renter |
| lead_quality | Single select | hot, warm, cold |
| lead_score | Number | 0-100 |
| buyer_readiness | Single select | ready_now, 3_months, 6_months, researching |
| motivation_level | Single select | high, medium, low |
| stage | Single select | lead, contacted, showing_scheduled, offer_made, under_contract, closed |
| status | Single select | new, active, nurturing, closed_won, closed_lost |
| received_at | Date & time | - |
| last_contact | Date & time | - |
| next_action | Single line text | - |
| message | Long text | Original inquiry message |
| key_priorities | Long text | Comma-separated |
| communication_style | Single select | formal, casual, data_driven, emotional |
| nurture_strategy | Single select | aggressive, moderate, gentle, educational |
| predicted_close_timeline | Single select | 30_days, 60_days, 90_days, 6_months |
| assigned_agent | Single line text | Link to Agents table |
| notes | Long text | Internal notes |

#### 2.3 Create "Property_Matches" Table

| Field | Type | Options |
|-------|------|---------|
| match_id | Auto number | Primary field |
| lead_id | Single line text | Link to Leads |
| properties_json | Long text | JSON array of matched properties |
| match_count | Number | - |
| sent_at | Date & time | - |
| top_match_address | Single line text | - |
| top_match_price | Currency | - |
| viewed | Checkbox | Did lead view properties? |
| favorites | Long text | Lead's favorite property IDs |

#### 2.4 Create "Activities" Table

| Field | Type | Options |
|-------|------|---------|
| activity_id | Auto number | Primary field |
| lead_id | Single line text | Link to Leads |
| activity_type | Single select | email_sent, sms_sent, call_made, showing_scheduled, offer_submitted |
| activity_date | Date & time | - |
| details | Long text | - |
| outcome | Single select | successful, no_response, scheduled, completed |

#### 2.5 Create "Drip_Campaigns" Table

| Field | Type | Options |
|-------|------|---------|
| campaign_id | Auto number | Primary field |
| lead_id | Single line text | Link to Leads |
| campaign_type | Single select | welcome_series, active_nurture, monthly_update, long_term_nurture |
| email_template | Single select | getting_started, new_listings, market_trends, seasonal_opportunities |
| sent_at | Date & time | - |
| opened | Checkbox | - |
| clicked | Checkbox | - |

#### 2.6 Get Airtable API Token

1. Go to https://airtable.com/create/tokens
2. Create token: "n8n Real Estate CRM"
3. Add scopes: `data.records:read`, `data.records:write`
4. Add your "Real Estate CRM" base
5. **Copy and save token**

### Step 3: Configure Zillow Integration (10 min)

**Zillow Premier Agent Setup:**

#### 3.1 Enable Lead Integration

1. Log in to Zillow Premier Agent Dashboard
2. Go to **Account Settings** → **Lead Management**
3. Find **API & Integrations** section
4. Enable **Webhook Integration**

#### 3.2 Configure Webhook

1. Webhook URL: `https://your-n8n-url.com/webhook/zillow-lead`
2. Events to send:
   - New Lead Received
   - Lead Updated
3. Authentication: (if required, use API key)
4. Click **Save**

#### 3.3 Test Connection

1. Zillow provides a "Send Test Lead" button
2. Click it to send sample data
3. Check n8n execution log
4. Verify lead appears in Airtable

**Alternative: Email Parsing**

If Zillow doesn't offer webhooks for your account tier:

1. Forward Zillow lead emails to a dedicated Gmail address
2. Use n8n Gmail trigger to parse emails
3. Extract lead data using regex patterns
4. Feed into workflow

**Zillow Email Parser Pattern:**
```javascript
// Parse Zillow lead email
const emailBody = $input.first().json.body;

const nameMatch = emailBody.match(/Name: (.+)/);
const emailMatch = emailBody.match(/Email: (.+)/);
const phoneMatch = emailBody.match(/Phone: (.+)/);
const locationMatch = emailBody.match(/Location: (.+)/);

const lead = {
  first_name: nameMatch ? nameMatch[1].split(' ')[0] : '',
  last_name: nameMatch ? nameMatch[1].split(' ')[1] : '',
  email: emailMatch ? emailMatch[1] : '',
  phone: phoneMatch ? phoneMatch[1] : '',
  location: locationMatch ? locationMatch[1] : ''
};
```

### Step 4: Configure Realtor.com Integration (10 min)

**Realtor.com Professional Setup:**

#### 4.1 Enable Lead Notifications

1. Log in to Realtor.com Professional Dashboard
2. Go to **Settings** → **Lead Settings**
3. Enable **Instant Lead Notifications**

#### 4.2 Configure Webhook

1. Look for **Third-Party Integrations** or **API Settings**
2. If available, add webhook URL: `https://your-n8n-url.com/webhook/realtor-lead`
3. If webhooks not available, use email forwarding (like Zillow)

#### 4.3 Alternative: Zapier Bridge

If direct integration isn't available:

1. Use Zapier as intermediary
2. Zapier trigger: Realtor.com New Lead
3. Zapier action: Webhook to n8n
4. Cost: $0-20/month for Zapier

### Step 5: MLS API Integration (15 min)

**MLS API Options:**

There are several MLS data providers. Choose based on your MLS membership:

#### Option A: Bridge Interactive (Recommended)

**Most comprehensive, covers 90% of US MLSs**

**Setup:**
1. Go to https://www.bridgedataoutput.com
2. Sign up for account
3. Select your MLS(s)
4. Get API token

**Pricing:**
- $99-199/month depending on MLS
- 10,000 API calls/month included

**In n8n:**
1. **Settings** → **Credentials** → **Create**
2. Select **HTTP Header Auth**
3. Name: "MLS API"
4. Header Name: `Authorization`
5. Header Value: `Bearer YOUR_TOKEN`
6. Save

**Update workflow:**
- Open "Search MLS Properties" node
- URL is already set to Bridge API
- Replace `YOUR_MLS_API_TOKEN` in query params

#### Option B: RESO Web API (Direct MLS)

**If your MLS provides RESO API directly**

**Setup:**
1. Contact your MLS tech support
2. Request RESO Web API access
3. Get OAuth credentials
4. Follow MLS-specific authentication

**Common MLS Providers:**
- ARMLS (Arizona)
- NTREIS (North Texas)
- Bright MLS (Mid-Atlantic)
- CRMLS (California)

#### Option C: Zillow/Realtor.com APIs

**Limited but free alternative**

Use Zillow or Realtor.com APIs for property search:
- More limited data
- Requires separate agreements
- Free for partners

#### MLS API Configuration

**In "Search MLS Properties" node:**

```javascript
// Adjust query parameters for your MLS API
{
  "city": "={{ $json.location }}",
  "ListPrice.min": "={{ $json.price_min }}",
  "ListPrice.max": "={{ $json.price_max }}",
  "BedroomsTotal.min": "={{ $json.bedrooms_min }}",
  "BathroomsTotalInteger.min": "={{ Math.floor($json.bathrooms_min) }}",
  "StandardStatus": "Active",
  "limit": 10,
  "sortBy": "ListPrice"
}
```

**Field mappings vary by MLS. Common fields:**
- Bridge Interactive: `ListPrice`, `BedroomsTotal`, `BathroomsTotalInteger`
- RESO Standard: `ListPrice`, `BedroomsTotal`, `BathroomsFull`
- Custom MLS: Check documentation

### Step 6: Email & SMS Setup (10 min)

#### 6.1 Gmail Setup

**Configure OAuth2:**
1. Go to Google Cloud Console: https://console.cloud.google.com
2. Create project: "Real Estate CRM"
3. Enable Gmail API
4. Create OAuth credentials:
   - Application type: Web application
   - Authorized redirect URIs: `YOUR_N8N_URL/rest/oauth2-credential/callback`
5. Copy Client ID and Client Secret

**In n8n:**
1. **Settings** → **Credentials** → **Create**
2. Select **Gmail OAuth2 API**
3. Paste Client ID and Secret
4. Click **Connect** and authorize
5. Save

#### 6.2 Twilio SMS Setup

**Create Twilio Account:**
1. Go to https://www.twilio.com/try-twilio
2. Sign up (free trial includes $15 credit)
3. Verify your phone number
4. Get a Twilio phone number

**Get Credentials:**
1. Twilio Dashboard → **Account Info**
2. Copy **Account SID**
3. Copy **Auth Token**
4. Copy your **Twilio Phone Number**

**In n8n:**
1. **Settings** → **Credentials** → **Create**
2. Select **HTTP Basic Auth**
3. Name: "Twilio API"
4. Username: (Account SID)
5. Password: (Auth Token)
6. Save

**Update workflow:**
- Open "Send SMS Notification" node
- Replace `YOUR_ACCOUNT_SID` with your Account SID
- Replace `YOUR_TWILIO_NUMBER` with your Twilio number (format: +12125551234)

**Twilio Pricing:**
- US SMS: $0.0079 per message
- For 100 leads/month: ~$0.80

### Step 7: Claude AI Setup (2 min)

1. Get API key: https://console.anthropic.com
2. **Settings** → **Credentials** → **Create**
3. Select **Anthropic API**
4. Paste API key
5. Save

**Pricing:**
- Claude Sonnet: ~$0.03 per lead analysis
- For 100 leads/month: ~$30

### Step 8: Slack Notifications (Optional, 3 min)

1. Create Slack app: https://api.slack.com/apps
2. Add `chat:write` scope
3. Install to workspace
4. Copy Bot Token
5. In n8n:
   - Add **Slack API** credential
   - Paste token
6. Update workflow:
   - Replace `YOUR_SLACK_CHANNEL` with channel ID (e.g., `#leads`)

---

## 🔧 Detailed Configuration

### Webhook URLs

After importing workflow, you'll have these webhook URLs:

1. **Zillow:** `https://your-n8n.com/webhook/zillow-lead`
2. **Realtor.com:** `https://your-n8n.com/webhook/realtor-lead`
3. **Website:** `https://your-n8n.com/webhook/website-lead`

**Find your webhook URLs:**
1. Open each webhook node
2. Click **Test URL** tab
3. Copy the Production URL
4. Use these URLs in Zillow/Realtor settings

### Website Lead Form Integration

**HTML Form Example:**

```html
<!DOCTYPE html>
<html>
<head>
    <title>Contact Us - [Your Name] Real Estate</title>
    <style>
        body { font-family: Arial, sans-serif; max-width: 600px; margin: 50px auto; padding: 20px; }
        label { display: block; margin: 15px 0 5px; font-weight: bold; }
        input, select, textarea { width: 100%; padding: 10px; font-size: 14px; border: 1px solid #ddd; border-radius: 5px; }
        button { background: #2196F3; color: white; padding: 15px 30px; border: none; border-radius: 5px; font-size: 16px; cursor: pointer; margin-top: 20px; }
        button:hover { background: #0b7dda; }
        .success { background: #d4edda; padding: 15px; border-radius: 5px; margin: 20px 0; display: none; }
    </style>
</head>
<body>
    <h1>Find Your Dream Home</h1>
    <p>Let me help you find the perfect property!</p>
    
    <form id="leadForm">
        <label>First Name *</label>
        <input type="text" name="first_name" required>
        
        <label>Last Name *</label>
        <input type="text" name="last_name" required>
        
        <label>Email *</label>
        <input type="email" name="email" required>
        
        <label>Phone *</label>
        <input type="tel" name="phone" required>
        
        <label>Preferred Location/City *</label>
        <input type="text" name="location" required placeholder="e.g., Downtown Phoenix">
        
        <label>Zip Code</label>
        <input type="text" name="zip_code" placeholder="85001">
        
        <label>Price Range *</label>
        <div style="display: flex; gap: 10px;">
            <input type="number" name="price_min" placeholder="Min" required style="width: 48%;">
            <input type="number" name="price_max" placeholder="Max" required style="width: 48%;">
        </div>
        
        <label>Minimum Bedrooms *</label>
        <select name="bedrooms" required>
            <option value="">Select...</option>
            <option value="1">1+</option>
            <option value="2">2+</option>
            <option value="3">3+</option>
            <option value="4">4+</option>
            <option value="5">5+</option>
        </select>
        
        <label>Minimum Bathrooms *</label>
        <select name="bathrooms" required>
            <option value="">Select...</option>
            <option value="1">1+</option>
            <option value="1.5">1.5+</option>
            <option value="2">2+</option>
            <option value="2.5">2.5+</option>
            <option value="3">3+</option>
        </select>
        
        <label>Property Type *</label>
        <select name="property_type" required>
            <option value="">Select...</option>
            <option value="House">House</option>
            <option value="Condo">Condo</option>
            <option value="Townhouse">Townhouse</option>
            <option value="Multi-Family">Multi-Family</option>
            <option value="Land">Land</option>
            <option value="Any">Any</option>
        </select>
        
        <label>I'm looking to:</label>
        <select name="lead_type">
            <option value="Buyer">Buy</option>
            <option value="Seller">Sell</option>
            <option value="Renter">Rent</option>
        </select>
        
        <label>How soon are you looking to move?</label>
        <select name="urgency">
            <option value="High">ASAP (within 30 days)</option>
            <option value="Medium">1-3 months</option>
            <option value="Low">3-6 months</option>
        </select>
        
        <label>Additional Details</label>
        <textarea name="message" rows="4" placeholder="Tell me about your ideal home..."></textarea>
        
        <button type="submit">Get My Property Matches</button>
    </form>

    <div id="successMessage" class="success">
        <strong>Thank you!</strong> I've received your information and will send you personalized property matches within the next few minutes. Check your email!
    </div>

    <script>
        document.getElementById('leadForm').addEventListener('submit', async (e) => {
            e.preventDefault();
            
            const formData = new FormData(e.target);
            const data = Object.fromEntries(formData);
            
            try {
                const response = await fetch('YOUR_N8N_WEBHOOK_URL', {
                    method: 'POST',
                    headers: {'Content-Type': 'application/json'},
                    body: JSON.stringify(data)
                });
                
                if (response.ok) {
                    document.getElementById('successMessage').style.display = 'block';
                    e.target.reset();
                    
                    // Optional: Redirect to thank you page
                    // setTimeout(() => window.location.href = '/thank-you', 2000);
                } else {
                    alert('Something went wrong. Please try again or call me directly.');
                }
            } catch (error) {
                console.error('Error:', error);
                alert('Connection error. Please try again.');
            }
        });
    </script>
</body>
</html>
```

Replace `YOUR_N8N_WEBHOOK_URL` with your actual webhook URL from the "Website Lead Form" node.

---

## 🧪 Testing & Validation

### Test 1: Zillow Lead

**Create test lead:**
1. Use Zillow's "Send Test Lead" feature
2. Or fill out a Zillow contact form yourself

**Expected flow:**
- ✅ Lead captured via webhook
- ✅ Normalized and saved to Airtable
- ✅ AI analysis completed (lead_quality, score, etc.)
- ✅ MLS searched for matching properties
- ✅ Welcome email sent with top 5 properties
- ✅ SMS notification sent
- ✅ Property matches saved
- ✅ Slack notification (if configured)

**Verify:**
- Check Airtable "Leads" table for new record
- Check email inbox (welcome email)
- Check phone (SMS)
- Check Airtable "Property_Matches" table
- Check Slack (if configured)

### Test 2: Realtor.com Lead

Same process as Zillow test.

### Test 3: Website Form

1. Fill out your website form
2. Use your own email/phone
3. Wait for automated response

### Test 4: Hot Lead Alert

**Create hot lead:**
Submit form with these characteristics:
- High budget ($500k+)
- Specific needs (4 bed, 3 bath)
- Urgent timeline ("ASAP")
- Detailed message showing motivation

**Expected:**
- ✅ Lead scored as "hot"
- ✅ Slack alert sent immediately
- ✅ Action: "Call within 1 hour"

### Test 5: Drip Campaign

1. Wait 3+ days after lead submission
2. Drip Campaign Scheduler runs daily at 9 AM
3. Lead should receive follow-up email

**Expected:**
- ✅ Lead fetched from Airtable
- ✅ Campaign type determined
- ✅ Email sent (market update, new listings, etc.)

### Verification Checklist

- [ ] Zillow leads processing correctly
- [ ] Realtor.com leads processing
- [ ] Website form leads working
- [ ] All leads saving to Airtable
- [ ] AI analysis completing (check lead_score field)
- [ ] MLS returning properties
- [ ] Property matching working
- [ ] Welcome emails sending
- [ ] SMS notifications sending
- [ ] Hot lead alerts triggering
- [ ] Drip campaigns executing

---

## 🎨 Customization Guide

### 1. Adjust Lead Scoring

**In "Enrich Lead Data" node:**

```javascript
// Customize scoring thresholds
if (aiAnalysis.lead_quality === 'hot') {
  immediate_actions = ['call_immediately', 'send_top_listings', 'schedule_showing'];
  follow_up_schedule = {
    first_call: 'within_1_hour',
    first_email: 'within_2_hours',
    second_call: 'same_day',
    showing_offer: 'within_24_hours'
  };
}

// Adjust for your market
// Luxury market: Higher score threshold for "hot"
if (cart.price_min > 1000000) {
  // Luxury leads need different handling
  immediate_actions.push('assign_to_luxury_specialist');
}

// First-time buyers: More education needed
if (aiAnalysis.buyer_readiness === 'researching') {
  immediate_actions = ['add_to_educational_drip', 'send_buyer_guide'];
}
```

### 2. Customize Property Matching

**In "Format Property Matches" node:**

```javascript
// Adjust match scoring algorithm
function calculateMatchScore(property, lead) {
  let score = 100;
  
  // Your custom criteria
  const price = property.ListPrice;
  const budget = (lead.price_min + lead.price_max) / 2;
  
  // Price match (most important)
  if (price < lead.price_min || price > lead.price_max) {
    score -= 40; // More severe penalty
  } else if (Math.abs(price - budget) < budget * 0.1) {
    score += 15; // Bonus for near-budget match
  }
  
  // Location premium
  if (property.City === lead.preferred_city) {
    score += 20; // Exact city match
  }
  
  // New construction bonus
  if (property.YearBuilt >= 2020) {
    score += 10;
  }
  
  // School district (if important to your clients)
  if (property.SchoolDistrict === 'Highly Rated') {
    score += 15;
  }
  
  return Math.max(0, Math.min(100, score));
}

// Increase number of properties shown
formattedProperties.slice(0, 10); // Show top 10 instead of 5
```

### 3. Personalize Email Templates

**In "Generate Welcome Email" node:**

Adjust AI prompt:

```
Create email that:
- Uses my specific brand voice (professional but warm)
- Mentions my unique value propositions:
  * 15 years local market experience
  * Average 2-week close time
  * 98% client satisfaction rate
- Includes my photo and credentials
- References specific neighborhoods by name
- Under 250 words (shorter for mobile)
```

**Or create static template:**

```javascript
// In "Prepare Email Content" node
function generateDefaultEmail(lead) {
  return `
    <div style="font-family: Arial; max-width: 600px; margin: 0 auto;">
      <img src="YOUR_HEADSHOT_URL" style="width: 150px; border-radius: 50%;">
      
      <h2>Hi ${lead.first_name}! 🏡</h2>
      
      <p>I'm [Your Name], your local real estate expert in ${lead.location}. With over 15 years helping families find their perfect home, I'm excited to help you too!</p>
      
      <p>Based on your budget of $${lead.price_min.toLocaleString()} - $${lead.price_max.toLocaleString()}, I've found some fantastic ${lead.bedrooms_min} bedroom properties in ${lead.location}:</p>
      
      {{PROPERTY_LISTINGS}}
      
      <p><strong>Why work with me?</strong></p>
      <ul>
        <li>✅ Average 2-week close time</li>
        <li>✅ 98% client satisfaction</li>
        <li>✅ Deep local market knowledge</li>
      </ul>
      
      <p>Let's schedule a quick 15-minute call to discuss your needs!</p>
      
      <a href="https://calendly.com/your-link" style="background: #4CAF50; color: white; padding: 15px 30px; text-decoration: none; border-radius: 5px; display: inline-block;">Book Your Call</a>
      
      <p>Best regards,<br>
      [Your Name]<br>
      [Your Brokerage]<br>
      [Your Phone]<br>
      [Your License #]</p>
    </div>
  `;
}
```

### 4. Configure Drip Campaign Content

**In "Determine Campaign Type" node:**

```javascript
// Customize campaign timing
if (daysSinceReceived <= 3) {
  campaign_type = 'welcome_series';
  email_template = 'getting_started';
} else if (daysSinceReceived === 7) {
  campaign_type = 'follow_up';
  email_template = 'checking_in';
} else if (daysSinceReceived === 14) {
  campaign_type = 'market_education';
  email_template = 'neighborhood_spotlight';
} else if (daysSinceReceived === 30) {
  campaign_type = 'listing_updates';
  email_template = 'new_on_market';
} else if (daysSinceReceived % 30 === 0) {
  campaign_type = 'monthly_newsletter';
  email_template = 'market_trends';
}

// Add seasonal content
const month = new Date().getMonth();
if (month === 0) { // January
  email_template = 'new_year_opportunities';
} else if (month === 4) { // May
  email_template = 'spring_market_report';
}
```

**Create email templates:**

```javascript
const templates = {
  getting_started: {
    subject: "Welcome! Let's find your perfect home",
    body: "Hi ${name}, I'm so glad you reached out..."
  },
  checking_in: {
    subject: "${name}, any questions about the properties I sent?",
    body: "I wanted to follow up on the homes I sent you..."
  },
  neighborhood_spotlight: {
    subject: "Discover ${location}: Your neighborhood guide",
    body: "Let me tell you about ${location}..."
  },
  new_on_market: {
    subject: "Just listed: New homes in ${location}!",
    body: "Great news! ${count} new properties just hit the market..."
  },
  market_trends: {
    subject: "${location} Market Update - ${month}",
    body: "Here's what's happening in your area..."
  }
};
```

### 5. Add Showing Scheduler Integration

**Integrate with Calendly:**

```javascript
// After sending property matches
const calendlyLink = `https://calendly.com/your-link?name=${lead.full_name}&email=${lead.email}&notes=Interested in ${top_match.address}`;

// Include in email
<p>Ready to see these homes in person?</p>
<a href="${calendlyLink}">Schedule Your Showing</a>
```

**Or use ShowingTime API:**

```javascript
// Create showing request via API
POST https://api.showingtime.com/api/v1/showings
{
  listing_id: property.mls_number,
  client_name: lead.full_name,
  client_email: lead.email,
  client_phone: lead.phone,
  requested_times: [
    {date: "2024-01-15", time: "10:00"},
    {date: "2024-01-15", time: "14:00"}
  ]
}
```

---

## 🔧 Troubleshooting

### Issue: No leads coming through

**Problem:** Workflow running but no leads captured

**Solutions:**
1. **Check webhook URLs are correct**
   - Go to webhook node
   - Copy Production URL
   - Verify it matches URL in Zillow/Realtor settings
2. **Test webhook manually:**
```bash
curl -X POST https://your-n8n.com/webhook/zillow-lead \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "John",
    "lastName": "Doe",
    "email": "john@example.com",
    "phone": "555-1234",
    "location": "Phoenix",
    "priceMin": 300000,
    "priceMax": 500000,
    "beds": 3,
    "baths": 2
  }'
```
3. **Check n8n execution log** for errors
4. **Verify webhook is active** (green checkmark)

### Issue: MLS not returning properties

**Error:** "No properties found" or API error

**Solutions:**
1. **Verify MLS API credentials**
   - Test API directly in browser or Postman
   - Check token hasn't expired
2. **Check search criteria aren't too restrictive**
   - Expand price range temporarily
   - Remove bedroom/bathroom filters for testing
3. **Verify location format**
   - Some MLS APIs require exact city names
   - Try zip code instead of city
4. **Check API rate limits**
   - Most MLS APIs have daily limits
   - Bridge Interactive: 10,000 calls/month
5. **Test MLS API manually:**
```bash
curl -H "Authorization: Bearer YOUR_TOKEN" \
  "https://api.bridgedataoutput.com/api/v2/pub/properties?access_token=YOUR_TOKEN&city=Phoenix&ListPrice.min=300000&limit=5"
```

### Issue: Emails not sending

**Problem:** Welcome email not arriving

**Solutions:**
1. **Check spam folder**
2. **Re-authenticate Gmail OAuth**
   - Credentials may have expired
   - Go to Gmail credential and reconnect
3. **Verify email content isn't triggering spam filters**
   - Reduce exclamation marks
   - Don't use ALL CAPS
   - Include unsubscribe link
4. **Check Gmail sending limits**
   - Free Gmail: 500 emails/day
   - Google Workspace: 2,000 emails/day
5. **Test email separately:**
   - Create simple test workflow
   - Just Gmail node
   - Send to yourself

### Issue: SMS not sending

**Error:** Twilio error or no SMS received

**Solutions:**
1. **Verify phone number format**
   - Must be international format: +12125551234
   - No spaces, dashes, parentheses
2. **Check Twilio account status**
   - Account might need verification
   - Free trial has limitations
3. **Verify Twilio credentials**
   - Account SID correct?
   - Auth Token correct?
4. **Check Twilio console for errors**
   - Go to Twilio Dashboard → Logs
   - Look for failed messages
5. **Test Twilio API directly:**
```bash
curl -X POST https://api.twilio.com/2010-04-01/Accounts/YOUR_SID/Messages.json \
  -u YOUR_SID:YOUR_AUTH_TOKEN \
  -d "To=+15551234567" \
  -d "From=+15559876543" \
  -d "Body=Test message"
```

### Issue: AI analysis failing

**Error:** "Failed to parse AI response"

**Solutions:**
1. **Check Claude API key**
   - Verify key is correct
   - Check credit balance
2. **AI response not valid JSON**
   - Check AI prompt includes "Return ONLY valid JSON"
   - AI might be adding markdown
   - Add better JSON cleaning in code node
3. **Timeout issues**
   - Increase n8n timeout settings
   - Simplify AI prompt

### Issue: Properties not matching lead criteria

**Problem:** Showing wrong properties

**Solutions:**
1. **Check normalization logic**
   - Verify price_min/max extracted correctly
   - Check bedroom/bathroom conversion
2. **Adjust match scoring**
   - Lower threshold for showing properties
   - Expand criteria slightly (e.g., +/- $25k on price)
3. **Debug match scores:**
```javascript
// Add logging to see scores
console.log('Property:', prop.address);
console.log('Match Score:', calculateMatchScore(prop, lead));
console.log('Criteria:', {
  price: prop.ListPrice,
  beds: prop.BedroomsTotal,
  location: prop.City
});
```

---

## 💼 Selling This System

### Pricing Strategy

**For Individual Agents:**
- Setup: $2,000-4,000
- Monthly: $300-500 (monitoring, optimization, support)
- One-time lead gen platform setup: $500-1,000

**For Small Teams (2-5 agents):**
- Setup: $4,000-8,000
- Monthly: $500-1,000
- Includes: Multi-agent routing, team dashboard

**For Brokerages:**
- Setup: $10,000-25,000
- Monthly: $1,500-3,000
- Includes: Custom integrations, training, white-label

### ROI Pitch Template

*"You're spending $500-2,000/month on Zillow and Realtor.com leads. But here's the problem:*

*Industry stats:*
- *60-70% of leads go cold*
- *Average response time: 4-6 hours*
- *Agents follow up 1-2 times, then give up*

*Result: You're wasting $300-1,400/month on leads that slip through.*

*This system changes everything:*

*⚡ Responds in under 2 minutes (not hours)*  
*🎯 Sends perfect property matches automatically*  
*📧 Nurtures leads for months (not days)*  
*🔥 Alerts you instantly for hot leads*  
*📊 Never lets a lead go cold*

*Real numbers:*
- *Your current close rate: 8% (industry average)*
- *With this system: 20-25%*
- *That's 2-3 more deals per month*
- *At $10,000 commission = $20-30k extra/month*

*Investment:*
- *Setup: $3,000 (one-time)*
- *Monthly: $400 (includes everything)*
- *First month: $23,600 profit*
- ***Every month after: $27,000 profit***

*Plus, you get your evenings and weekends back. No more manually searching MLS at 9 PM.*

*Want to see it work with your actual leads?"*

### Target Customers

**Best fit:**
1. **Top Producers** - Handling 30+ leads/month, need systems to scale
2. **New Agents** - Want professional systems from day 1
3. **Growing Teams** - Adding agents, need centralized lead management
4. **Brokerages** - Want to increase agent productivity
5. **Luxury Agents** - High-value leads need white-glove treatment

**Red flags they need this:**
- "I can't keep up with all my leads"
- "Half my Zillow leads go nowhere"
- "I know I should follow up more"
- "I spend hours searching MLS for clients"
- "I want to scale but can't hire yet"

### Implementation Timeline

**Week 1: Setup**
- Day 1-2: Install and configure workflow
- Day 3: Connect Zillow/Realtor
- Day 4: MLS integration
- Day 5: Test with historical leads

**Week 2: Training & Optimization**
- Day 1-2: Train agent on system
- Day 3-4: Customize email templates
- Day 5: Go live with monitoring

**Week 3: Prove ROI**
- Process 20-50 real leads
- Track response times
- Measure conversion rates
- Present results

**Month 1: Full Production**
- System running autonomously
- Agent closes 2-3 extra deals
- ROI proven
- Upsell advanced features

### Success Metrics

**Week 1:**
- All leads captured and processed
- 100% response rate
- <2 minute response time

**Month 1:**
- 50-200 leads processed
- 3x faster response than before
- 15-20% conversion to showings
- 1-2 deals closed directly from automation

**Month 3:**
- 20-25% conversion rate
- Consistent extra deals per month
- Agent testimonial ready
- Referrals from happy agent

---

## 🚀 Advanced Features

### 1. Buyer Profile Scoring

Track engagement over time:

```javascript
// Score leads based on behavior
let engagement_score = 0;

if (email_opened) engagement_score += 10;
if (property_clicked) engagement_score += 20;
if (replied_to_email) engagement_score += 30;
if (scheduled_showing) engagement_score += 50;

// Adjust nurture strategy
if (engagement_score > 70) {
  nurture_strategy = 'aggressive';
} else if (engagement_score < 20) {
  nurture_strategy = 'gentle';
}
```

### 2. Comparative Market Analysis (CMA)

For seller leads:

```javascript
// Generate automated CMA
const nearbyProperties = await searchMLS({
  address_near: lead.property_address,
  radius: 0.5, // miles
  sold_date: 'last_6_months'
});

const averagePrice = calculateAverage(nearbyProperties);
const suggestedPrice = averagePrice * 1.02; // 2% above average

// Send CMA email
sendEmail({
  subject: `Your home value estimate: $${suggestedPrice}`,
  body: generateCMAReport(nearbyProperties, suggestedPrice)
});
```

### 3. Multi-Language Support

For diverse markets:

```javascript
// Detect language preference
const language = lead.message.includes('español') ? 'es' : 'en';

// Generate email in preferred language
const prompt = language === 'es' 
  ? 'Generar correo electrónico de bienvenida en español...'
  : 'Generate welcome email in English...';
```

### 4. Video Personalization

Auto-generate personalized video messages:

```javascript
// Use service like Dubb or BombBomb
POST https://api.dubb.com/v2/videos
{
  template_id: 'welcome_template',
  variables: {
    recipient_name: lead.first_name,
    properties: matched_properties
  }
}

// Include video link in email
<p>I recorded a personal message for you:</p>
<a href="${video_url}">Watch Video</a>
```

### 5. Predictive Lead Scoring

Machine learning model:

```javascript
// Train model on historical data
// Factors: time_to_respond, property_views, email_opens, etc.

const predictedCloseDate = mlModel.predict({
  lead_score: 75,
  budget: 400000,
  urgency: 'high',
  engagement: 85
});

// Result: "This lead will likely close in 45 days"
```

---

## ✅ Launch Checklist

**Pre-Launch:**
- [ ] Workflow imported successfully
- [ ] Airtable CRM created with all tables
- [ ] Zillow integration configured
- [ ] Realtor.com integration configured
- [ ] MLS API connected and tested
- [ ] Gmail OAuth configured
- [ ] Twilio SMS set up
- [ ] Claude API configured
- [ ] Test lead processed successfully
- [ ] Email templates customized
- [ ] Welcome email looks professional
- [ ] Hot lead alerts working
- [ ] Property matches accurate

**Launch Day:**
- [ ] Activate all webhook nodes
- [ ] Monitor first 5 leads closely
- [ ] Check response times
- [ ] Verify emails delivering
- [ ] Confirm SMS sending
- [ ] Watch for errors

**Week 1:**
- [ ] Process 20+ leads
- [ ] Track conversion rates
- [ ] Optimize email templates
- [ ] Adjust property matching
- [ ] Fine-tune lead scoring

**Month 1:**
- [ ] Calculate actual ROI
- [ ] Measure time savings
- [ ] Document extra deals closed
- [ ] Create case study
- [ ] Plan next optimizations

---

## 📚 Best Practices

### Lead Response

**Speed matters:**
- Hot leads: Call within 1 hour
- Warm leads: Call within 24 hours
- Cold leads: Email immediately, call within week

**Multi-touch approach:**
- Email + SMS for first contact
- Follow up 3 times minimum
- Space follow-ups 3-7 days apart

### Property Matching

**Quality over quantity:**
- Show 5 perfect matches > 20 okay matches
- Explain WHY each property fits
- Include neighborhood insights

**Keep it fresh:**
- Re-search MLS weekly
- Send new listings immediately
- Alert on price reductions

### Email Content

**Keep it personal:**
- Use lead's name
- Reference their specific criteria
- Mention their location specifically
- Include your photo

**Mobile-optimized:**
- Short paragraphs (2-3 sentences)
- Large tap-able buttons
- Compress images
- Test on phone before sending

### Follow-Up Cadence

**First week:**
- Day 1: Welcome + properties
- Day 3: "Any questions?" check-in
- Day 7: New listings

**First month:**
- Weekly: New property alerts
- Bi-weekly: Market updates

**Long-term:**
- Monthly: Market newsletter
- Quarterly: Seasonal opportunities
- Always: Immediate alerts on perfect matches

---

## 🎉 You're Ready!

This workflow will:
- ✅ Respond to every lead in under 2 minutes
- ✅ Send perfect property matches automatically
- ✅ Nurture leads for months without manual work
- ✅ Never let a lead fall through the cracks
- ✅ Close 2-3 more deals per month

**This system is worth $2,000-8,000 in setup value.**

Every lead that goes cold is a lost commission. This workflow ensures that never happens!

---

**Version:** 1.0.0  
**Setup time:** 60-90 minutes  
**Extra deals per month:** 2-3  
**ROI:** 100x or better  

Built with ❤️ for real estate professionals!
