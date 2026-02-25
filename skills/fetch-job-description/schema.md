# Job Description Extraction Schema

This file defines the extraction rules, field mappings, and output format for the `fetch-job-description` skill.

---

## Section 1: Common Fields

These fields are always present in the output for both platforms.

| JSON Key             | Description                              |
|----------------------|------------------------------------------|
| `source_url`         | The URL provided by the user             |
| `source_platform`    | `"104"` or `"cake.me"`                   |
| `title`              | Job title (h1)                           |
| `company`            | Company name                             |
| `updated_at`         | Last updated date text                   |
| `location`           | Work location                            |
| `employment_type`    | Full-time / part-time                    |
| `salary`             | Salary text as shown on the page         |
| `responsibilities`   | Array of job duties / 工作內容           |
| `requirements.required`  | Array — required qualifications and skills |
| `requirements.preferred` | Array — nice-to-have / bonus qualifications |

---

## Section 2: Platform-Specific Fields

### 104.com.tw

**Additional fields present on 104:**

| JSON Key                  | UI Label (Chinese) | Notes                            |
|---------------------------|--------------------|----------------------------------|
| `experience_required`     | 工作經歷           | e.g. `"不拘"`, `"3年以上"`       |
| `contact.person`          | 聯絡人             | e.g. `"人資經理"`                |
| `contact.response_time`   | 應徵回覆           | e.g. `"7個工作天"`               |

**Merging rules for 104:**
- `education_required` (學歷要求, e.g. `"大學以上"`) → merge as first item in `requirements.required` array
- Do NOT include it as a standalone field

**Fields not applicable on 104 → set to `null`:**
- `seniority`
- `interview_process`

---

### Cake.me

**Additional fields present on Cake.me:**

| JSON Key            | UI Label / Tag        | Notes                                              |
|---------------------|-----------------------|----------------------------------------------------|
| `seniority`         | 初階 / 中高階 tag     | e.g. `"中高階"`, `"初階"`                          |
| `interview_process` | 面試流程 section      | Describe interview rounds as a single string       |

**Merging rules for Cake.me:**
- Years-of-experience tag (e.g. `"需具備 3 年以上工作經驗"`) → merge into `requirements.required` array
- Do NOT include it as a standalone field

**Fields not applicable on Cake.me → set to `null`:**
- `experience_required`
- `contact`

---

## Section 3: Excluded Fields

These fields are explicitly NOT included in the output:

| Field              | Reason for exclusion                              |
|--------------------|---------------------------------------------------|
| `categories`       | Job category tags — not useful for analysis       |
| `applicant_count`  | Number of applicants — excluded                   |
| `headcount`        | Number of open positions — excluded               |
| `benefits`         | All benefit items (legal and others) — excluded   |
| `education_required` | Merged into `requirements.required` instead     |

---

## Section 4: Output Format Rules

- **Keys**: English snake_case
- **Values**: original language as displayed on the page (Chinese or English as-is)
- **Array fields**: each bullet point or list item as a separate string element in the array
- **Null fields**: explicitly set to `null`, never omit the key

---

## Section 5: Output JSON Shape

### 104.com.tw output

```json
{
  "source_url": "...",
  "source_platform": "104",
  "title": "...",
  "company": "...",
  "updated_at": "...",
  "location": "...",
  "employment_type": "...",
  "salary": "...",
  "experience_required": "...",
  "seniority": null,
  "responsibilities": ["..."],
  "requirements": {
    "required": ["大學以上", "..."],
    "preferred": ["..."]
  },
  "interview_process": null,
  "contact": {
    "person": "...",
    "response_time": "..."
  }
}
```

### Cake.me output

```json
{
  "source_url": "...",
  "source_platform": "cake.me",
  "title": "...",
  "company": "...",
  "updated_at": "...",
  "location": "...",
  "employment_type": "...",
  "salary": "...",
  "experience_required": null,
  "seniority": "中高階",
  "responsibilities": ["..."],
  "requirements": {
    "required": ["需具備 3 年以上工作經驗", "..."],
    "preferred": ["..."]
  },
  "interview_process": "...",
  "contact": null
}
```

---

## Section 6: Platform Differences Summary

| Field                | 104.com.tw                           | Cake.me                               |
|----------------------|--------------------------------------|---------------------------------------|
| `seniority`          | `null`                               | ✓ (初階 / 中高階)                     |
| `experience_required`| ✓ (工作經歷 as explicit field)       | `null` (years merged into requirements)|
| `education_required` | merged into `requirements.required`  | not applicable                        |
| `interview_process`  | `null`                               | ✓ (面試流程 section)                  |
| `contact`            | ✓ (聯絡方式)                         | `null`                                |

---

## Section 7: Extensibility

To add a new platform (e.g. LinkedIn):
1. Add a new subsection under **Section 2** with the platform's field mappings and merge rules
2. List any fields that should be `null` for that platform
3. Add an example JSON shape to **Section 5**
4. No changes to `SKILL.md` are needed
