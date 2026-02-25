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

1. Read `schema.md` in the same directory to load the extraction schema and field mappings
2. Detect the platform from the URL (`104.com.tw` → `"104"`, `cake.me` → `"cake.me"`)
3. Use `mcp__playwright__browser_navigate` to navigate to the job posting URL
4. Use `mcp__playwright__browser_snapshot` to capture the fully rendered page content
5. Apply the platform-specific field mappings from `schema.md` and return a single JSON code block

## Usage

When the user provides a job posting URL:
1. Read `schema.md` from this skill's directory for the extraction schema
2. Detect the platform from the URL
3. Navigate to the URL using Playwright
4. Take a snapshot of the rendered page
5. Extract all fields per the schema and return the JSON output

If no URL is provided, ask the user to paste the job posting URL.
