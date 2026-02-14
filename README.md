# Real-Time Stock Competitor Engine

An automated n8n workflow that monitors multiple stock tickers for significant price movements (≥5% change) and delivers AI-powered institutional-grade market analysis via email every 2 hours.

## 📊 Overview

This automation combines real-time stock data from TwelveData API, news aggregation from Yahoo Finance, and AI analysis from Google Gemini to provide professional-grade market intelligence reports directly to your inbox.

## ✨ Features

- **Automated Monitoring**: Checks stock prices every 2 hours
- **Multi-Stock Tracking**: Monitors EOSE, GLXY, OPEN, TSLA, and FNMA simultaneously
- **Smart Filtering**: Only triggers analysis for stocks with ≥5% price movement
- **AI-Powered Analysis**: Uses Google Gemini 2.5 Flash for institutional-grade market insights
- **News Integration**: Pulls top 5 relevant news articles for context
- **Email Delivery**: Sends formatted HTML analysis reports to your team
- **Duplicate Prevention**: Built-in logic to prevent duplicate email notifications

## 🏗️ Workflow Architecture

```
Schedule Trigger (Every 2 hours)
    ↓
Get Stock Updates (TwelveData API)
    ↓
Convert to Numbers & Split Stocks
    ↓
Filter (% Change ≥ 5%?) ─── No → Do Nothing
    ↓ Yes
Get Latest News (Yahoo Finance)
    ↓
AI Analysis (Google Gemini)
    ↓
Format for Email
    ↓
Send Gmail Notification
```

## 📋 Prerequisites

### Required Accounts & API Keys

