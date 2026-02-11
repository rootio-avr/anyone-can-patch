# anyone-can-patch

# CVE Backporting Workshop - Three Prompts for Cursor/Claude Code

These prompts distill the multi-agent Patch Platoon AI system into three simplified prompts suitable for a 30-minute workshop. They focus on **application packages** (npm, pypi) only, not OS packages.

## No Permissions Required

These prompts are designed to run **with no permissions flags** in both Claude Code and Cursor.

The workflow clones repositories, analyzes code, and generates patches purely through local operations. You maintain full control over what happens and when.

## Prompt 1: Research & Identify Backportable Fix

**When to use:** You have a CVE ID and a vulnerable package version, and need to find the fix commit and assess backportability.

## Prompt 2: Apply Fix to Vulnerable Version

**When to use:** You have identified the fix commit(s) and need to apply them to your vulnerable package version.

## Prompt 3: Validate the Patched Package

**When to use:** Patch applied, need to verify it works.

## CVE and package Example

```
CVE-2025-68675 apache-airflow 2.10.5
```

Feel free to try out other CVE and package variations.


## For Workshop Participants:

**Best Practices:**
- Copy the prompts exactly as written (they're tuned for good results)
- Replace the `{placeholders}` with your specific CVE/package info
- Read the agent's output carefully and verify assumptions
- Don't trust the output blindly - these are starting points
- Use the JSON outputs to track what was found/changed

**When Things Go Wrong:**
- If Prompt 1 finds too many commits: Ask it to prioritize the smallest one
- If Prompt 2 creates syntax errors: Ask it to fix them with specific error messages
- If Prompt 3 tests fail: Ask it to investigate why and adjust the tests
- If stuck: Move to the next prompt and document the gap

**Beyond the Workshop:**
- For production use, consider a full Agentic system
- Always have security experts review AI-generated patches
- Combine AI assistance with human expertise
- Use these prompts as templates to develop your own
