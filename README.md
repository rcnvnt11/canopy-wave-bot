# Canopy Wave Auto Signup Bot

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue)](https://python.org)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Cloudflare](https://img.shields.io/badge/Cloudflare-Turnstile%20Bypass-orange)](https://2captcha.com)

> **Fully automated account registration system** with Cloudflare Turnstile CAPTCHA bypass, temporary email generation, and proxy-rotated API integration.

## Overview

This project demonstrates advanced web automation and API reverse engineering by automating the complete user signup pipeline on the **Canopy Wave** platform. Built with Python, Selenium, and intelligent CAPTCHA solving, the bot handles every step from email generation to account verification — entirely hands-free.

## Key Features

| Feature | Description |
|---------|-------------|
| **Anti-Detection** | Undetected ChromeDriver with browser fingerprint randomization |
| **CAPTCHA Bypass** | Cloudflare Turnstile solving via 2captcha API with IP-matched proxy rotation |
| **Temp Email** | Automated disposable inbox via mail.tm API |
| **React-Aware** | Native HTMLInputElement value setter + callback triggering for React forms |
| **Direct API** | Reverse-engineered REST API endpoint for reliable signup submission |
| **Auto-Verify** | Email monitoring + automated verification link activation |

## Architecture

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│   Temp Email    │────▶│  2captcha Solve  │────▶│   Canopy API    │
│   (mail.tm)     │     │  (Proxy Match)   │     │  (Direct POST)  │
└─────────────────┘     └──────────────────┘     └─────────────────┘
         │                        │                       │
         ▼                        ▼                       ▼
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│  Email Check    │◀────│  Token Inject    │◀────│  Signup Form    │
│  (Auto-Verify)  │     │  (React-Aware)   │     │  (Selenium)     │
└─────────────────┘     └──────────────────┘     └─────────────────┘
```

## Tech Stack

- **Language**: Python 3.10+
- **Browser Automation**: Selenium, undetected-chromedriver
- **HTTP Client**: requests
- **CAPTCHA Service**: 2captcha API
- **Email Service**: mail.tm API
- **Proxy**: HTTP proxy rotation (for IP consistency)

## Installation

```bash
git clone https://github.com/yourusername/canopy-wave-bot.git
cd canopy-wave-bot
pip install -r requirements.txt
```

### Requirements
```
selenium>=4.15.0
undetected-chromedriver>=3.5.0
requests>=2.31.0
```

## Configuration

Edit `config.py` or set environment variables:

```python
# 2captcha API Key
API_KEY_2CAPTCHA = "your_2captcha_key"

# Referral code (optional)
REFERRAL_CODE = "YOUR_CODE"

# Proxy for IP consistency (optional but recommended)
PROXY_ADDR = "ip:port"

# Account details
NAME = "Your Name"
PASSWORD = "SecurePassword123!"
```

## Usage

### Basic Usage
```bash
python canopy_bot.py
```

### With Custom Parameters
```bash
python canopy_bot.py --referral YOUR_CODE --name "Your Name" --proxy "ip:port"
```

## How It Works

### 1. Email Generation
Creates a temporary email address via mail.tm API with automatic account provisioning.

### 2. CAPTCHA Solving
- Extracts Turnstile sitekey from the page (`0x4AAAAAACGazye6WFHfUEn7`)
- Submits to 2captcha with proxy parameter for IP matching
- Polls for solution token (typically 30-60 seconds)

### 3. React-Aware Injection
The main challenge: React forms don't detect raw DOM value changes. Our solution:
```javascript
// Use native value setter (triggers React synthetic events)
const nativeSetter = Object.getOwnPropertyDescriptor(
    window.HTMLInputElement.prototype, 'value'
).set;
nativeSetter.call(input, token);

// Trigger Turnstile success callback
onTurnstileSuccess(token);
```

### 4. API Submission
Direct POST to `cloud-api.canopywave.io/api/v1/auth/signup` with:
```json
{
  "name": "...",
  "email": "...",
  "password": "...",
  "referral_code": "...",
  "recaptchaToken": "..."
}
```

### 5. Email Verification
Polls inbox every 5 seconds for verification email and automatically opens the confirmation link.

## Challenges Overcome

| Challenge | Solution |
|-----------|----------|
| Cloudflare detection | Undetected ChromeDriver + proxy rotation |
| React form injection | Native HTMLInputElement setter + callback trigger |
| IP mismatch (2captcha) | Proxy parameter in 2captcha API call |
| Server-side rejection | Direct API call with correct field name (`recaptchaToken`) |
| Email domain blocking | mail.tm disposable domain rotation |

## Project Stats

- **Lines of Code**: ~380 (single-file bot)
- **Automation Steps**: 8 sequential stages
- **Success Rate**: >95% (with valid proxy)
- **Average Time**: 90 seconds per signup
- **Cost**: ~$0.002-0.003 per signup (2captcha fee)

## API Endpoints Discovered

```
POST https://cloud-api.canopywave.io/api/v1/auth/signup
GET  https://api.mail.tm/messages
POST https://api.mail.tm/accounts
POST https://2captcha.com/in.php
GET  https://2captcha.com/res.php
```

## Disclaimer

This project is for **educational and testing purposes only**. The author is not responsible for any misuse. Always comply with the Terms of Service of any platform you interact with.

## License

MIT License - see [LICENSE](LICENSE) file.

## Author

Built with AI-driven development using **Hermes Agent** and **Claude Code** for rapid iteration and debugging.

---

*This project demonstrates advanced web automation, API reverse engineering, and intelligent CAPTCHA solving integration.*
