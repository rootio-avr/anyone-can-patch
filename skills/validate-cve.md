# Validate CVE Patch - Build, Test, Review

**Usage**: `/validate-cve CVE-YYYY-NNNNN package-name vulnerable-version`

**Example**: `/validate-cve CVE-2025-68675 apache-airflow 2.10.5`

**Prerequisites**: Run `/patch-cve` first to apply the security fix

---

I need you to validate the security patch for CVE-{CVE_ID} on {package_name} {vulnerable_version}.

CONTEXT:
- CVE: {CVE_ID}
- Files modified: {list_files}
- What the fix does: {brief_description}

PHASE 1 - BUILD
Build the package:
- npm: `npm install && npm run build`
- pypi: `pip install -e .`

Must succeed with exit 0. If fails → STOP, report error.

PHASE 2 - REGRESSION TESTS
Run the existing test suite:
- npm: `npm test`
- pypi: `pytest`

Report: total / passed / failed. New failures = regression from patch.

PHASE 3 - SECURITY REVIEW
Review the patch and verify:
1. Fixes root cause (not just symptoms)
2. No dangerous patterns added (eval, exec, string concat for SQL/commands)
3. No obvious bypasses (encodings, edge cases)
4. Normal inputs still work

OUTPUT:
```json
{
  "build": {"success": true, "exit_code": 0},
  "regression_tests": {"total": 125, "passed": 125, "failed": 0, "new_failures": []},
  "security_review": {
    "root_cause_addressed": true,
    "dangerous_patterns": false,
    "bypass_concerns": "none|low|medium|high",
    "normal_operation_preserved": true
  },
  "verdict": "APPROVED|NEEDS_REVIEW|REJECTED",
  "summary": "Brief summary of findings",
  "untested_areas": ["List areas not covered by existing tests"],
  "recommendations": ["Any follow-up actions needed"]
}
```

Provide: summary, any regressions, security concerns, what wasn't tested.

RULES:
- Run full test suite (or relevant subset if thousands of tests)
- If no tests exist → note this, recommend manual testing
- When in doubt → flag for human review, don't approve blindly
- Be honest about limitations and untested scenarios
