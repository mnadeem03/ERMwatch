# ERMwatch Status Checking Logic

**Transparency Note**  
We believe in complete openness. This document explains exactly how ERMwatch determines whether an electronic resource is "Working" or "Down". This helps libraries and institutions understand the tool and build trust.

### Purpose
ERMwatch performs a simple, polite accessibility check on the URLs you add (databases, e-journals, e-books, repositories, websites, etc.).  
It does **not** attempt to login, scrape content, bypass authentication, or perform any aggressive actions.

### Checking Process (Step by Step)

For every resource, the app follows this logic every **10 minutes**:

1. **Primary Check – HEAD Request**
   - Sends a lightweight `HEAD` request to the URL.
   - If the server returns a status code **below 400** (including 200, 301, 302, etc.) → **Marked as WORKING**.
   - If the server returns **401 or 403** → **Also marked as WORKING**.
     - *Reason*: Many legitimate academic databases, subscribed e-journals, and scholarly platforms return 401/403 because they require authentication or institutional access. These are still fully functional for authorized users.

2. **Fallback Check – GET Request**
   - If the HEAD request fails or returns an error code (other than 401/403), the app tries a normal `GET` request.
   - Same rule applies: status code **below 400** or **401/403** → **WORKING**.
   - Any other response (404, 500, 502, timeout, connection error, etc.) → **DOWN**.

3. **Error Handling**
   - **Timeout** (after 8–10 seconds) → "Timeout"
   - **Connection refused / unreachable** → "Connection failed"
   - **SSL / Certificate issues** → Logged as error (but app continues)
   - Any other exception → Short, readable error message is stored

### Key Design Decisions

- **10-minute interval**: Balanced between timely detection and being respectful to servers (not too frequent).
- **Standard User-Agent**: The app identifies itself similarly to a normal web browser.
- **No Redirect Following** (limited): Prevents unnecessary load.
- **SSL Warnings Suppressed**: Common with some library vendor certificates; does not affect accuracy.
- **Treats 401/403 as UP**: This is critical for credibility in academic environments. A strict "only 200 OK" check would incorrectly mark many valid subscribed resources as Down.

### Important Notes for Institutions & Vendors

- This is a **lightweight accessibility test** — exactly what a librarian or user would experience when opening the link in their browser.
- The tool does **not** violate any terms of service. It performs the same checks a human would do manually.
- No data is collected or sent anywhere. Everything stays on your local computer.
- Checks are infrequent and non-aggressive.

### Why This Logic?

We wanted the status to be **credible and practical**:
- "Working" means the resource is accessible (as it would be for your users).
- "Down" means there is a real problem that needs attention (connection issue, server error, etc.).

This approach has been tested with real scholarly databases, open repositories, and institutional websites.

---

**Feedback Welcome**  
If you are a librarian, systems administrator, or developer and have suggestions to improve this logic while keeping it simple and respectful, please open an Issue on GitHub.

We are happy to discuss and refine it together for the benefit of the global library community.

---

*Part of ERMwatch v1.1 – Open tool for libraries*  
Developed by Muhammad Nadeem with assistance from Grok AI (xAI)