1. **n8n Instance** (Cloud or Self-hosted)
   - [Get started with n8n](https://n8n.io/)

2. **TwelveData API** (Free tier available)
   - Sign up at [TwelveData](https://twelvedata.com/)
   - Free tier: 800 API credits/day
   - Get your API key from the dashboard

3. **Google Gemini API** (Free tier available)
   - Create account at [Google AI Studio](https://makersuite.google.com/app/apikey)
   - Generate API key
   - Free tier: 60 requests/minute

4. **Gmail Account**
   - Any Gmail account for sending notifications
   - OAuth2 authentication required in n8n

### Technical Requirements

- n8n version 1.0.0 or higher
- Internet connection for API calls
- Sufficient API quota on all services

## 🚀 Installation

### Step 1: Import the Workflow

1. Download `Real_Time_Stock_Competitor_Engine.json` from this repository
2. Open your n8n instance
3. Click **Workflows** → **Import from File**
4. Select the downloaded JSON file
5. Click **Import**

### Step 2: Configure API Credentials

#### A. TwelveData API Setup

1. In n8n, locate the **"Get Stock Update"** and **"Get Stock Update 01"** nodes
2. Click on each node
3. Replace `YOUR_TWELVEDATA_API_KEY` with your actual API key:
   ```
   https://api.twelvedata.com/quote?symbol=EOSE&apikey=YOUR_API_KEY_HERE
   ```

#### B. Google Gemini API Setup

1. Click on the **"Message a model"** and **"Message a model 01"** nodes
2. Click on **Credentials** → **Create New**
3. Select **Google PaLM/Gemini API**
4. Enter your Google API key
5. Save the credential
6. Select it from the dropdown

#### C. Gmail OAuth Setup

1. Click on **"Send a message"** and **"Send a message 01"** nodes
2. Click on **Credentials** → **Create New**
3. Select **Gmail OAuth2**
4. Follow the OAuth flow to authorize your Gmail account
5. Save the credential

### Step 3: Customize Settings

#### Configure Stock Symbols

Edit the **"Get Stock Update 01"** node to monitor your preferred stocks:

```
https://api.twelvedata.com/quote?symbol=STOCK1,STOCK2,STOCK3&interval=4h&apikey=YOUR_API_KEY
```

**Current symbols**: EOSE, GLXY, OPEN, TSLA, FNMA

#### Set Email Recipients

1. Open **"Send a message"** and **"Send a message 01"** nodes
2. Update the `sendTo` field with your email addresses:
   ```
   your-email@example.com, team-member@example.com
   ```

#### Adjust Threshold Percentage

To change the trigger threshold from 5%:

1. Open **"% greater than 5 ?"** and **"% greater than 5 ? 01"** nodes
2. Modify the `rightValue` parameter (currently set to `5`)

#### Modify Schedule

To change from every 2 hours:

1. Open **"Schedule Trigger"** node
2. Adjust `hoursInterval` (currently `2`)
3. Adjust `triggerAtMinute` for timing (currently `15` = :15 past the hour)

### Step 4: Activate Workflow

1. Click the **Active** toggle in the top-right corner
2. The workflow will now run automatically on schedule

## 📧 Email Report Format

Each triggered analysis includes:

### 🎯 Executive Summary
- Company name & ticker symbol
- Percentage change
- Brief overview of price movement drivers

### 📊 Primary Catalyst Analysis
- Up to 3 key catalysts identified
- Category classification (Company/Sector/Market/Macro)
- Impact level assessment (High/Medium/Low)
- Source attribution with news links

### 🔍 Market Context
- Broader sector/market trend analysis

### 💡 Intelligence Takeaway
- Actionable conclusions
- Sustainability assessment
- Key monitoring points

### 📰 Reference Links
- Top 5 news article URLs

## 🛠️ Customization Options

### Change AI Model

The workflow uses Gemini 2.5 Flash by default. To use a different model:

1. Open **"Message a model"** nodes
2. Change `modelId` value to desired model:
   - `models/gemini-pro`
   - `models/gemini-2.5-flash`
   - etc.

### Modify Analysis Prompt

The AI prompt is fully customizable in the **"Message a model"** nodes. You can adjust:
- Tone and style
- Analysis depth
- Report format
- Focus areas

### Add More Stocks

Simply add ticker symbols to the API URL (comma-separated):
```
symbol=AAPL,GOOGL,MSFT,AMZN,TSLA
```

**Note**: Be mindful of TwelveData API rate limits (800 credits/day on free tier)

## 📊 Sample Output

See the `/screenshots` folder for examples of:
- n8n workflow canvas
- Email notification received
- AI analysis report sample
- Stock data formatting

*(Add your screenshots to the `/screenshots` folder after setup)*

## ⚠️ Important Notes

### API Rate Limits

- **TwelveData Free Tier**: 800 API credits/day
- **Gemini Free Tier**: 60 requests/minute
- Running every 2 hours = 12 executions/day
- Monitor your usage in respective dashboards

### Data Accuracy

- Stock prices are delayed (typically 15 minutes on free tiers)
- For real-time data, consider paid TwelveData plans
- News sources may vary in relevance

### Email Deliverability

- Gmail has sending limits (~500 emails/day)
- Consider using a dedicated email service for high volume
- Check spam folders if emails aren't arriving

## 🐛 Troubleshooting

### Workflow Not Executing

1. Check that workflow is **Active** (toggle in top-right)
2. Verify schedule trigger settings
3. Check n8n execution logs for errors

### No Emails Received

1. Verify Gmail OAuth credentials are valid
2. Check email addresses are correct
3. Look in spam/junk folders
4. Check Gmail sending limits haven't been exceeded

### API Errors

1. **TwelveData**: Verify API key and rate limits
2. **Gemini**: Check API key and quota
3. **Yahoo Finance**: No auth required, may have rate limits

### "No Stock Movement" (No Emails)

This is expected behavior! Emails are only sent when stocks move ≥5%. To test:

1. Lower the threshold temporarily to 0.1%
2. Manually execute the workflow
3. Check that emails are sent
4. Reset threshold to 5%

## 🔒 Security Best Practices

- **Never commit API keys** to public repositories
- Use environment variables for sensitive data when possible
- Regularly rotate API keys
- Monitor API usage for unusual activity
- Use OAuth2 for Gmail (more secure than app passwords)

## 📝 License

This workflow is provided as-is for educational and personal use. Modify as needed for your use case.

## 🤝 Contributing

Feel free to fork this workflow and customize it! If you make improvements, consider sharing them back with the community.

## 📞 Support

For issues with:
- **n8n**: [n8n Community Forum](https://community.n8n.io/)
- **TwelveData API**: [TwelveData Support](https://twelvedata.com/support)
- **Google Gemini**: [Google AI Documentation](https://ai.google.dev/docs)

## 🎯 Future Enhancements

Potential improvements to consider:

- [ ] Add SMS notifications for critical alerts
- [ ] Create Slack integration
- [ ] Build a dashboard for historical tracking
- [ ] Add sentiment analysis scoring
- [ ] Implement portfolio-level insights
- [ ] Create custom stock watchlists
- [ ] Add price alert conditions (not just percentage)
- [ ] Integrate technical indicators (RSI, MACD, etc.)

---

**Made with ❤️ using n8n**

*Last Updated: February 2026*
