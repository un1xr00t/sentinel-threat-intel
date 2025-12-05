# 🛡️ SENTINEL - Enterprise Threat Intelligence Platform

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![n8n](https://img.shields.io/badge/Built%20with-n8n-FF6D5A)](https://n8n.io)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)

> A zero-cost, enterprise-grade threat intelligence aggregation platform that replaces commercial solutions costing $50K-$500K annually.

![SENTINEL Dashboard](assets/sentinel-workflow.png)

---

## 🎯 What is SENTINEL?

SENTINEL is an automated threat intelligence platform built on [n8n](https://n8n.io) that aggregates, enriches, scores, and alerts on cybersecurity threats in real-time. It monitors 28+ authoritative sources and delivers actionable intelligence directly to your Discord server.

**This is not a toy project.** This is production-ready architecture with:
- Fingerprint-based deduplication to prevent alert fatigue
- Smart scoring algorithms that prioritize real threats over noise
- CVE enrichment with CVSS scores and EPSS exploit probability
- AI-powered analysis for executive summaries
- Multi-tier alerting based on threat severity

---

## 💰 Why SENTINEL?

| Feature | Commercial Solutions | SENTINEL |
|---------|---------------------|----------|
| Annual Cost | $50,000 - $500,000 | **$0 - $20/month** |
| Threat Sources | 10-50 | **28+** |
| CVE Enrichment | ✅ | ✅ |
| EPSS Scoring | Sometimes | ✅ |
| AI Analysis | Premium tier | ✅ |
| Customizable | Limited | **Fully Open Source** |
| Self-Hosted | Rarely | ✅ |

---

## ✨ Features

### 📡 Intelligence Sources (28+)

**Government & Official Advisories**
- CISA Advisories & News
- US-CERT Alerts
- CISA KEV (Known Exploited Vulnerabilities)

**CVE & Exploit Feeds**
- NVD (National Vulnerability Database)
- Exploit-DB
- EPSS (Exploit Prediction Scoring System)

**Vendor Security Bulletins**
- Microsoft MSRC
- Google Project Zero
- Cisco PSIRT
- VMware Security Advisories

**Threat Intelligence APIs**
- ThreatFox IOCs
- Ransomware.live

**Security News & Research**
- The Hacker News
- BleepingComputer
- Krebs on Security
- Dark Reading
- Security Week
- SANS ISC
- Schneier on Security

**Vendor Research Blogs**
- CrowdStrike
- Mandiant
- Kaspersky (Securelist)
- Unit 42 (Palo Alto)
- SentinelOne Labs
- ESET (WeLiveSecurity)
- Cisco Talos
- Fortinet
- Proofpoint

**Community**
- Reddit r/netsec
- Reddit r/cybersecurity

---

### 🧠 Smart Scoring Engine

SENTINEL doesn't just dump alerts on you. It uses a multi-factor scoring algorithm:

| Factor | Points | Example |
|--------|--------|---------|
| **Critical Keywords** | +15 to +20 | "zero-day", "actively exploited", "RCE" |
| **CISA KEV Status** | +50 | Known exploited vulnerability |
| **Source Credibility** | +5 to +15 | CISA (+15) vs Reddit (+5) |
| **CVE Presence** | +5 per CVE | Up to +20 |
| **CVSS Score** | +0 to +25 | Based on severity (0-10 scale) |
| **EPSS Score** | +10 to +20 | High exploit probability |
| **Recency** | +4 to +10 | Published within last 2-12 hours |
| **Threat Actors** | +10 | Known APT groups mentioned |
| **IOC Presence** | +2 per IOC | IPs, domains, hashes |

**Priority Levels:**
- 🔴 **CRITICAL** (80+ points): Immediate action required
- 🟠 **HIGH** (50-79 points): Review within hours
- 🟡 **MEDIUM** (25-49 points): Review within 24 hours
- 🔵 **LOW** (<25 points): Filtered out (reduces noise)

---

### 🔬 CVE Enrichment

When a CVE is detected, SENTINEL automatically enriches it with:

**From NVD (National Vulnerability Database):**
- CVSS Score (0-10 severity rating)
- CVSS Vector (attack complexity details)
- CWE Weakness Classification
- Official vulnerability description

**From FIRST.org EPSS:**
- Exploit Probability Score (0-100%)
- Likelihood of exploitation in next 30 days
- Percentile ranking against all CVEs

---

### 🤖 AI-Powered Analysis

Optional integration with OpenAI GPT-4o-mini provides:
- Executive summaries (2-3 sentences, non-technical)
- Technical analysis for security teams
- Affected systems identification
- Recommended actions
- Risk assessment with justification

**Cost:** ~$0.0002 per alert (fraction of a cent)

---

### 📨 Multi-Channel Discord Alerting

**Critical Alerts Channel**
- 🔴 Score 80+ or CISA KEV items
- @everyone notifications
- Full enrichment data (CVSS, EPSS, IOCs)
- AI-generated executive summary

**General Threat Intel Channel**
- 🟠🟡 Score 25-79 items
- No ping (reduces fatigue)
- Condensed alert format

**Daily Digest**
- Summary of last 24 hours
- Count by priority level
- Top sources
- CVEs tracked

---

### 🔄 Deduplication System

SENTINEL uses fingerprint-based deduplication to ensure you never see the same alert twice:

1. Each article gets a unique fingerprint (hash of title + date)
2. Fingerprints are stored in Baserow database
3. New items are checked against existing fingerprints
4. Duplicates are filtered before alerting
5. Old fingerprints (7+ days) are automatically cleaned up

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        SCHEDULE TRIGGERS                         │
├─────────────────┬─────────────────┬─────────────────────────────┤
│  Every 15 min   │    Every Hour   │        Every 4 Hours        │
│  (Critical)     │  (High Priority)│         (General)           │
└────────┬────────┴────────┬────────┴──────────────┬──────────────┘
         │                 │                       │
         ▼                 ▼                       ▼
┌─────────────────────────────────────────────────────────────────┐
│                      RSS FEEDS & APIs                            │
│  CISA, US-CERT, CVE, Exploit-DB, MSRC, Project Zero, News...   │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                  NORMALIZE & EXTRACT ENTITIES                    │
│  • Standardize format    • Extract CVEs (CVE-XXXX-XXXXX)        │
│  • Detect source         • Extract IOCs (IPs, domains, hashes) │
│  • Generate fingerprint  • Identify threat actors               │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                     SMART SCORING ENGINE                         │
│  Keywords + Source + CVEs + IOCs + Recency = Priority Score     │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                   DEDUPLICATION (Baserow)                        │
│  Check fingerprint against database → Filter duplicates          │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      CVE ENRICHMENT                              │
│  NVD API (CVSS) + FIRST.org EPSS (Exploit Probability)         │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    AI ANALYSIS (Optional)                        │
│  GPT-4o-mini → Executive Summary + Technical Analysis           │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      DISCORD ALERTS                              │
│  Critical Channel (@everyone) ←→ General Channel (no ping)      │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                   SAVE FINGERPRINT                               │
│  Store in Baserow for future deduplication                      │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🚀 Quick Start

### Prerequisites

- [n8n](https://n8n.io) instance (self-hosted or cloud)
- [Baserow](https://baserow.io) account (free tier works)
- Discord server with webhook permissions
- (Optional) OpenAI API key for AI analysis
- (Optional) NVD API key for higher rate limits

### Step 1: Import the Workflow

1. Download `SENTINEL.json` from this repository
2. In n8n, go to **Workflows** → **Import from File**
3. Select the downloaded JSON file

### Step 2: Set Up Baserow

1. Create a free account at [baserow.io](https://baserow.io)
2. Create a new database and table with these fields:
   - `fingerprint` (Text)
   - `title` (Text)
   - `created_on` (Date/Time, auto-set)
3. Get your API credentials from Baserow settings
4. In n8n, create a Baserow credential with your API key
5. Update all Baserow nodes with your database/table IDs

### Step 3: Configure Discord Webhooks

1. In your Discord server, go to **Server Settings** → **Integrations** → **Webhooks**
2. Create two webhooks:
   - `#critical-alerts` - For high-priority threats
   - `#threat-intel` - For general security news
3. Copy the webhook URLs
4. Update the Discord HTTP Request nodes in the workflow with your URLs

### Step 4: (Optional) Configure AI Analysis

1. Get an API key from [OpenAI](https://platform.openai.com)
2. In n8n, create an HTTP Header Auth credential:
   - Name: `Authorization`
   - Value: `Bearer sk-your-api-key-here`
3. Apply this credential to the "AI Summary" node

### Step 5: (Optional) Configure NVD API

1. Request a free API key from [NVD](https://nvd.nist.gov/developers/request-an-api-key)
2. In the "NVD Lookup" node, replace `YOUR_NVD_API_KEY_HERE` in the URL

### Step 6: Activate!

1. Toggle the workflow to **Active**
2. Watch the alerts flow into your Discord server

---

## ⚙️ Configuration

### Adjusting Alert Thresholds

In the "Filter Min Score 20" node, you can adjust the minimum score:
- **20** (default): Balanced signal-to-noise
- **15**: More alerts, more noise
- **30**: Fewer alerts, higher quality

### Adjusting Scan Frequency

Modify the Schedule Trigger nodes:
- **Critical sources**: Default 15 minutes (don't go lower - respect rate limits)
- **High priority**: Default 1 hour
- **General**: Default 4 hours

### Adding New Sources

1. Add a new RSS Feed Read node
2. Connect it to "Normalize & Extract Entities"
3. The source will be auto-detected, or add detection logic in the normalize code

### Customizing Scoring

Edit the "Smart Scoring Engine" code node to:
- Add/remove keywords
- Adjust point values
- Add new scoring factors

---

## 📊 Expected Volume

Based on typical threat landscape activity:

| Metric | Daily Estimate |
|--------|----------------|
| Articles Processed | 200-400 |
| After Scoring (20+) | 25-55 |
| After Deduplication | 15-35 |
| Critical Alerts | 0-5 |
| High/Medium Alerts | 15-30 |

---

## 💵 Cost Breakdown

| Component | Cost |
|-----------|------|
| n8n | Free (self-hosted) or $20/mo (cloud) |
| Baserow | Free tier (1000 rows) |
| Discord | Free |
| NVD API | Free |
| EPSS API | Free |
| OpenAI (optional) | ~$0.05-0.50/month |
| **Total** | **$0-21/month** |

---

## 🐛 Troubleshooting

### Duplicate Alerts Still Appearing

See [N8N-BASEROW-DEDUPLICATION-FIX.md](docs/N8N-BASEROW-DEDUPLICATION-FIX.md) for the solution. Key points:
- Don't set `"operation": "getMany"` on Baserow list nodes
- Ensure `"returnAll": true` is set
- Use simple fingerprint comparison (no String/trim)

### RSS Feed Errors

Some feeds may be temporarily unavailable. The workflow continues processing other feeds. Check:
- Is the URL still valid?
- Is the site blocking automated requests?
- Try adding `"ignoreSSL": true` in feed options

### Rate Limiting

If you're hitting API rate limits:
- NVD: Add your API key for higher limits
- Increase scan intervals
- The workflow has built-in delays between API calls

### Discord Webhook Errors

- Verify webhook URL is correct
- Check Discord server permissions
- Ensure embed format is valid JSON

---

## 🗺️ Roadmap

- [ ] Slack integration
- [ ] Microsoft Teams integration
- [ ] Email digest option
- [ ] Web dashboard
- [ ] Threat actor tracking database
- [ ] MITRE ATT&CK mapping visualization
- [ ] IOC export to STIX/TAXII format
- [ ] Integration with SIEM platforms

---

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request. For major changes, please open an issue first to discuss what you would like to change.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- [n8n](https://n8n.io) - Workflow automation platform
- [Baserow](https://baserow.io) - Open source database
- [CISA](https://www.cisa.gov) - Cybersecurity advisories
- [NVD](https://nvd.nist.gov) - Vulnerability database
- [FIRST.org](https://www.first.org/epss) - EPSS scoring
- All the security researchers and journalists whose work powers this platform

---

## ☕ Support

If SENTINEL has saved you time or money, consider:

[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://buymeacoffee.com/whthomas22)

---

## 📬 Contact

- LinkedIn: [Your LinkedIn]
- Twitter: [@YourHandle]
- Email: your.email@example.com

---

<p align="center">
  <b>Built with 💜 for the security community</b>
</p>
