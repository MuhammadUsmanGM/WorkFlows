# Email Summarizer Workflow Setup Instructions

## What This Workflow Does
- Runs every day at 8 AM
- Fetches unread emails from the last 24 hours
- Uses Google Gemini AI to identify important emails (client messages, announcements, etc.)
- Ignores unimportant emails (social media, promotions, connection requests)
- Sends a summary to your WhatsApp

## Setup Steps

### 1. Import the Workflow
1. Open your n8n dashboard
2. Click on "Workflows" in the sidebar
3. Click the "+" button to create a new workflow
4. Click on the three dots menu (⋮) in the top right
5. Select "Import from File"
6. Upload the `email_summarizer_workflow.json` file

### 2. Get Google Gemini API Key
1. Go to https://aistudio.google.com/
2. Sign in with your Google account
3. Click "Get API Key" 
4. Create a new API key
5. Copy the API key

### 3. Configure Gmail Connection
1. In the workflow, click on the "Gmail - Get Unread Emails" node
2. Click "Create New Credential"
3. Follow the OAuth authentication process
4. Allow n8n to access your Gmail

### 4. Add Your Gemini API Key
1. Click on the "Google Gemini AI" node
2. Find the query parameter "key"
3. Replace `YOUR_GOOGLE_GEMINI_API_KEY` with your actual API key

### 5. Setup WhatsApp (Using CallMeBot)
CallMeBot is a FREE service to send WhatsApp messages:

1. Add CallMeBot number to your contacts: **+34 644 24 97 24**
2. Send this message to that number via WhatsApp: **I allow callmebot to send me messages**
3. You'll receive your API key in response
4. In the "Send to WhatsApp" node:
   - Replace `YOUR_PHONE_NUMBER_WITH_COUNTRY_CODE` with your number (e.g., +923001234567)
   - Replace `YOUR_CALLMEBOT_API_KEY` with the key you received

### 6. Test the Workflow
1. Click "Execute Workflow" button
2. Check if it fetches your emails
3. Verify the AI summary
4. Check your WhatsApp for the message

### 7. Activate the Workflow
1. Toggle the "Active" switch in the top right to ON
2. The workflow will now run automatically every day at 8 AM

## Customization Options

### Change the Schedule Time
In the "Schedule Trigger" node, modify the cron expression:
- `0 8 * * *` = 8 AM daily
- `0 20 * * *` = 8 PM daily
- `0 9 * * 1-5` = 9 AM on weekdays only

### Adjust Email Filter
In the "Gmail - Get Unread Emails" node, you can change:
- `newer_than:1d` to `newer_than:2d` (last 2 days)
- Add labels: `label:important` to filter by label

### Modify AI Instructions
In the "Google Gemini AI" node, you can customize what emails are considered important by editing the prompt.

## Troubleshooting

**No emails fetched?**
- Make sure you have unread emails in the last 24 hours
- Check Gmail authentication is working

**AI not working?**
- Verify your Gemini API key is correct
- Check you haven't exceeded the free tier limits (15 req/min, 1500 req/day)

**WhatsApp not working?**
- Make sure you completed the CallMeBot activation
- Check your phone number format includes country code (e.g., +92 for Pakistan)
- Verify your API key is correct

**Alternative to CallMeBot:**
If CallMeBot doesn't work, you can use:
- Twilio WhatsApp API (requires account)
- Telegram (easier setup, use Telegram node in n8n)
- Email notification to yourself

## Free Tier Limits
- **Google Gemini**: 15 requests/min, 1,500 requests/day (FREE)
- **CallMeBot**: Unlimited messages (FREE)
- **n8n**: Depends on your hosting (self-hosted = unlimited)

## Need Help?
- Check n8n documentation: https://docs.n8n.io/
- Google Gemini API docs: https://ai.google.dev/docs
- CallMeBot: https://www.callmebot.com/blog/free-api-whatsapp-messages/
