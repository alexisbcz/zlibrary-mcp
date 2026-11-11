# MCP Server Recommendations for Hyperlibrary Scraping

Based on the comprehensive site analysis, here are the MCP server requirements:

## Critical MCP Servers Needed

### 1. 🔴 Playwright MCP (CRITICAL)
**Why**: Hyperlibrary's Hydra mode uses JavaScript-heavy parked domains and potential CAPTCHA
**Use Cases**:
- Handle JavaScript redirects on parked domains
- Solve CAPTCHA challenges when they appear
- Evade browser fingerprinting
- Navigate through JavaScript-rendered content

### 2. 🟡 HTTP Client MCP (RECOMMENDED)
**Why**: Advanced request handling beyond basic httpx
**Use Cases**:
- Cookie jar management across domains
- Automatic proxy rotation
- Request signature generation
- Rate limiting compliance

### 3. 🟢 Cache/Storage MCP (NICE TO HAVE)
**Why**: Persist discovered domains and sessions
**Use Cases**:
- Cache working domains
- Store session cookies
- Track domain health
- Remember user preferences

## Current Workaround

The Python bridge currently handles basic scraping needs:
- ✅ HTTP requests via httpx
- ✅ HTML parsing via BeautifulSoup
- ✅ Cookie management
- ✅ Retry logic

But CANNOT handle:
- ❌ JavaScript execution
- ❌ CAPTCHA solving
- ❌ Browser fingerprint evasion
- ❌ Dynamic domain discovery

## Implementation Priority

1. **Continue with Python bridge** (working for authenticated users)
2. **Monitor failure rates** (track when JS/CAPTCHA blocks occur)
3. **Implement Playwright MCP** when failure rate > 20%
4. **Add browser automation** as fallback, not replacement

## Setup Instructions

When ready to implement Playwright MCP:

```bash
# Install
npm install -g @modelcontextprotocol/server-playwright
npx playwright install chromium

# Configure
cat > .mcp/playwright-config.json << EOF
{
  "playwright": {
    "command": "npx",
    "args": ["@modelcontextprotocol/server-playwright"],
    "env": {
      "HEADLESS": "true",
      "BROWSER": "chromium",
      "TIMEOUT": "30000"
    }
  }
}
EOF
```

## Risk Matrix

| Scenario | Current (Python) | With Playwright |
|----------|-----------------|-----------------|
| Static HTML | ✅ Works | ✅ Works |
| JavaScript Pages | ❌ Fails | ✅ Works |
| CAPTCHA | ❌ Fails | ✅ Solvable |
| Domain Discovery | ⚠️ Manual | ✅ Automated |
| Anti-bot Detection | ⚠️ Basic | ✅ Advanced |

## Recommendation

**Current Status**: Python scraping is sufficient WITH:
- Valid credentials
- Active mirror domain
- No CAPTCHA challenges

**Upgrade Trigger**: Implement Playwright MCP when:
- CAPTCHA appears regularly
- JavaScript becomes mandatory
- Success rate drops below 80%
- Domain discovery needs automation