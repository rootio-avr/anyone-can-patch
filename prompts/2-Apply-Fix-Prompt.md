## Prompt 2: Apply Fix to Vulnerable Version

**When to use:** You have identified the fix commit(s) and need to apply them to your vulnerable package version.

**Copy-paste this prompt:**

```
I need you to backport the security fix for CVE-{CVE_ID} to {package_name} version
{vulnerable_version}.

CONTEXT FROM RESEARCH:
- Fixed in version: {fixed_version}
- Fix commit(s): {commit_sha}
- Files to modify: {list_files}
- Complexity: {LOW|MEDIUM|HIGH}

PATCHING STRATEGY:

PHASE 1 - WORKSPACE SETUP
1. Locate or clone the vulnerable version {vulnerable_version} source code
2. Create a new branch: `patch-{CVE_ID}`
3. Verify the package has SOURCE FILES (not just compiled/dist files)
   - For npm: Check that src/ or lib/ has .js/.ts files (not just dist/)
   - For pypi: Check for .py files (not just .whl or .egg)

PHASE 2 - EXTRACT SECURITY CHANGES
1. Review the fix commit diff carefully
2. Identify the SECURITY-RELEVANT changes only:
   - Validation checks (input sanitization, bounds checking)
   - Error handling additions
   - Buffer size corrections
   - Authentication/authorization checks
3. EXCLUDE non-security changes:
   - Code style/formatting changes
   - Refactoring that doesn't affect security
   - Documentation updates
   - Test file changes (handle separately)

PHASE 3 - ADAPT & APPLY CHANGES
1. For each security-relevant change:
   - Find the equivalent location in the target version
   - Adapt variable names if they differ
   - Adapt code structure if needed (e.g., callback → promise)
   - Preserve the SECURITY LOGIC even if syntax must change

2. Handle common backporting challenges:
   - If functions were renamed: Find the equivalent function
   - If files were moved: Find the new location
   - If dependencies missing: Add minimal required dependencies
   - If API changed: Adapt to the older API surface

PHASE 4 - VALIDATION
1. Verify the patch logically addresses the vulnerability:
   - Does it add validation where the CVE showed there was none?
   - Does it fix the buffer overflow / injection / traversal?
   - Does it prevent the attack vector described in the CVE?

2. Check for dangerous patterns introduced:
   - No `eval()` or `exec()` added
   - No SQL string concatenation
   - No shell command injection vectors
   - No unsafe deserialization

OUTPUT - Provide:
1. Complete list of modified files with the changes made
2. A unified diff patch file in standard format:

patch
--- a/path/to/file.js
+++ b/path/to/file.js
@@ -10,7 +10,10 @@
 function vulnerableFunction(input) {
-  return processInput(input);
+  if (!input || input.length > MAX_LENGTH) {
+    throw new Error('Invalid input');
+  }
+  return processInput(input);
 }

3. Explanation of each change:
   - What security issue does it fix?
   - How was it adapted from the original fix?
   - Any differences from the upstream fix?

PATCHING RULES:
- ONLY change what's necessary for security
- Preserve existing code style and formatting
- Don't refactor surrounding code
- Don't add comments unless clarifying security logic
- Don't add features or "improvements"
- Keep changes minimal and focused

ECOSYSTEM-SPECIFIC NOTES:

For **npm packages**:
- Patch .js or .ts files in src/ or lib/ (not dist/)
- Don't modify package.json unless adding security-critical dependency
- Preserve CommonJS vs ESM style

For **pypi packages**:
- Patch .py files in package root or src/
- Don't modify setup.py unless adding security-critical dependency
- Follow existing indentation (spaces vs tabs)

CRITICAL LIMITATIONS:
- If the fix requires new dependencies: Flag as "may require manual testing"
- If the code structure is too different: Flag as "needs manual adaptation"
- If you're unsure: Document assumptions and flag for human review

**IMPORTANT: DO NOT COMMIT OR PUSH CHANGES**
- Create the patch file only
- Stage changes for review if needed
- NEVER run git commit or git push
- Leave it to the user to decide when/how to commit

```
