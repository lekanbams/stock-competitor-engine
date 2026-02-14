# Real-Time Stock Competitor Engine (Simplified)

An automated n8n workflow that monitors multiple stock tickers for significant price movements (≥5% change) and delivers AI-powered institutional-grade market analysis via email every 2 hours. This is a streamlined, single-path version optimized for clarity and ease of maintenance.

## 📊 Overview

This automation combines real-time stock data from TwelveData API, news aggregation from Yahoo Finance, and AI analysis from Google Gemini to provide professional-grade market intelligence reports directly to your inbox.

## ✨ Features

- **Automated Monitoring**: Checks stock prices every 2 hours (configurable)
- **Multi-Stock Tracking**: Monitors EOSE, GLXY, OPEN, TSLA, and FNMA simultaneously
- **Smart Filtering**: Only triggers analysis for stocks with ≥5% price movement
- **AI-Powered Analysis**: Uses Google Gemini 2.5 Flash for institutional-grade market insights
- **News Integration**: Pulls top 5 relevant news articles for context
- **Email Delivery**: Sends formatted HTML analysis reports
- **Duplicate Prevention**: Built-in logic to prevent duplicate email notifications
- **Clean Architecture**: Simplified single-path workflow for easy maintenance

## 🏗️ Workflow Architecture

