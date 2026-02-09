# CVE Backporting Skills for Claude Code

These skills provide a streamlined workflow for researching, patching, and validating CVE fixes in npm and pypi packages.

## Quick Start

Use these three skills in sequence:

```bash
# 1. Research the CVE and find the fix
/research-cve CVE-2025-68675 apache-airflow 2.10.5

# 2. Apply the fix to the vulnerable version
/patch-cve CVE-2025-68675 apache-airflow 2.10.5

# 3. Validate the patched package
/validate-cve CVE-2025-68675 apache-airflow 2.10.5
```

**Total time**: 15-25 minutes for LOW complexity CVEs

---

## Skills Overview

### 1. `/research-cve` - Find Backportable Fix

**Purpose**: Research a CVE and identify the best backportable fix

**What it does**:
- ✅ Validates CVE status (not DISPUTED/REJECTED/RESERVED)
- ✅ Searches NVD, GitHub, upstream repos in parallel
- ✅ Analyzes fix commits and complexity (1-10 scale)
- ✅ Assesses backport feasibility to target version
- ✅ Provides PROCEED/CAUTION/BLOCKED recommendation

**Output**: JSON report with fix commits, complexity assessment, and backport challenges

**Time**: 5-10 minutes

---

### 2. `/patch-cve` - Apply Fix to Vulnerable Version

**Purpose**: Backport the security fix to the vulnerable package version

**What it does**:
- ✅ Clones vulnerable version source code
- ✅ Creates patch branch
- ✅ Extracts security-relevant changes only
- ✅ Adapts changes for target version
- ✅ Generates unified diff patch file

**Output**: Modified files + unified diff patch + explanation of changes

**Time**: 5-10 minutes

---

### 3. `/validate-cve` - Build, Test, Review

**Purpose**: Validate the patched package works and is secure

**What it does**:
- ✅ Builds the patched package
- ✅ Runs existing test suite for regressions
- ✅ Security review of the patch
- ✅ Provides APPROVED/NEEDS_REVIEW/REJECTED verdict

**Output**: JSON validation report with test results and security assessment

**Time**: 5-7 minutes

---

## Installation

### Option 1: Use Directly (No Installation)

Reference the skill files directly:

```bash
claude-code --skill=skills/research-cve.md CVE-2025-68675 apache-airflow 2.10.5
```

### Option 2: Install to Claude Config

Copy skills to your Claude skills directory:

```bash
cp skills/*.md ~/.claude/skills/
```

Then invoke with the short syntax:

```bash
/research-cve CVE-2025-68675 apache-airflow 2.10.5
```

---

## Usage Examples

### Example 1: Apache Airflow CVE-2025-68675

```bash
# Research
/research-cve CVE-2025-68675 apache-airflow 2.10.5
# → Complexity: 2/10 (LOW), Recommendation: PROCEED

# Patch
/patch-cve CVE-2025-68675 apache-airflow 2.10.5
# → Adds proxy fields to DEFAULT_SENSITIVE_FIELDS
# → Wraps 4 log statements with redact()

# Validate
/validate-cve CVE-2025-68675 apache-airflow 2.10.5
# → Build: SUCCESS
# → Tests: 80/80 passed
# → Verdict: APPROVED
```

### Example 2: npm package (hypothetical)

```bash
# Research
/research-cve CVE-2024-12345 body-parser 1.19.0
# → Complexity: 3/10 (LOW), Recommendation: PROCEED

# Patch
/patch-cve CVE-2024-12345 body-parser 1.19.0
# → Adds input validation to querystring.parse()

# Validate
/validate-cve CVE-2024-12345 body-parser 1.19.0
# → Build: SUCCESS
# → Tests: 89/89 passed
# → Verdict: APPROVED
```

---

## Supported Ecosystems

- ✅ **npm** (JavaScript/TypeScript packages)
- ✅ **pypi** (Python packages)

---

## Complexity Scoring

Skills use a 1-10 complexity scale:

| Score | Level | Files | Lines | Recommendation |
|-------|-------|-------|-------|----------------|
| 1-3 | LOW | ≤3 | <100 | PROCEED confidently |
| 4-6 | MEDIUM | 4-10 | 100-500 | PROCEED with caution |
| 7-9 | HIGH | >10 | >500 | Warn about difficulty |
| 10 | BLOCKED | Many | Many | Not suitable for automated backporting |

---

## Key Features

### Parallel Execution
The research skill executes all searches in parallel (NVD, GitHub, upstream) in a single LLM response for speed.

### Security-Only Changes
The patch skill extracts only security-relevant changes, excluding refactoring, style changes, and documentation.

### Honest Validation
The validate skill provides honest assessments including untested areas and limitations.

### Ecosystem Awareness
Skills handle npm vs pypi differences (file paths, build commands, test frameworks).

---

## Limitations

These skills are simplified for workshop/demo purposes and cannot:
- ❌ Handle high complexity fixes (7+ score) reliably
- ❌ Detect when a CVE requires multiple commits
- ❌ Validate against real exploits (no PoC execution)
- ❌ Push to GitLab and trigger CI/CD
- ❌ Create merge requests automatically
- ❌ Use internal databases of prior patches

For production use, see the full [Patch Platoon AI](../) multi-agent system.

---

## Tips for Best Results

1. **Use the skills in sequence** - Each skill builds on the previous one
2. **Review the output** - Don't blindly trust AI-generated patches
3. **Start with LOW complexity CVEs** - These have the highest success rate
4. **Provide context** - If the skill asks for clarification, provide it
5. **Manual review required** - Always have a security expert review before deploying

---

## Workshop Use

These skills are designed for a 30-minute workshop:

| Time | Activity |
|------|----------|
| 0-3 min | Introduction to CVE backporting |
| 3-11 min | Demo `/research-cve` |
| 11-18 min | Demo `/patch-cve` |
| 18-23 min | Demo `/validate-cve` |
| 23-30 min | Q&A and discussion |

---

## Troubleshooting

**Problem**: Skill finds wrong commit
- **Solution**: Manually specify the commit SHA to `/patch-cve`

**Problem**: Patch creates syntax errors
- **Solution**: Ask Claude to fix with specific error message

**Problem**: Tests fail after patching
- **Solution**: Check if failures are related to patch or pre-existing

**Problem**: CVE is too complex (7+ score)
- **Solution**: This indicates automated backporting may not work reliably

---

## Contributing

To improve these skills:
1. Test with different CVEs and packages
2. Document edge cases and failure modes
3. Refine prompts based on results
4. Share successful examples

---

## License

Part of the Patch Platoon AI project. See [LICENSE](../LICENSE) for details.
