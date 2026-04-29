# CSV Import — Chat Transcript Example

*The following shows what a CSV import interaction looks like in chat. The agent reads the file, proposes a mapping, previews the data, flags issues, and waits for confirmation.*

---

**User:** "I have a spreadsheet from a networking event last week. Can I import these into my pipeline?"

**Agent:** Sure. Drop the file or give me the path.

**User:** `~/Downloads/contacts-april-2026.csv`

**Agent:** I've read `contacts-april-2026.csv`. Here's what's in it:

*23 rows, 6 columns*

**Headers:** Biz Name, Website, Contact, Email, Priority, Source

**Sample (first 5 rows):**

| Biz Name | Website | Contact | Email | Priority | Source |
|---|---|---|---|---|---|
| Summit Dental Care | summitdentalcare.ie | Dr. A. Murphy | amurphy@summit.ie | 8 | Networking |
| Clearview Physio | clearviewphysio.ie | Jane Walsh | jane@clearview.ie | 7 | Google |
| The Green Room Café | N/A | Tom Ryan | tom@greenroom.ie | 5 | Referral |
| Galway Autism Support | galwayautism.ie | S. O'Brien | sarah@galwayautism.ie | 7 | Website |
| West Coast Plumbing | wcplumbing.ie | M. Kelly | mark@wcplumbing.ie | NULL | Cold email |

---

**Proposed mapping:**

| Your column | Our field | Notes |
|---|---|---|
| Biz Name | `company` | ✅ |
| Website | `website` | ✅ — will add `https://` where missing |
| Contact | `contact_name` | ✅ |
| Email | `contact_email` | ✅ |
| Priority | `lead_score` | ✅ — validated as 1–10, NULL values will be imported as NULL |
| Source | — | **skip** — not in our pipeline schema |

---

**Preview (first 5 of 23 rows):**

| Company | Website | Score | Confidence | Status | Tags |
|---|---|---|---|---|---|
| Summit Dental Care | summitdentalcare.ie | 8 | LOW | lead | imported |
| Clearview Physio | clearviewphysio.ie | 7 | LOW | lead | imported |
| The Green Room Café | ⚠️ *(no website)* | 5 | LOW | lead | imported |
| Galway Autism Support | galwayautism.ie | 7 | LOW | lead | imported |
| West Coast Plumbing | wcplumbing.ie | NULL | LOW | lead | imported |

---

**Flags:**

- ⚠️ **1 duplicate:** O'Connor & Sons Solicitors already exists in your pipeline (score 6, status: proposal_sent). Will skip.
- ⚠️ **2 rows with no website:** The Green Room Café, Rapid IT Solutions. Will import with NULL website.
- ⚠️ **1 row with no company name:** Row 14 is empty — will skip (company is required).

---

**Summary:**
- 23 rows total
- 20 to import
- 1 skipped (duplicate)
- 1 skipped (no company name)
- 2 with missing website (will import as NULL)
- 1 with invalid score (value "11" in row 9 — will set to NULL)

**Defaults applied to all imported rows:**
- Status: `lead`
- Data confidence: `LOW`
- Tags: `imported`
- Research notes: "Imported from contacts-april-2026.csv on 2026-04-29"

The source CSV will be copied to `$WEBCLIENT_STUDIO_CONFIG_DIR/imports/` for your records.

---

Does this mapping look right? Say **"go"** to import, or tell me if you want to change anything.