```
Schedule Trigger (Every 2 hours at :15)
    ↓
Get Stock Update (TwelveData API)
    ↓
Convert Values (Parse & Split Stocks)
    ↓
Is % Greater Than 5%? ─── No → Do Nothing
    ↓ Yes
Fetch News (Yahoo Finance)
    ↓
Run Analysis (Google Gemini)
    ↓
Parse Output (Format for Email)
    ↓
Send Update (Gmail)
    ↓
If (Check Not Duplicate) → Stop or Retry
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

1. Download `Stock_Competitor_Engine_Simplified.json` from this repository
2. Open your n8n instance
3. Click **Workflows** → **Import from File**
4. Select the downloaded JSON file
5. Click **Import**

### Step 2: Configure API Credentials

#### A. TwelveData API Setup

1. In n8n, locate the **"Get stock update"** node
2. Click on the node
3. Replace `YOUR_TWELVEDATA_API_KEY` in the URL with your actual API key:
   ```
   https://api.twelvedata.com/quote?symbol=EOSE,GLXY,OPEN,TSLA,FNMA&interval=4h&apikey=YOUR_API_KEY_HERE
   ```

#### B. Google Gemini API Setup

1. Click on the **"Run analysis"** node
2. Click on **Credentials** → **Create New**
3. Select **Google PaLM/Gemini API**
4. Enter your Google API key
5. Save the credential
6. Select it from the dropdown

#### C. Gmail OAuth Setup

1. Click on the **"Send update"** node
2. Click on **Credentials** → **Create New**
3. Select **Gmail OAuth2**
4. Follow the OAuth flow to authorize your Gmail account
5. Save the credential

### Step 3: Customize Settings

#### Configure Stock Symbols

Edit the **"Get stock update"** node to monitor your preferred stocks:

```
https://api.twelvedata.com/quote?symbol=STOCK1,STOCK2,STOCK3&interval=4h&apikey=YOUR_API_KEY
```

**Current symbols**: EOSE, GLXY, OPEN, TSLA, FNMA

Replace with your watchlist (comma-separated, no spaces).

#### Set Email Recipients

1. Open the **"Send update"** node
2. Update the `sendTo` field with your email address:
   ```
   your-email@example.com
   ```

For multiple recipients:
```
email1@example.com, email2@example.com, email3@example.com
```

#### Adjust Threshold Percentage

To change the trigger threshold from 5%:

1. Open the **"Is % greater than 5 ?1"** node
2. Modify the `rightValue` parameter (currently set to `5`)
3. Example: Set to `3` for 3% threshold, or `10` for 10% threshold

#### Modify Schedule

To change from every 2 hours at :15 past the hour:

1. Open **"Schedule Trigger"** node
2. Adjust these settings:
   - `hoursInterval`: Change from `2` to your preference (1-24)
   - `triggerAtMinute`: Change from `15` to your preference (0-59)

**Examples:**
- Every hour at :00 → `hoursInterval: 1`, `triggerAtMinute: 0`
- Every 4 hours at :30 → `hoursInterval: 4`, `triggerAtMinute: 30`
- Every 6 hours at :45 → `hoursInterval: 6`, `triggerAtMinute: 45`

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

1. Open **"Run analysis"** node
2. Change `modelId` value to desired model:
   - `models/gemini-pro`
   - `models/gemini-2.5-flash`
   - `models/gemini-2.5-pro`

### Modify Analysis Prompt

The AI prompt is fully customizable in the **"Run analysis"** node. You can adjust:
- Tone and style
- Analysis depth
- Report format
- Focus areas
- Number of catalysts
- Section structure

### Add More Stocks

Simply add ticker symbols to the API URL (comma-separated):
```
symbol=AAPL,GOOGL,MSFT,AMZN,TSLA,NVDA
```

**Important Notes:**
- Free tier allows up to 50 symbols per request
- More symbols = more API credits used
- Be mindful of your 800 credits/day limit
- Each execution uses ~1 credit per symbol

### Change News Count

In the **"Fetch news"** node, modify the `newsCount` parameter:
```
newsCount: 10  // Get 10 news articles instead of 5
```

More news = better context but longer AI processing time.

### Email Subject Customization

In the **"Send update"** node, change the subject line:
```
subject: "🚨 Stock Alert: {{ $json.symbol }} up {{ $json.change }}%"
```

This creates dynamic subjects like: "🚨 Stock Alert: TSLA up 7.3%"

## 📊 Sample Output

See the `/screenshots` folder for examples of:
- n8n workflow canvas
- Email notification received
- AI analysis report sample
- Stock data formatting

*(Add your screenshots to the `/screenshots` folder after setup)*

## 📈 Understanding the Data

### Stock Data Fields

The workflow processes these fields from TwelveData:

- **symbol**: Ticker symbol (e.g., TSLA)
- **name**: Company name (e.g., Tesla Inc)
- **percent_change**: Percentage change from previous close
- **close**: Current/latest closing price
- **previous_close**: Previous day's closing price
- **open**: Opening price
- **high**: Day's high price
- **low**: Day's low price
- **volume**: Trading volume

### News Data

From Yahoo Finance:
- **title**: Article headline
- **link/url**: Article URL
- Top 5 most recent and relevant articles per stock

## ⚠️ Important Notes

### API Rate Limits

**TwelveData Free Tier**:
- 800 API credits/day
- 1 quote request = 1 credit per symbol
- 5 symbols × 12 executions/day = 60 credits/day (well within limits)
- Monitor usage: [TwelveData Dashboard](https://twelvedata.com/account/usage)

**Google Gemini Free Tier**:
- 60 requests/minute
- 1,500 requests/day
- This workflow: ~1 request per triggered stock
- Monitor usage: [Google AI Studio](https://makersuite.google.com/app/apikey)

**Gmail Sending Limits**:
- ~500 emails/day for personal accounts
- ~2,000 emails/day for Google Workspace accounts
- This workflow: Maximum 5 emails per execution (if all stocks trigger)

### Data Accuracy

- Stock prices may be delayed 15+ minutes on free tiers
- For real-time data, consider TwelveData paid plans
- News relevance depends on Yahoo Finance's algorithm
- AI analysis quality depends on news article quality

### Email Deliverability

- First-time senders may hit spam filters
- Add your sending address to contacts/safe senders
- Consider using a dedicated sending address
- Check spam/junk folders if emails aren't arriving

## 🐛 Troubleshooting

### Workflow Not Executing

**Symptom**: No emails received, no execution history

**Solutions**:
1. Verify workflow is **Active** (toggle in top-right)
2. Check schedule trigger settings are correct
3. Review n8n execution logs for errors
4. Ensure n8n instance is running (for self-hosted)
5. Check system time matches your expected schedule

### No Emails Received

**Symptom**: Workflow executes but no emails arrive

**Solutions**:
1. Verify Gmail OAuth credentials are valid (re-authenticate if expired)
2. Check email address spelling in **"Send update"** node
3. Look in spam/junk folders
4. Verify Gmail sending limits haven't been exceeded
5. Check Gmail account for security alerts
6. Test by temporarily lowering threshold to 0% to force execution

### API Errors

**TwelveData Errors**:
- `Invalid API key`: Check key is correct and active
- `Quota exceeded`: Monitor usage, reduce frequency or upgrade plan
- `Invalid symbol`: Check ticker symbols are correct

**Gemini Errors**:
- `Invalid API key`: Regenerate key in Google AI Studio
- `Quota exceeded`: Wait for quota reset or request increase
- `Model not found`: Verify model ID is correct

**Yahoo Finance Errors**:
- No authentication required
- If failing, may be temporary - try again later
- Consider adding error handling to skip news if unavailable

### "No Stock Movement" (Expected Behavior)

**Symptom**: Workflow executes but no emails sent

**This is normal!** Emails are only sent when stocks move ≥5%.

**To test the workflow**:
1. Temporarily lower threshold to 0.1% in **"Is % greater than 5 ?1"** node
2. Manually execute the workflow (test button)
3. Check that emails are sent
4. Reset threshold back to 5%

### Duplicate Emails

**Symptom**: Receiving the same stock alert multiple times

**Solutions**:
1. Check the **"If"** node is properly configured
2. Verify it's checking for `labelIds[0] === "SENT"`
3. Ensure proper connection from **"Send update"** to **"If"** node
4. Clear any stuck executions in n8n

### Analysis Quality Issues

**Symptom**: AI analysis is generic or unhelpful

**Solutions**:
1. Verify news articles are being fetched (check **"Fetch news"** output)
2. Increase `newsCount` to 10 for more context
3. Customize the AI prompt in **"Run analysis"** node
4. Try a different Gemini model (e.g., gemini-pro)
5. Ensure stock symbols are correct (wrong symbols = irrelevant news)

## 🔒 Security Best Practices

- **Never commit API keys** to public repositories
- Use n8n's built-in credential system (never hardcode keys)
- Rotate API keys every 3-6 months
- Monitor API usage for unusual activity
- Use OAuth2 for Gmail (more secure than app passwords)
- Restrict TwelveData API key to specific IP addresses if possible
- Enable 2FA on all accounts (Google, TwelveData, n8n)
- Review email recipient list regularly

## 💡 Pro Tips

1. **Start Small**: Begin with 2-3 stocks, then scale up
2. **Timing Matters**: Schedule during trading hours (9:30 AM - 4:00 PM EST)
3. **Avoid Pre-Market**: Don't run during pre-market (before 9:30 AM EST) - volatility can trigger false alerts
4. **Test Thoroughly**: Use manual execution to test before activating schedule
5. **Monitor API Usage**: Set up alerts in TwelveData and Google AI Studio
6. **Archive Reports**: Forward emails to a dedicated folder for historical tracking
7. **Customize Per Stock**: Consider separate workflows for different stock categories
8. **Add Filters**: Create additional IF nodes to filter by volume or other metrics
9. **Mobile Notifications**: Forward Gmail to your phone for instant alerts
10. **Backup Configuration**: Export your workflow JSON regularly

## 📈 Measuring Success

Track these metrics to evaluate performance:

**Accuracy Metrics**:
- True positive rate (legitimate alerts vs false alarms)
- News relevance score (manually rate 1-10)
- AI insight quality (actionable vs generic)

**Performance Metrics**:
- Average execution time
- API quota usage vs limits
- Email deliverability rate

**Business Metrics**:
- Time saved vs manual monitoring
- Trading decisions influenced by alerts
- ROI on paid API tiers (if upgraded)

## 🔄 Differences from Original Version

This simplified version differs from the multi-path original:

**Removed Complexity**:
- ✅ Single execution path (vs dual paths)
- ✅ Fewer nodes (11 vs 16)
- ✅ Clearer node naming
- ✅ No redundant data processing

**Kept Functionality**:
- ✅ Same stock monitoring capability
- ✅ Same AI analysis quality
- ✅ Same email delivery
- ✅ Same duplicate prevention

**Benefits**:
- Easier to understand and modify
- Faster execution time
- Simpler debugging
- Better for beginners

## 🎯 Advanced Customization Ideas

### Multi-Tier Alerts

Create different alert levels:
- 5-10% = Yellow Alert (watch list)
- 10-20% = Orange Alert (investigate)
- 20%+ = Red Alert (urgent action)

Modify the **"Is % greater than 5 ?1"** node to add multiple IF conditions.

### Sector Analysis

Group stocks by sector and analyze sector-wide trends:
- Tech: AAPL, GOOGL, MSFT
- Energy: XOM, CVX, OXY
- Finance: JPM, BAC, GS

### Portfolio Integration

Add a node to:
- Track your actual portfolio holdings
- Calculate position size recommendations
- Generate buy/sell signals

### Historical Tracking

Add Google Sheets node to:
- Log all price movements
- Track accuracy over time
- Build historical dataset for backtesting

### Slack/Discord Integration

Replace or supplement Gmail with:
- Slack webhook for team channels
- Discord webhook for community alerts
- SMS via Twilio for urgent alerts

## 🤝 Contributing

Ideas for improvements:
- Add technical indicators (RSI, MACD, moving averages)
- Integrate with brokerage APIs for auto-trading
- Create sentiment analysis from news headlines
- Add support for options, futures, or crypto
- Build a web dashboard for visualization

## 📝 License

This workflow is provided as-is for educational and personal use. Modify as needed for your use case.

## 📞 Support

For issues with:
- **n8n**: [n8n Community Forum](https://community.n8n.io/)
- **TwelveData API**: [TwelveData Support](https://twelvedata.com/support)
- **Google Gemini**: [Google AI Documentation](https://ai.google.dev/docs)

## 🎯 Future Enhancements

Potential improvements:

- [ ] Add pre-market and after-hours monitoring
- [ ] Integrate technical analysis indicators
- [ ] Add stop-loss/take-profit alerts
- [ ] Create multi-timeframe analysis (1h, 4h, daily)
- [ ] Build earnings calendar integration
- [ ] Add options flow data
- [ ] Create portfolio tracking dashboard
- [ ] Integrate with Trading View for charts
- [ ] Add backtesting capabilities
- [ ] Build machine learning price prediction

---

**Made with ❤️ using n8n + AI**

*Last Updated: February 2026*

## 📚 Related Resources

- [TwelveData API Documentation](https://twelvedata.com/docs)
- [Google Gemini API Guide](https://ai.google.dev/docs)
- [n8n Workflow Best Practices](https://docs.n8n.io/workflows/)
- [Stock Market Data Sources Comparison](https://github.com/topics/stock-market-data)
- [Algorithmic Trading Resources](https://github.com/topics/algorithmic-trading)
