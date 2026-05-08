---
name: fetch-job-description
description: Use when the user pastes a 104.com.tw or Cake.me job posting URL and wants to extract structured job data. Triggers on phrases like "get job description", "parse this job", "extract job details", or when a 104/cake.me job URL is shared for analysis.
metadata:
  author: luchengchiu
  version: "1.0.0"
  argument-hint: <job-posting-url>
---

# Fetch Job Description

Extract structured job data from a 104.com.tw or Cake.me job posting URL.

## How It Works

1. Check if `job-description-schema.md` exists in the project root directory. If found, use it as the extraction schema; otherwise, read the default `schema.md` from this skill's directory.
2. Detect the platform from the URL (`104.com.tw` → `"104"`, `cake.me` → `"cake.me"`)
3. Use `mcp__playwright__browser_navigate` to navigate to the job posting URL
4. Use `mcp__playwright__browser_snapshot` to capture the fully rendered page content
5. Apply the platform-specific field mappings from the schema and return a single JSON code block

## Usage

When the user provides a job posting URL:
1. Look for `job-description-schema.md` in the project root. If not found, read the default `schema.md` from this skill's directory.
2. Detect the platform from the URL
3. Navigate to the URL using Playwright
4. Take a snapshot of the rendered page
5. Extract all fields per the schema and return the JSON output

If no URL is provided, ask the user to paste the job posting URL.

## Error Handling: Browser Launch Failures

If `mcp__playwright__browser_navigate` fails with a browser launch error (e.g. "Failed to launch the browser process" or "existing browser session"):

1. Ask the user for permission to close the Playwright browser instance
2. If approved, kill only Playwright-managed Chrome processes (not the user's regular browser):
   ```bash
   pkill -f 'user-data-dir=.*ms-playwright'
   ```
   This targets only Chrome instances launched by Playwright (identifiable by their `--user-data-dir` containing `ms-playwright`), leaving the user's normal Chrome tabs untouched.
3. Wait 2 seconds (`sleep 2`)
4. Retry `mcp__playwright__browser_navigate` with the original URL
5. If the user declines, inform them to close Chrome manually and retry
