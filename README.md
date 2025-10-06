# 📧 Daily Digest - Automated Morning Briefing

A comprehensive n8n workflow that generates and delivers a personalized daily digest every weekday morning. Aggregates news, weather, emails, and calendar events into a single AI-powered email briefing.

![Status](https://img.shields.io/badge/status-active-success.svg)
![n8n](https://img.shields.io/badge/n8n-workflow-EA4B71?logo=n8n)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4o--mini-412991?logo=openai)
![Node](https://img.shields.io/badge/node-54-blue)

## ✨ Features

- **Automated Schedule** - Runs Monday through Friday at 9:00 AM
- **Multi-Source Aggregation** - Combines news, weather, emails, and calendar
- **AI-Powered Summaries** - GPT-4o-mini generates natural, personalized content
- **Smart Email Filtering** - Removes newsletters and automated messages
- **Weather Intelligence** - Includes clothing recommendations and umbrella alerts
- **Categorized News** - Automatically organizes stories by topic
- **Plain Text Output** - Mobile-friendly format without markdown
- **Parallel Processing** - Efficient data collection from all sources
- **Customizable Tone** - Light-hearted, personal second-person voice

## 🔄 Workflow Overview

### Data Flow

```
Schedule Trigger (9 AM Mon-Fri)
         ↓
    ┌────┴────┬────────┬─────────┐
    ↓         ↓        ↓         ↓
  News    Weather   Emails   Calendar
    ↓         ↓        ↓         ↓
 AI Process Each Source
    ↓         ↓        ↓         ↓
    └────┬────┴────────┴─────────┘
         ↓
   Aggregate Data
         ↓
   AI Agent Combines
         ↓
   Send via Gmail
```

## 📊 Data Sources

### News
- **CNN Money** - RSS Feed (http://rss.cnn.com/rss/money_topstories.rss)
- **Business Insider** - RSS Feed (https://feeds.businessinsider.com/custom/all)
- AI categorizes stories by topic and creates summaries

### Weather
- **Source**: OpenWeatherMap API
- **Location**: Los Angeles, US (configurable)
- **Format**: Imperial units
- **Forecast**: Next 24 hours
- **Extras**: Clothing suggestions, umbrella recommendations

### Emails
- **Source**: Gmail API
- **Filters**: Personal category, unread, last 24 hours
- **Processing**: AI filters human emails from newsletters
- **Output**: One-sentence summaries with sender info

### Calendar
- **Source**: Google Calendar API
- **Range**: Next 24 hours
- **Details**: Event summary, start time, timezone

## 🚀 Quick Start

### Prerequisites

- n8n instance (self-hosted or cloud)
- OpenAI API key
- OpenWeatherMap API key
- Gmail OAuth credentials
- Google Calendar OAuth credentials

### Option 1: Import Workflow

```bash
# In n8n UI
1. Click "Import from File"
2. Upload "Daily digest.json"
3. Configure credentials for each service
4. Activate workflow
```

### Option 2: Manual Setup

1. Create new workflow in n8n
2. Copy nodes from JSON file
3. Configure credentials:
   - OpenAI API
   - OpenWeatherMap API
   - Gmail OAuth
   - Google Calendar OAuth
4. Update email recipient address
5. Test each node individually
6. Activate workflow

## ⚙️ Configuration

### Schedule Timing

```javascript
// Schedule Trigger node
Cron Expression: "0 9 * * 1-5"
// Format: minute hour day month dayOfWeek
// Change to adjust timing
```

### Weather Location

```javascript
// OpenWeatherMap node
cityName: "los angeles,us"  // Change to your city
format: "imperial"          // Or "metric"
```

### Email Address

```javascript
// Gmail Tool node
sendTo: "your-email@gmail.com"  // Update recipient
```

### Calendar Selection

```javascript
// Google Calendar node
calendar: "your-calendar-id"  // Select your calendar
```

### AI Model

```javascript
// OpenAI Chat Model nodes
model: "gpt-4o-mini"  // Or upgrade to gpt-4
```

## 🎨 Customization

### Modify Digest Tone

Edit the AI Agent prompt to change:
- Voice (formal, casual, humorous)
- Length (brief, detailed)
- Structure (sections, format)
- Language style

```javascript
// AI Agent node prompt
"Create a daily digest from the data below.
Use a professional, informative tone (3rd person).
..." // Modify as needed
```

### Add News Sources

```javascript
// Add new RSS Feed Read node
1. Create RSS Feed Read node
2. Enter feed URL
3. Connect to Merge node
4. Increase Merge node inputs
```

### Change Weather Details

```javascript
// Basic LLM Chain1 node
"Summarize the weather forecast...
Include temperature range,
precipitation chances,
UV index recommendations,
..." // Add desired details
```

### Adjust Email Filters

```javascript
// Gmail node
filters: {
  labelIds: ["CATEGORY_PERSONAL", "INBOX"],  // Add labels
  readStatus: "unread",
  receivedAfter: "={{ $now.minus(1, 'day') }}"  // Change timeframe
}
```

### Customize Calendar Range

```javascript
// Google Calendar node
timeMax: "={{ $now.plus({ days: 2 }) }}"  // Extend to 2 days
```

## 📁 Workflow Structure

```
Daily Digest/
├── Trigger
│   └── Schedule Trigger (Cron: 0 9 * * 1-5)
│
├── Data Collection (Parallel)
│   ├── News Branch
│   │   ├── CNN RSS Feed
│   │   ├── Business Insider RSS Feed
│   │   ├── Merge Feeds
│   │   ├── Edit Fields (Extract title/snippet)
│   │   ├── Aggregate Items
│   │   ├── Basic LLM Chain (Summarize)
│   │   └── Edit Fields (Format output)
│   │
│   ├── Weather Branch
│   │   ├── OpenWeatherMap API
│   │   ├── Split Out (Process list)
│   │   ├── Filter (Next 24h)
│   │   ├── Aggregate Items
│   │   ├── Basic LLM Chain (Summarize + clothing)
│   │   └── Edit Fields (Format output)
│   │
│   ├── Email Branch
│   │   ├── Gmail API (Get unread)
│   │   ├── Edit Fields (Extract text/from)
│   │   ├── Aggregate Items
│   │   ├── Basic LLM Chain (Filter + summarize)
│   │   └── Edit Fields (Format output)
│   │
│   └── Calendar Branch
│       ├── Google Calendar API
│       ├── Edit Fields (Extract details)
│       ├── Aggregate Items
│       └── Edit Fields (Format output)
│
├── Merge Data
│   ├── Merge (4 inputs)
│   ├── Aggregate (Combine fields)
│   └── Edit Fields (Structure data)
│
└── Generate & Send
    ├── AI Agent (Compose digest)
    ├── OpenAI Chat Model
    └── Gmail Tool (Send email)
```

## 🎯 Output Format

```
Hi there,

Today's weather
- Partly cloudy with highs around 75°F
- Light jacket recommended for morning
- No umbrella needed

Unread emails
- John Smith (john@example.com): Project update and meeting request
- Sarah Jones (sarah@example.com): Q4 budget review feedback

Today's events
- 10:00 AM - Team standup (Pacific Time)
- 2:00 PM - Client presentation (Pacific Time)

Latest news

Technology
- Tech company announces new AI breakthrough
- Smartphone sales reach record highs

Economy
- Markets rise on positive earnings reports
- Federal Reserve maintains interest rates

Have a great day, [Name]!
```

## 🛠️ Node Breakdown

| Node Type | Count | Purpose |
|-----------|-------|---------|
| Schedule Trigger | 1 | Initiates workflow daily |
| RSS Feed Read | 2 | Fetches news feeds |
| OpenWeatherMap | 1 | Gets weather data |
| Gmail | 1 | Retrieves emails |
| Google Calendar | 1 | Fetches events |
| Basic LLM Chain | 3 | Summarizes data sources |
| AI Agent | 1 | Composes final digest |
| OpenAI Chat Model | 4 | Powers AI processing |
| Aggregate | 4 | Combines items |
| Set (Edit Fields) | 7 | Formats data |
| Merge | 2 | Joins branches |
| Split Out | 1 | Processes arrays |
| Filter | 1 | Filters time range |
| Gmail Tool | 1 | Sends digest email |
| Structured Output Parser | 1 | Parses email JSON |

## 🔧 Troubleshooting

### No Digest Received

```javascript
// Check each branch:
1. Verify Schedule Trigger is active
2. Test RSS feeds manually (check URLs)
3. Confirm API credentials are valid
4. Check Gmail OAuth permissions
5. Verify recipient email address
```

### Missing News Stories

```javascript
// RSS Feed Read nodes
1. Test feed URLs in browser
2. Check for feed format changes
3. Verify network connectivity
4. Increase timeout if needed
```

### Empty Email Section

```javascript
// Gmail node
1. Verify filter criteria:
   - Category: CATEGORY_PERSONAL
   - Status: unread
   - Time: last 24 hours
2. Check OAuth scopes include Gmail read
3. Manually check inbox for matching emails
```

### No Calendar Events

```javascript
// Google Calendar node
1. Verify calendar ID is correct
2. Check time range (now to +1 day)
3. Confirm OAuth includes calendar.readonly
4. Test with known upcoming event
```

### AI Generation Errors

```javascript
// OpenAI nodes
1. Check API key validity
2. Verify model availability (gpt-4o-mini)
3. Review prompt formatting
4. Check token limits
5. Monitor API usage/quotas
```

### Weather Data Issues

```javascript
// OpenWeatherMap node
1. Verify API key
2. Check city name format: "city,country"
3. Confirm operation: 5DayForecast
4. Validate API endpoint availability
```

## 💡 Enhancement Ideas

### Immediate Improvements
- [ ] Add high-priority email highlighting
- [ ] Include travel time to first meeting
- [ ] Add stock ticker for watchlist
- [ ] Include local event recommendations
- [ ] Add motivational quote

### Advanced Features
- [ ] Multiple recipient support
- [ ] User preference configuration (what to include)
- [ ] Weekend digest (different content)
- [ ] SMS/Slack delivery options
- [ ] Error notifications
- [ ] Digest history archive
- [ ] A/B testing different formats
- [ ] Smart prioritization (urgent items first)
- [ ] RSS feed recommendations based on interests
- [ ] Integration with task management tools

### AI Enhancements
- [ ] Sentiment analysis on emails
- [ ] Meeting preparation summaries
- [ ] Conflicting event detection
- [ ] Smart scheduling suggestions
- [ ] Personalized news filtering by interest
- [ ] Email urgency classification
- [ ] Weather activity recommendations

## 📊 Performance Optimization

### Execution Time
- **Average**: 30-45 seconds
- **Parallel Processing**: 4 branches simultaneously
- **Bottleneck**: AI processing (3-5s per LLM call)

### Tips for Faster Execution
```javascript
1. Use gpt-3.5-turbo instead of gpt-4o-mini
2. Reduce news items processed (filter by date)
3. Cache weather data (update less frequently)
4. Batch email processing
5. Limit calendar event count
```

### Error Handling
```javascript
// Add error handling nodes:
1. Add "Continue On Fail" setting to API nodes
2. Create fallback branches for missing data
3. Add error notification email
4. Log failures for debugging
```

## 🔒 Security Best Practices

- **API Keys**: Store in n8n credentials, never in workflow
- **OAuth Tokens**: Use n8n's built-in OAuth handling
- **Email Access**: Request minimum required scopes
- **Data Privacy**: Ensure n8n instance is secure
- **Credentials Rotation**: Update API keys periodically

## 🌐 Deployment Options

### Self-Hosted n8n
```bash
# Docker
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n

# Docker Compose
# See n8n documentation
```

### n8n Cloud
1. Sign up at n8n.cloud
2. Import workflow JSON
3. Configure credentials
4. Activate workflow

### Monitoring
- Enable execution logging
- Set up error notifications
- Monitor API usage/costs
- Track digest delivery rate

## 📝 Credentials Setup

### OpenAI API
```
1. Visit platform.openai.com
2. Create API key
3. Add to n8n credentials
4. Select in OpenAI Chat Model nodes
```

### OpenWeatherMap API
```
1. Visit openweathermap.org
2. Sign up and get free API key
3. Add to n8n credentials
4. Configure in OpenWeatherMap node
```

### Gmail OAuth
```
1. Create Google Cloud project
2. Enable Gmail API
3. Create OAuth credentials
4. Add to n8n
5. Authorize scopes
```

### Google Calendar OAuth
```
1. Use same Google Cloud project
2. Enable Calendar API
3. Use existing OAuth credentials
4. Authorize calendar.readonly scope
```

## 📖 Additional Resources

- [n8n Documentation](https://docs.n8n.io)
- [OpenAI API Reference](https://platform.openai.com/docs)
- [Gmail API Guide](https://developers.google.com/gmail/api)
- [Google Calendar API](https://developers.google.com/calendar)
- [OpenWeatherMap API](https://openweathermap.org/api)

## 📄 License

This workflow is open source and available under the [MIT License](LICENSE).

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!

1. Fork the repository
2. Create feature branch (`git checkout -b feature/EnhancedDigest`)
3. Commit changes (`git commit -m 'Add traffic data'`)
4. Push to branch (`git push origin feature/EnhancedDigest`)
5. Open Pull Request

## 👨‍💻 Author

**Redoanuzzaman**
- GitHub: [@REDOANUZZAMAN](https://github.com/REDOANUZZAMAN)
- Email: redoanuzzaman707@gmail.com
- Website: [redoan.dev](https://redoan.dev)

## 🙏 Acknowledgments

- n8n community for workflow automation platform
- OpenAI for GPT models
- All API providers for data access
- Morning briefing email inspiration

## 💖 Show Your Support

Give a ⭐️ if this workflow helps your mornings!

---

Made with ☕ and n8n automation

**Last Updated:** October 2025
