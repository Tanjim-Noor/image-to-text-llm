
## ⚠️ Google Gemini API Free Tier Quota Limits

**Note:** The free tier quota has been exceeded. See limits below for each model.

### Free Tier Rate Limits (Per Model)

| Model | Requests/Min | Requests/Day | Tokens/Min | Daily Quota |
|-------|---------|----------|-----------|----------|
| **Gemini 2.5 Pro** | 5 RPM | 100 RPD | 250,000 TPM | Limited |
| **Gemini 2.5 Flash** | 10 RPM | 250 RPD | 250,000 TPM | **20 Requests/Day (Free Tier)** ⚠️ |
| **Gemini 2.5 Flash-Lite** | 15 RPM | 1,000 RPD | 250,000 TPM | Limited |

### Key Information

- **Free Tier Models Supported:** Flash model only (2.5-flash)
- **RPM (Requests Per Minute):** Rate limiting per minute
- **TPM (Tokens Per Minute):** Combined input + output tokens per minute
- **RPD (Requests Per Day):** Daily request quota (resets at midnight Pacific Time)
- **Commercial Usage:** ✅ Allowed with restrictions
- **Quota Reset Time:** Midnight Pacific Time (PT)

### Rate Limit Error Details

The error indicates:
- **Metric:** `generativelanguage.googleapis.com/generate_content_free_tier_requests`
- **Free Tier Quota:** 20 requests/day for gemini-2.5-flash
- **Error Code:** 429 RESOURCE_EXHAUSTED

### Solutions to Continue Processing

1. **Upgrade to Paid Tier** (Recommended for production)
   - Tier 1: Full paid billing account required
   - Tier 2: $250+ cumulative spend on Google Cloud
   - Tier 3: $1,000+ cumulative spend on Google Cloud

2. **Wait for Quota Reset**
   - Your quota resets at midnight Pacific Time (UTC-8)

3. **Alternative Models**
   - Switch to `Gemini 2.5 Flash-Lite` (15 RPM, 1,000 RPD)
   - Use `Gemini 2.5 Pro` (lower speed, higher quality, 100 RPD)

4. **Optimize Your Usage**
   - Reduce requests per day
   - Batch requests where possible
   - Implement caching for repeated queries
   - Use Flash-Lite for simple tasks

### For More Information

- Official Rate Limits Documentation: https://ai.google.dev/gemini-api/docs/rate-limits
- View Your Usage: https://aistudio.google.com/usage?timeRange=last-28-days&tab=rate-limit
- Request Rate Limit Increase: https://forms.gle/ETzX94k8jf7iSotH9