## Prompt 1: Research & Identify Backportable Fix

**When to use:** You have a CVE ID and a vulnerable package version, and need to find the fix commit and assess backportability.

**Copy-paste this prompt:**

```
I need you to research CVE-{CVE_ID} affecting {package_name} version {vulnerable_version}
and find the best backportable fix.

RESEARCH STRATEGY - Execute in phases:

PHASE 1 - CVE VALIDATION (Do this first!)
1. Check CVE status (verify it's not DISPUTED, REJECTED, or RESERVED)
2. Validate CVE year is not in the future
3. If CVE is invalid, STOP and report why

PHASE 2 - PARALLEL INFORMATION GATHERING (Critical: Do these in ONE response!)
Execute ALL of these searches in parallel using multiple tool calls in a single response:
- Search NVD database for {CVE_ID} (look for "References" section with commits/PRs)
- Search GitHub for "{CVE_ID} {package_name}" (look for Security Advisories)
- Search upstream repository for "{CVE_ID}" in commit messages
- Search package changelogs/release notes for version that fixed it

For each search result, capture:
- Fix commit SHA(s) - MAY BE MULTIPLE COMMITS for one CVE!
- Fixed version number
- Link to commit/PR/advisory
- Brief description of what was changed

PHASE 3 - FIX ANALYSIS
1. Clone the upstream repository and check out the fix commit(s)
2. Analyze the changes:
   - How many files changed?
   - How many lines changed?
   - What type of fix? (validation, bounds checking, sanitization, etc.)
   - Are there test files? (exclude those from complexity count)

3. Complexity Assessment (1-10 scale):
   - 1-3 (LOW): ≤3 files, <100 lines → PROCEED confidently
   - 4-6 (MEDIUM): 4-10 files, 100-500 lines → PROCEED with caution
   - 7-9 (HIGH): >10 files, >500 lines → Warn about difficulty
   - 10 (BLOCKED): Architecture changes, refactors → NOT workshop-suitable

PHASE 4 - BACKPORT ASSESSMENT
1. Clone the vulnerable version {vulnerable_version}
2. Compare file structure between fix version and target version:
   - Do the modified files exist in target version?
   - Are the modified functions/classes present?
   - Are there path differences? (e.g., src/ vs lib/)
3. Identify potential challenges:
   - Missing functions that the fix depends on
   - Different code structure
   - Conflicting variable names

OUTPUT - Provide a structured summary:
```json
{
  "cve_id": "CVE-XXXX-XXXXX",
  "package": "{package_name}",
  "ecosystem": "npm|pypi",
  "vulnerable_version": "{vulnerable_version}",
  "fixed_version": "X.Y.Z",
  "fix_commits": [
    {
      "sha": "abc123...",
      "url": "https://github.com/...",
      "description": "Add input validation for..."
    }
  ],
  "complexity": {
    "score": 3,
    "level": "LOW",
    "files_changed": 2,
    "lines_changed": 45,
    "reasoning": "Simple validation added to one function"
  },
  "backport_assessment": {
    "feasible": true,
    "challenges": ["Function signature slightly different in target version"],
    "required_adaptations": ["May need to adjust parameter names"]
  },
  "recommendation": "PROCEED|CAUTION|BLOCKED"
}
```

Also provide:
- Plain English summary of what the vulnerability was
- Plain English summary of what the fix does
- Specific file paths and line numbers where changes are needed
- Any "gotchas" for backporting

OPTIMIZATION RULES:
- Target completing research in ≤10 tool calls
- Batch all independent searches in one response (Phase 2)
- Stop when you have "enough" information, not "perfect" information
- If you find fix commit in NVD, you may skip GitHub search
- If fix is complexity 8+, you can skip detailed backport assessment

IMPORTANT NOTES:
- Some CVEs require MULTIPLE commits - look for "follow-up" or "part 2"
- Release/merge commits usually aren't the actual fix - look for the PR
- Security advisories often link directly to the fixing commit
```
