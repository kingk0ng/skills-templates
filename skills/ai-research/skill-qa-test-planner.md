---
id: skill-qa-test-planner
type: skill
name: qa-test-planner
description: Generate comprehensive test plans, manual test cases, regression test
  suites, and bug reports for QA engineers. Includes Figma MCP integration for design
  validation.
category: ai-research
complexity: medium
keywords:
- api
- go
- performance
- qa
- security
- sql
- test
- testing
- vulnerability
capabilities: []
token_estimate: 3589
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~3,589 -->
<!-- Includes Reference Materials -->

> **How to Use This Template**
>
> This template can be used with various AI coding assistants:
> 
> **GitHub Copilot:**
> - Add to `.github/copilot-instructions.md` in your repository
> - Reference in chat: `@workspace` to include in context
> - Add specific sections to your workspace instructions
> 
> **Augment Code:**
> - Load context: `aug context add <path-to-this-file>`
> - Reference in queries naturally
> - Use with specific commands
> 
> **Claude (Desktop/Web):**
> - Add to Project Knowledge in Claude Projects
> - Reference in custom instructions
> - Copy relevant sections as needed
> 
> **ChatGPT:**
> - Add to Custom GPT configuration
> - Include in conversation instructions
> - Use as system prompt
> 
> **Generic Usage:**
> Copy the content below and paste it into your AI assistant's context window
> or system instructions.

---




# qa-test-planner

> Generate comprehensive test plans, manual test cases, regression test suites, and bug reports for QA engineers. Includes Figma MCP integration for design validation.

# QA Test Planner

A comprehensive skill for QA engineers to create test plans, generate manual test cases, build regression test suites, validate designs against Figma, and document bugs effectively.

> **Activation:** This skill is triggered only when explicitly called by name (e.g., `/qa-test-planner`, `qa-test-planner`, or `use the skill qa-test-planner`).

---

## Quick Start

**Create a test plan:**
```
"Create a test plan for the user authentication feature"
```

**Generate test cases:**
```
"Generate manual test cases for the checkout flow"
```

**Build regression suite:**
```
"Build a regression test suite for the payment module"
```

**Validate against Figma:**
```
"Compare the login page against the Figma design at [URL]"
```

**Create bug report:**
```
"Create a bug report for the form validation issue"
```

---

## Quick Reference

| Task | What You Get | Time |
|------|--------------|------|
| Test Plan | Strategy, scope, schedule, risks | 10-15 min |
| Test Cases | Step-by-step instructions, expected results | 5-10 min each |
| Regression Suite | Smoke tests, critical paths, execution order | 15-20 min |
| Figma Validation | Design-implementation comparison, discrepancy list | 10-15 min |
| Bug Report | Reproducible steps, environment, evidence | 5 min |

---

## How It Works

```
Your Request
    │
    ▼
┌─────────────────────────────────────────────────────┐
│ 1. ANALYZE                                          │
│    • Parse feature/requirement                      │
│    • Identify test types needed                     │
│    • Determine scope and priorities                 │
├─────────────────────────────────────────────────────┤
│ 2. GENERATE                                         │
│    • Create structured deliverables                 │
│    • Apply templates and best practices             │
│    • Include edge cases and variations              │
├─────────────────────────────────────────────────────┤
│ 3. VALIDATE                                         │
│    • Check completeness                             │
│    • Verify traceability                            │
│    • Ensure actionable steps                        │
└─────────────────────────────────────────────────────┘
    │
    ▼
QA Deliverable Ready
```

---

## Commands

### Interactive Scripts

| Script | Purpose | Usage |
|--------|---------|-------|
| `./scripts/generate_test_cases.sh` | Create test cases interactively | Step-by-step prompts |
| `./scripts/create_bug_report.sh` | Generate bug reports | Guided input collection |

### Natural Language

| Request | Output |
|---------|--------|
| "Create test plan for {feature}" | Complete test plan document |
| "Generate {N} test cases for {feature}" | Numbered test cases with steps |
| "Build smoke test suite" | Critical path tests |
| "Compare with Figma at {URL}" | Visual validation checklist |
| "Document bug: {description}" | Structured bug report |

---

## Core Deliverables

### 1. Test Plans
- Test scope and objectives
- Testing approach and strategy
- Environment requirements
- Entry/exit criteria
- Risk assessment
- Timeline and milestones

### 2. Manual Test Cases
- Step-by-step instructions
- Expected vs actual results
- Preconditions and setup
- Test data requirements
- Priority and severity

### 3. Regression Suites
- Smoke tests (15-30 min)
- Full regression (2-4 hours)
- Targeted regression (30-60 min)
- Execution order and dependencies

### 4. Figma Validation
- Component-by-component comparison
- Spacing and typography checks
- Color and visual consistency
- Interactive state validation

### 5. Bug Reports
- Clear reproduction steps
- Environment details
- Evidence (screenshots, logs)
- Severity and priority

---

## Anti-Patterns

| Avoid | Why | Instead |
|-------|-----|---------|
| Vague test steps | Can't reproduce | Specific actions + expected results |
| Missing preconditions | Tests fail unexpectedly | Document all setup requirements |
| No test data | Tester blocked | Provide sample data or generation |
| Generic bug titles | Hard to track | Specific: "[Feature] issue when [action]" |
| Skip edge cases | Miss critical bugs | Include boundary values, nulls |

---

## Verification Checklist

**Test Plan:**
- [ ] Scope clearly defined (in/out)
- [ ] Entry/exit criteria specified
- [ ] Risks identified with mitigations
- [ ] Timeline realistic

**Test Cases:**
- [ ] Each step has expected result
- [ ] Preconditions documented
- [ ] Test data available
- [ ] Priority assigned

**Bug Reports:**
- [ ] Reproducible steps
- [ ] Environment documented
- [ ] Screenshots/evidence attached
- [ ] Severity/priority set

---

## References

- [Test Case Templates](references/test_case_templates.md) - Standard formats for all test types
- [Bug Report Templates](references/bug_report_templates.md) - Documentation templates
- [Regression Testing Guide](references/regression_testing.md) - Suite building and execution
- [Figma Validation Guide](references/figma_validation.md) - Design-implementation validation

---

<details>
<summary><strong>Deep Dive: Test Case Structure</strong></summary>

### Standard Test Case Format

```markdown
## TC-001: [Test Case Title]

**Priority:** High | Medium | Low
**Type:** Functional | UI | Integration | Regression
**Status:** Not Run | Pass | Fail | Blocked

### Objective
[What are we testing and why]

### Preconditions
- [Setup requirement 1]
- [Setup requirement 2]
- [Test data needed]

### Test Steps
1. [Action to perform]
   **Expected:** [What should happen]

2. [Action to perform]
   **Expected:** [What should happen]

3. [Action to perform]
   **Expected:** [What should happen]

### Test Data
- Input: [Test data values]
- User: [Test account details]
- Configuration: [Environment settings]

### Post-conditions
- [System state after test]
- [Cleanup required]

### Notes
- [Edge cases to consider]
- [Related test cases]
- [Known issues]
```

### Test Types

| Type | Focus | Example |
|------|-------|---------|
| Functional | Business logic | Login with valid credentials |
| UI/Visual | Appearance, layout | Button matches Figma design |
| Integration | Component interaction | API returns data to frontend |
| Regression | Existing functionality | Previous features still work |
| Performance | Speed, load handling | Page loads under 3 seconds |
| Security | Vulnerabilities | SQL injection prevented |

</details>

<details>
<summary><strong>Deep Dive: Test Plan Template</strong></summary>

### Test Plan Structure

```markdown
# Test Plan: [Feature/Release Name]

## Executive Summary
- Feature/product being tested
- Testing objectives
- Key risks
- Timeline overview

## Test Scope

**In Scope:**
- Features to be tested
- Test types (functional, UI, performance)
- Platforms and environments
- User flows and scenarios

**Out of Scope:**
- Features not being tested
- Known limitations
- Third-party integrations (if applicable)

## Test Strategy

**Test Types:**
- Manual testing
- Exploratory testing
- Regression testing
- Integration testing
- User acceptance testing

**Test Approach:**
- Black box testing
- Positive and negative testing
- Boundary value analysis
- Equivalence partitioning

## Test Environment
- Operating systems
- Browsers and versions
- Devices (mobile, tablet, desktop)
- Test data requirements
- Backend/API environments

## Entry Criteria
- [ ] Requirements documented
- [ ] Designs finalized
- [ ] Test environment ready
- [ ] Test data prepared
- [ ] Build deployed

## Exit Criteria
- [ ] All high-priority test cases executed
- [ ] 90%+ test case pass rate
- [ ] All critical bugs fixed
- [ ] No open high-severity bugs
- [ ] Regression suite passed

## Risk Assessment

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| [Risk 1] | H/M/L | H/M/L | [Mitigation] |

## Test Deliverables
- Test plan document
- Test cases
- Test execution reports
- Bug reports
- Test summary report
```

</details>

<details>
<summary><strong>Deep Dive: Bug Reporting</strong></summary>

### Bug Report Template

```markdown
# BUG-[ID]: [Clear, specific title]

**Severity:** Critical | High | Medium | Low
**Priority:** P0 | P1 | P2 | P3
**Type:** Functional | UI | Performance | Security
**Status:** Open | In Progress | Fixed | Closed

## Environment
- **OS:** [Windows 11, macOS 14, etc.]
- **Browser:** [Chrome 120, Firefox 121, etc.]
- **Device:** [Desktop, iPhone 15, etc.]
- **Build:** [Version/commit]
- **URL:** [Page where bug occurs]

## Description
[Clear, concise description of the issue]

## Steps to Reproduce
1. [Specific step]
2. [Specific step]
3. [Specific step]

## Expected Behavior
[What should happen]

## Actual Behavior
[What actually happens]

## Visual Evidence
- Screenshot: [attached]
- Video: [link if applicable]
- Console errors: [paste errors]

## Impact
- **User Impact:** [How many users affected]
- **Frequency:** [Always, Sometimes, Rarely]
- **Workaround:** [If one exists]

## Additional Context
- Related to: [Feature/ticket]
- Regression: [Yes/No]
- Figma design: [Link if UI bug]
```

### Severity Definitions

| Level | Criteria | Examples |
|-------|----------|----------|
| **Critical (P0)** | System crash, data loss, security | Payment fails, login broken |
| **High (P1)** | Major feature broken, no workaround | Search not working |
| **Medium (P2)** | Feature partial, workaround exists | Filter missing one option |
| **Low (P3)** | Cosmetic, rare edge cases | Typo, minor alignment |

</details>

<details>
<summary><strong>Deep Dive: Figma MCP Integration</strong></summary>

### Design Validation Workflow

**Prerequisites:**
- Figma MCP server configured
- Access to Figma design files
- Figma URLs for components/pages

**Process:**

1. **Get Design Specs from Figma**
```
"Get the button specifications from Figma file [URL]"

Response includes:
- Dimensions (width, height)
- Colors (background, text, border)
- Typography (font, size, weight)
- Spacing (padding, margin)
- Border radius
- States (default, hover, active, disabled)
```

2. **Compare Implementation**
```
TC: Primary Button Visual Validation
1. Inspect primary button in browser dev tools
2. Compare against Figma specs:
   - Dimensions: 120x40px
   - Border-radius: 8px
   - Background color: #0066FF
   - Font: 16px Medium #FFFFFF
3. Document discrepancies
```

3. **Create Bug if Mismatch**
```
BUG: Primary button color doesn't match design
Severity: Medium
Expected (Figma): #0066FF
Actual (Implementation): #0052CC
Screenshot: [attached]
Figma link: [specific component]
```

### What to Validate

| Element | What to Check | Tool |
|---------|---------------|------|
| Colors | Hex values exact | Browser color picker |
| Spacing | Padding/margin px | DevTools computed styles |
| Typography | Font, size, weight | DevTools font panel |
| Layout | Width, height, position | DevTools box model |
| States | Hover, active, focus | Manual interaction |
| Responsive | Breakpoint behavior | DevTools device mode |

### Example Queries
```
"Get button specifications from Figma design [URL]"
"Compare navigation menu implementation against Figma design"
"Extract spacing values for dashboard layout from Figma"
"List all color tokens used in Figma design system"
```

</details>

<details>
<summary><strong>Deep Dive: Regression Testing</strong></summary>

### Suite Structure

| Suite Type | Duration | Frequency | Coverage |
|------------|----------|-----------|----------|
| Smoke | 15-30 min | Daily | Critical paths only |
| Targeted | 30-60 min | Per change | Affected areas |
| Full | 2-4 hours | Weekly/Release | Comprehensive |
| Sanity | 10-15 min | After hotfix | Quick validation |

### Building a Regression Suite

**Step 1: Identify Critical Paths**
- What can users NOT live without?
- What generates revenue?
- What handles sensitive data?
- What's used most frequently?

**Step 2: Prioritize Test Cases**

| Priority | Description | Must Run |
|----------|-------------|----------|
| P0 | Business-critical, security | Always |
| P1 | Major features, common flows | Weekly+ |
| P2 | Minor features, edge cases | Releases |

**Step 3: Execution Order**
1. Smoke first - if fails, stop and fix build
2. P0 tests next - must pass before proceeding
3. P1 then P2 - track all failures
4. Exploratory - find unexpected issues

### Pass/Fail Criteria

**PASS:**
- All P0 tests pass
- 90%+ P1 tests pass
- No critical bugs open

**FAIL (Block Release):**
- Any P0 test fails
- Critical bug discovered
- Security vulnerability
- Data loss scenario

**CONDITIONAL:**
- P1 failures with workarounds
- Known issues documented
- Fix plan in place

</details>

<details>
<summary><strong>Deep Dive: Test Execution Tracking</strong></summary>

### Test Run Report Template

```markdown
# Test Run: [Release Version]

**Date:** 2024-01-15
**Build:** v2.5.0-rc1
**Tester:** [Name]
**Environment:** Staging

## Summary
- Total Test Cases: 150
- Executed: 145
- Passed: 130
- Failed: 10
- Blocked: 5
- Not Run: 5
- Pass Rate: 90%

## Test Cases by Priority

| Priority | Total | Pass | Fail | Blocked |
|----------|-------|------|------|---------|
| P0 (Critical) | 25 | 23 | 2 | 0 |
| P1 (High) | 50 | 45 | 3 | 2 |
| P2 (Medium) | 50 | 45 | 3 | 2 |
| P3 (Low) | 25 | 17 | 2 | 1 |

## Critical Failures
- TC-045: Payment processing fails
  - Bug: BUG-234
  - Status: Open

## Blocked Tests
- TC-112: Dashboard widget (API endpoint down)

## Risks
- 2 critical bugs blocking release
- Payment integration needs attention

## Next Steps
- Retest after BUG-234 fix
- Complete remaining 5 test cases
- Run full regression before sign-off
```

### Coverage Tracking

```markdown
## Coverage Matrix

| Feature | Requirements | Test Cases | Status | Gaps |
|---------|--------------|------------|--------|------|
| Login | 8 | 12 | Complete | None |
| Checkout | 15 | 10 | Partial | Payment errors |
| Dashboard | 12 | 15 | Complete | None |
```

</details>

<details>
<summary><strong>QA Process Workflow</strong></summary>

### Phase 1: Planning
- [ ] Review requirements and designs
- [ ] Create test plan
- [ ] Identify test scenarios
- [ ] Estimate effort and timeline
- [ ] Set up test environment

### Phase 2: Test Design
- [ ] Write test cases
- [ ] Review test cases with team
- [ ] Prepare test data
- [ ] Build regression suite
- [ ] Get Figma design access

### Phase 3: Execution
- [ ] Execute test cases
- [ ] Log bugs with clear steps
- [ ] Validate against Figma (UI tests)
- [ ] Track test progress
- [ ] Communicate blockers

### Phase 4: Reporting
- [ ] Compile test results
- [ ] Analyze coverage
- [ ] Document risks
- [ ] Provide go/no-go recommendation
- [ ] Archive test artifacts

</details>

<details>
<summary><strong>Best Practices</strong></summary>

### Test Case Writing

**DO:**
- Be specific and unambiguous
- Include expected results for each step
- Test one thing per test case
- Use consistent naming conventions
- Keep test cases maintainable

**DON'T:**
- Assume knowledge
- Make test cases too long
- Skip preconditions
- Forget edge cases
- Leave expected results vague

### Bug Reporting

**DO:**
- Provide clear reproduction steps
- Include screenshots/videos
- Specify exact environment details
- Describe impact on users
- Link to Figma for UI bugs

**DON'T:**
- Report without reproduction steps
- Use vague descriptions
- Skip environment details
- Forget to assign priority
- Duplicate existing bugs

### Regression Testing

**DO:**
- Automate repetitive tests when possible
- Maintain regression suite regularly
- Prioritize critical paths
- Run smoke tests frequently
- Update suite after each release

**DON'T:**
- Skip regression before releases
- Let suite become outdated
- Test everything every time
- Ignore failed regression tests

</details>

---

## Examples

<details>
<summary><strong>Example: Login Flow Test Case</strong></summary>

```markdown
## TC-LOGIN-001: Valid User Login

**Priority:** P0 (Critical)
**Type:** Functional
**Estimated Time:** 2 minutes

### Objective
Verify users can successfully login with valid credentials

### Preconditions
- User account exists (test@example.com / Test123!)
- User is not already logged in
- Browser cookies cleared

### Test Steps
1. Navigate to https://app.example.com/login
   **Expected:** Login page displays with email and password fields

2. Enter email: test@example.com
   **Expected:** Email field accepts input

3. Enter password: Test123!
   **Expected:** Password field shows masked characters

4. Click "Login" button
   **Expected:**
   - Loading indicator appears
   - User redirected to /dashboard
   - Welcome message shown: "Welcome back, Test User"
   - Avatar/profile image displayed in header

### Post-conditions
- User session created
- Auth token stored
- Analytics event logged

### Edge Cases to Consider
- TC-LOGIN-002: Invalid password
- TC-LOGIN-003: Non-existent email
- TC-LOGIN-004: SQL injection attempt
- TC-LOGIN-005: Very long password
```

</details>

<details>
<summary><strong>Example: Responsive Design Test Case</strong></summary>

```markdown
## TC-UI-045: Mobile Navigation Menu

**Priority:** P1 (High)
**Type:** UI/Responsive
**Devices:** Mobile (iPhone, Android)

### Objective
Verify navigation menu works correctly on mobile devices

### Preconditions
- Access from mobile device or responsive mode
- Viewport width: 375px (iPhone SE) to 428px (iPhone Pro Max)

### Test Steps
1. Open homepage on mobile device
   **Expected:** Hamburger menu icon visible (top-right)

2. Tap hamburger icon
   **Expected:**
   - Menu slides in from right
   - Overlay appears over content
   - Close (X) button visible

3. Tap menu item
   **Expected:** Navigate to section, menu closes

4. Compare against Figma mobile design [link]
   **Expected:**
   - Menu width: 280px
   - Slide animation: 300ms ease-out
   - Overlay opacity: 0.5, color #000000
   - Font size: 16px, line-height 24px

### Breakpoints to Test
- 375px (iPhone SE)
- 390px (iPhone 14)
- 428px (iPhone 14 Pro Max)
- 360px (Galaxy S21)
```

</details>

---

**"Testing shows the presence, not the absence of bugs." - Edsger Dijkstra**

**"Quality is not an act, it is a habit." - Aristotle**


---


## 📚 Reference Materials


### Regression_Testing

# Regression Testing Guide

Comprehensive guide to regression testing strategies and execution.

---

## What is Regression Testing?

**Definition:** Re-testing existing functionality to ensure new changes haven't broken anything.

**When to run:**
- Before every release
- After bug fixes
- After new features
- After refactoring
- Weekly/nightly builds

---

## Regression Test Suite Structure

### 1. Smoke Test Suite (15-30 min)

**Purpose:** Quick sanity check

**When:** Daily, before detailed testing

**Coverage:**
- Critical user paths
- Core functionality
- System health checks
- Build stability

**Example Smoke Suite:**
```
SMOKE-001: User can login
SMOKE-002: User can navigate to main features
SMOKE-003: Critical API endpoints respond
SMOKE-004: Database connectivity works
SMOKE-005: User can complete primary action
SMOKE-006: User can logout
```

### 2. Full Regression Suite (2-4 hours)

**Purpose:** Comprehensive validation

**When:** Before releases, weekly

**Coverage:**
- All functional test cases
- Integration scenarios
- UI validation
- Data integrity
- Security checks

### 3. Targeted Regression (30-60 min)

**Purpose:** Test impacted areas

**When:** After specific changes

**Coverage:**
- Modified feature area
- Related components
- Integration points
- Dependent functionality

---

## Building a Regression Suite

### Step 1: Identify Critical Paths

**Questions:**
- What can users absolutely NOT live without?
- What generates revenue?
- What handles sensitive data?
- What's used most frequently?

**Example Critical Paths:**
- User authentication
- Payment processing
- Data submission
- Report generation
- Core business logic

### Step 2: Prioritize Test Cases

**P0 (Must Run):**
- Business-critical functionality
- Security-related tests
- Data integrity checks
- Revenue-impacting features

**P1 (Should Run):**
- Major features
- Common user flows
- Integration points
- Performance checks

**P2 (Nice to Run):**
- Minor features
- Edge cases
- UI polish
- Optional functionality

### Step 3: Group by Feature Area

```
Authentication & Authorization
├─ Login/Logout
├─ Password reset
├─ Session management
└─ Permissions

Payment Processing
├─ Checkout flow
├─ Payment methods
├─ Refunds
└─ Receipt generation

User Management
├─ Profile updates
├─ Preferences
├─ Account settings
└─ Data export
```

---

## Regression Suite Examples

### E-commerce Regression Suite

**Smoke Tests (20 min):**
1. Homepage loads
2. User can login
3. Product search works
4. Add to cart functions
5. Checkout accessible
6. Payment gateway responds

**Full Regression (3 hours):**

**User Account (30 min):**
- Registration
- Login/Logout
- Password reset
- Profile updates
- Address management

**Product Catalog (45 min):**
- Browse categories
- Search functionality
- Filters and sorting
- Product details
- Image zoom
- Reviews display

**Shopping Cart (30 min):**
- Add items
- Update quantities
- Remove items
- Apply discounts
- Save for later
- Cart persistence

**Checkout & Payment (45 min):**
- Guest checkout
- Registered user checkout
- Multiple addresses
- Payment methods
- Order confirmation
- Email notifications

**Order Management (30 min):**
- Order history
- Order tracking
- Cancellations
- Returns/Refunds
- Reorders

---

## Execution Strategy

### Test Execution Order

**1. Smoke first**
- If smoke fails → stop, fix build
- If smoke passes → proceed to full regression

**2. P0 tests next**
- Critical functionality
- Must pass before proceeding

**3. P1 then P2**
- Complete remaining tests
- Track failures

**4. Exploratory**
- Unscripted testing
- Find unexpected issues

### Pass/Fail Criteria

**PASS:**
- All P0 tests pass
- 90%+ P1 tests pass
- No critical bugs open
- Performance acceptable

**FAIL (Block Release):**
- Any P0 test fails
- Critical bug discovered
- Security vulnerability
- Data loss scenario

**CONDITIONAL PASS:**
- P1 failures with workarounds
- Known issues documented
- Fix plan in place

---

## Regression Test Management

### Test Suite Maintenance

**Monthly Review:**
- Remove obsolete tests
- Update changed functionality
- Add new critical paths
- Optimize slow tests

**After Each Release:**
- Update test data
- Fix broken tests
- Add regression for bugs found
- Document changes

### Automation Considerations

**Good Candidates for Automation:**
- Stable, repetitive tests
- Smoke tests
- API tests
- Data validation
- Cross-browser checks

**Keep Manual:**
- Exploratory testing
- Usability evaluation
- Visual design validation
- Complex user scenarios

---

## Regression Test Execution Report

```markdown
# Regression Test Report: Release 2.5.0

**Date:** 2024-01-15
**Build:** v2.5.0-rc1
**Tester:** QA Team
**Environment:** Staging

## Summary

| Suite | Total | Pass | Fail | Blocked | Pass Rate |
|-------|-------|------|------|---------|-----------|
| Smoke | 10 | 10 | 0 | 0 | 100% |
| P0 Critical | 25 | 23 | 2 | 0 | 92% |
| P1 High | 50 | 47 | 2 | 1 | 94% |
| P2 Medium | 40 | 38 | 1 | 1 | 95% |
| **TOTAL** | **125** | **118** | **5** | **2** | **94%** |

## Critical Failures (P0)

### BUG-234: Payment processing fails for Visa
- **Test:** TC-PAY-001
- **Impact:** High - Blocks 40% of transactions
- **Status:** In Progress
- **ETA:** 2024-01-16

### BUG-235: User session expires prematurely
- **Test:** TC-AUTH-045
- **Impact:** Medium - Users logged out unexpectedly
- **Status:** Under investigation

## Recommendation

**Status:** ⚠️ CONDITIONAL GO
- Fix BUG-234 (payment) before release
- BUG-235 acceptable with documented workaround
- Retest after fixes
- Final regression run before production deployment

## Risks

- Payment issue could impact revenue
- Session bug may frustrate users
- Limited time before release deadline

## Next Steps

1. Fix BUG-234 by EOD
2. Retest payment flow
3. Document session workaround
4. Final smoke test before release
```

---

## Common Pitfalls

**❌ Don't:**
- Run same tests without updating
- Skip regression "to save time"
- Ignore failures in low-priority tests
- Test only happy paths
- Forget to update test data
- Run regression once and forget

**✅ Do:**
- Maintain suite regularly
- Run regression consistently
- Investigate all failures
- Include edge cases
- Keep test data fresh
- Automate repetitive tests

---

## Regression Checklist

**Before Execution:**
- [ ] Test environment ready
- [ ] Build deployed
- [ ] Test data prepared
- [ ] Previous bugs verified fixed
- [ ] Test suite reviewed/updated

**During Execution:**
- [ ] Follow test execution order
- [ ] Document all failures
- [ ] Screenshot/record issues
- [ ] Note unexpected behavior
- [ ] Track blockers

**After Execution:**
- [ ] Compile results
- [ ] File new bugs
- [ ] Update test cases
- [ ] Report to stakeholders
- [ ] Archive artifacts

---

## Quick Reference

| Suite Type | Duration | Frequency | Coverage |
|------------|----------|-----------|----------|
| Smoke | 15-30 min | Daily | Critical paths |
| Targeted | 30-60 min | Per change | Affected areas |
| Full | 2-4 hours | Weekly/Release | Comprehensive |
| Sanity | 10-15 min | After hotfix | Quick validation |

**Remember:** Regression testing is insurance against breaking existing functionality.




### Test_Case_Templates

# Test Case Templates

Standard templates for creating consistent, comprehensive test cases.

---

## Standard Test Case Template

```markdown
## TC-[ID]: [Test Case Title]

**Priority:** P0 (Critical) | P1 (High) | P2 (Medium) | P3 (Low)
**Type:** Functional | UI | Integration | Regression | Performance | Security
**Status:** Not Run | Pass | Fail | Blocked | Skipped
**Estimated Time:** X minutes
**Created:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD

---

### Objective

[Clear statement of what this test validates and why it matters]

---

### Preconditions

- [ ] [Setup requirement 1]
- [ ] [Setup requirement 2]
- [ ] [Test data/accounts needed]
- [ ] [Environment configuration]

---

### Test Steps

1. **[Action to perform]**
   - Input: [specific data if any]
   - **Expected:** [What should happen]

2. **[Action to perform]**
   - Input: [specific data if any]
   - **Expected:** [What should happen]

3. **[Action to perform]**
   - Input: [specific data if any]
   - **Expected:** [What should happen]

---

### Test Data

| Field | Value | Notes |
|-------|-------|-------|
| [Field 1] | [Value] | [Any special handling] |
| [Field 2] | [Value] | [Any special handling] |

**Test Account:**
- Username: [test user]
- Password: [test password]
- Role: [user type]

---

### Post-conditions

- [System state after successful test]
- [Cleanup required]
- [Data to verify/restore]

---

### Edge Cases & Variations

| Variation | Input | Expected Result |
|-----------|-------|-----------------|
| Empty input | "" | Validation error shown |
| Max length | 256 chars | Accepted/Truncated |
| Special chars | @#$% | Handled correctly |

---

### Related Test Cases

- TC-XXX: [Related scenario]
- TC-YYY: [Prerequisite test]

---

### Execution History

| Date | Tester | Build | Result | Bug ID | Notes |
|------|--------|-------|--------|--------|-------|
| | | | | | |

---

### Notes

[Additional context, known issues, or considerations]
```

---

## Functional Test Case Template

For testing business logic and feature functionality.

```markdown
## TC-FUNC-[ID]: [Feature] - [Scenario]

**Priority:** P[0-3]
**Type:** Functional
**Module:** [Feature/Module name]
**Requirement:** REQ-XXX

### Objective
Verify that [feature] behaves correctly when [scenario]

### Preconditions
- User logged in as [role]
- [Feature prerequisite]
- Test data: [dataset]

### Test Steps

1. Navigate to [page/feature]
   **Expected:** [Page loads correctly]

2. Perform [action]
   **Input:** [test data]
   **Expected:** [System response]

3. Verify [result]
   **Expected:** [Success criteria]

### Boundary Tests
- Minimum value: [test]
- Maximum value: [test]
- Null/empty: [test]

### Negative Tests
- Invalid input: [test]
- Unauthorized access: [test]
- Missing required fields: [test]
```

---

## UI/Visual Test Case Template

For validating visual appearance and design compliance.

```markdown
## TC-UI-[ID]: [Component/Page] Visual Validation

**Priority:** P[0-3]
**Type:** UI/Visual
**Figma Design:** [URL]
**Breakpoints:** Desktop | Tablet | Mobile

### Objective
Verify [component] matches Figma design specifications

### Preconditions
- Browser: [Chrome/Firefox/Safari]
- Screen resolution: [specified]
- Theme: [Light/Dark]

### Visual Specifications

**Layout:**
| Property | Expected | Actual | Status |
|----------|----------|--------|--------|
| Width | XXXpx | | [ ] |
| Height | XXXpx | | [ ] |
| Padding | XX XX XX XX | | [ ] |
| Margin | XX XX XX XX | | [ ] |

**Typography:**
| Property | Expected | Actual | Status |
|----------|----------|--------|--------|
| Font | [Family] | | [ ] |
| Size | XXpx | | [ ] |
| Weight | XXX | | [ ] |
| Line-height | XXpx | | [ ] |
| Color | #XXXXXX | | [ ] |

**Colors:**
| Element | Expected | Actual | Status |
|---------|----------|--------|--------|
| Background | #XXXXXX | | [ ] |
| Border | #XXXXXX | | [ ] |
| Text | #XXXXXX | | [ ] |

**Interactive States:**
- [ ] Default state matches design
- [ ] Hover state matches design
- [ ] Active/pressed state matches design
- [ ] Focus state matches design
- [ ] Disabled state matches design

### Responsive Checks

**Desktop (1920px):**
- [ ] Layout correct
- [ ] All elements visible

**Tablet (768px):**
- [ ] Layout adapts correctly
- [ ] Touch targets adequate

**Mobile (375px):**
- [ ] Layout stacks correctly
- [ ] Content readable
- [ ] Navigation accessible
```

---

## Integration Test Case Template

For testing component interactions and data flow.

```markdown
## TC-INT-[ID]: [System A] to [System B] Integration

**Priority:** P[0-3]
**Type:** Integration
**Systems:** [List integrated systems]
**API Endpoint:** [endpoint if applicable]

### Objective
Verify data flows correctly from [source] to [destination]

### Preconditions
- [System A] running
- [System B] running
- Test credentials configured
- Network connectivity verified

### Test Steps

1. Trigger [action] in [System A]
   **Input:** [data payload]
   **Expected:** Request sent to [System B]

2. Verify [System B] receives data
   **Expected:**
   - Status code: 200
   - Response format: JSON
   - Data transformation correct

3. Verify [System A] handles response
   **Expected:** [UI update/confirmation]

### Data Validation
| Field | Source Value | Transformed Value | Status |
|-------|--------------|-------------------|--------|
| [field1] | [value] | [expected] | [ ] |
| [field2] | [value] | [expected] | [ ] |

### Error Scenarios
- [ ] Network timeout handling
- [ ] Invalid response handling
- [ ] Authentication failure handling
- [ ] Rate limiting handling
```

---

## Regression Test Case Template

For ensuring existing functionality remains intact.

```markdown
## TC-REG-[ID]: [Feature] Regression

**Priority:** P[0-3]
**Type:** Regression
**Original Feature:** [Feature name]
**Last Modified:** [Date]

### Objective
Verify [feature] still works correctly after recent changes

### Context
Recent changes that may affect this feature:
- [Change 1]
- [Change 2]

### Critical Path Tests

1. [ ] Core functionality works
2. [ ] Data persistence correct
3. [ ] UI renders properly
4. [ ] Error handling intact

### Integration Points
- [ ] [Dependent feature 1] still works
- [ ] [Dependent feature 2] still works
- [ ] API contracts unchanged

### Performance Baseline
- Expected load time: < Xs
- Expected response time: < Xms
```

---

## Security Test Case Template

For validating security controls and vulnerabilities.

```markdown
## TC-SEC-[ID]: [Security Control] Validation

**Priority:** P0 (Critical)
**Type:** Security
**OWASP Category:** [A01-A10]
**Risk Level:** Critical | High | Medium | Low

### Objective
Verify [security control] prevents [vulnerability/attack]

### Preconditions
- Test account with [role]
- Security testing tools configured
- Audit logging enabled

### Test Steps

1. Attempt [attack vector]
   **Input:** [malicious payload]
   **Expected:** Request blocked/sanitized

2. Verify security control response
   **Expected:**
   - Error message: Generic (no info leak)
   - Log entry: Attack attempt recorded
   - Account: Not compromised

### Attack Vectors
- [ ] SQL injection
- [ ] XSS (stored/reflected)
- [ ] CSRF
- [ ] Authentication bypass
- [ ] Authorization escalation

### Compliance Check
- [ ] [Regulation] requirement met
- [ ] Audit trail complete
- [ ] Data encrypted
```

---

## Performance Test Case Template

For validating speed, scalability, and resource usage.

```markdown
## TC-PERF-[ID]: [Feature] Performance

**Priority:** P[0-3]
**Type:** Performance
**Baseline:** [Previous metrics]

### Objective
Verify [feature] meets performance requirements

### Preconditions
- Load testing tool configured
- Baseline metrics recorded
- Test environment isolated

### Performance Criteria

| Metric | Target | Acceptable | Actual | Status |
|--------|--------|------------|--------|--------|
| Response time | < 200ms | < 500ms | | [ ] |
| Throughput | > 1000 req/s | > 500 req/s | | [ ] |
| Error rate | < 0.1% | < 1% | | [ ] |
| CPU usage | < 70% | < 85% | | [ ] |
| Memory usage | < 70% | < 85% | | [ ] |

### Load Scenarios

1. **Normal load:** X concurrent users
   - Duration: 5 minutes
   - Expected: All metrics within target

2. **Peak load:** Y concurrent users
   - Duration: 10 minutes
   - Expected: All metrics within acceptable

3. **Stress test:** Z concurrent users
   - Duration: Until failure
   - Expected: Graceful degradation

### Results
[Document actual results and comparison to baseline]
```

---

## Quick Reference: Test Case Naming

| Type | Prefix | Example |
|------|--------|---------|
| Functional | TC-FUNC- | TC-FUNC-001 |
| UI/Visual | TC-UI- | TC-UI-045 |
| Integration | TC-INT- | TC-INT-012 |
| Regression | TC-REG- | TC-REG-089 |
| Security | TC-SEC- | TC-SEC-005 |
| Performance | TC-PERF- | TC-PERF-023 |
| API | TC-API- | TC-API-067 |
| Smoke | SMOKE- | SMOKE-001 |

---

## Priority Definitions

| Priority | Description | When to Run |
|----------|-------------|-------------|
| P0 | Critical path, blocks release | Every build |
| P1 | Major features, high impact | Daily/Weekly |
| P2 | Standard features, moderate impact | Weekly/Release |
| P3 | Minor features, low impact | Release only |




### Figma_Validation

# Figma Design Validation with MCP

Guide for validating UI implementation against Figma designs using Figma MCP.

---

## Prerequisites

**Required:**
- Figma MCP server configured
- Access to Figma design files
- Figma URLs for components/pages

**Setup:**
```bash
# Install Figma MCP (follow official docs)
# Configure API token
# Verify access to design files
```

---

## Validation Workflow

### Step 1: Get Design Specifications

**Using Figma MCP:**
```
"Get the specifications for the primary button from Figma file at [URL]"

Response includes:
- Dimensions (width, height)
- Colors (background, text, border)
- Typography (font, size, weight)
- Spacing (padding, margin)
- Border radius
- States (default, hover, active, disabled)
```

### Step 2: Inspect Implementation

**Browser DevTools:**
1. Inspect element
2. Check computed styles
3. Verify dimensions
4. Compare colors (use color picker)
5. Check typography
6. Test interactive states

### Step 3: Document Discrepancies

**Create test case or bug:**
```
TC-UI-001: Primary Button Visual Validation

Design (Figma):
- Size: 120x40px
- Background: #0066FF
- Border-radius: 8px
- Font: 16px Medium #FFFFFF

Implementation:
- Size: 120x40px ✓
- Background: #0052CC ✗ (wrong shade)
- Border-radius: 8px ✓
- Font: 16px Regular #FFFFFF ✗ (wrong weight)

Status: FAIL
Bugs: BUG-234, BUG-235
```

---

## What to Validate

### Layout & Spacing
- [ ] Component dimensions
- [ ] Padding (all sides)
- [ ] Margins
- [ ] Grid alignment
- [ ] Responsive breakpoints
- [ ] Container max-width

**Example Query:**
```
"Extract spacing values for the card component from Figma"
```

### Typography
- [ ] Font family
- [ ] Font size
- [ ] Font weight
- [ ] Line height
- [ ] Letter spacing
- [ ] Text color
- [ ] Text alignment

**Example Query:**
```
"Get typography specifications for all heading levels from Figma design system"
```

### Colors
- [ ] Background colors
- [ ] Text colors
- [ ] Border colors
- [ ] Shadow colors
- [ ] Gradient values
- [ ] Opacity values

**Example Query:**
```
"List all color tokens used in the navigation component"
```

### Components
- [ ] Icon sizes and colors
- [ ] Button states
- [ ] Input field styling
- [ ] Checkbox/radio appearance
- [ ] Dropdown styling
- [ ] Card components

**Example Query:**
```
"Compare the implemented dropdown menu with Figma design at [URL]"
```

### Interactive States
- [ ] Default state
- [ ] Hover state
- [ ] Active/pressed state
- [ ] Focus state
- [ ] Disabled state
- [ ] Loading state
- [ ] Error state

---

## Common Discrepancies

### Typography Mismatches
- Wrong font weight (e.g., Regular instead of Medium)
- Incorrect font size
- Missing line-height
- Color hex codes off by a shade

### Spacing Issues
- Padding not matching
- Inconsistent margins
- Grid misalignment
- Component spacing varies

### Color Differences
- Hex values off (#0066FF vs #0052CC)
- Opacity not applied
- Gradient angles wrong
- Shadow colors incorrect

### Responsive Behavior
- Breakpoints don't match
- Mobile layout different
- Tablet view inconsistent
- Scaling not as designed

---

## Test Case Template

```markdown
## TC-UI-XXX: [Component] Visual Validation

**Figma Design:** [URL to specific component]

### Desktop (1920x1080)

**Layout:**
- [ ] Width: XXXpx
- [ ] Height: XXXpx
- [ ] Padding: XXpx XXpx XXpx XXpx
- [ ] Margin: XXpx

**Typography:**
- [ ] Font: [Family] [Weight]
- [ ] Size: XXpx
- [ ] Line-height: XXpx
- [ ] Color: #XXXXXX

**Colors:**
- [ ] Background: #XXXXXX
- [ ] Border: Xpx solid #XXXXXX
- [ ] Shadow: XXpx XXpx XXpx rgba(X,X,X,X)

**Interactive States:**
- [ ] Hover: [changes]
- [ ] Active: [changes]
- [ ] Focus: [changes]
- [ ] Disabled: [changes]

### Tablet (768px)
- [ ] [Responsive changes]

### Mobile (375px)
- [ ] [Responsive changes]

### Status
- [ ] PASS - All match
- [ ] FAIL - Discrepancies found
- [ ] BLOCKED - Design incomplete
```

---

## Figma MCP Queries

### Component Specifications
```
"Get complete specifications for the [component name] from Figma at [URL]"
"Extract all button variants from the design system"
"List typography styles defined in Figma"
```

### Color System
```
"Show me all color tokens in the Figma design system"
"What colors are used in the navigation bar design?"
"Get the exact hex values for primary, secondary, and accent colors"
```

### Spacing & Layout
```
"What are the padding values for the card component?"
"Extract grid specifications from the page layout"
"Get spacing tokens (8px, 16px, 24px, etc.)"
```

### Responsive Breakpoints
```
"What are the defined breakpoints in this Figma design?"
"Show mobile vs desktop layout differences for [component]"
```

---

## Bug Report for UI Discrepancies

```markdown
# BUG-XXX: [Component] doesn't match Figma design

**Severity:** Medium (UI)
**Type:** Visual

## Design vs Implementation

**Figma Design:** [URL]

**Expected (from Figma):**
- Button background: #0066FF
- Font weight: 600 (Semi-bold)
- Padding: 12px 24px

**Actual (in implementation):**
- Button background: #0052CC ❌
- Font weight: 400 (Regular) ❌
- Padding: 12px 24px ✓

## Screenshots

- Figma design: [attach]
- Current implementation: [attach]
- Side-by-side comparison: [attach]

## Impact

Users see inconsistent branding. Button appears less prominent than designed.
```

---

## Automation Ideas

### Visual Regression Testing
- Capture screenshots
- Compare against Figma exports
- Highlight pixel differences
- capabilities: File operations, code editing, terminal access, searchPercy, Chromatic, BackstopJS

### Design Token Validation
- Extract Figma design tokens
- Compare with CSS variables
- Flag mismatches
- Automate with scripts

---

## Best Practices

**DO:**
- ✅ Always reference specific Figma URLs
- ✅ Test all component states
- ✅ Check responsive breakpoints
- ✅ Document exact values (not "close enough")
- ✅ Screenshot both design and implementation
- ✅ Test in multiple browsers

**DON'T:**
- ❌ Assume "it looks right"
- ❌ Skip hover/active states
- ❌ Ignore small color differences
- ❌ Test only on one screen size
- ❌ Forget to check typography
- ❌ Miss spacing issues

---

## Checklist for UI Test Cases

Per component:
- [ ] Figma URL documented
- [ ] Desktop layout validated
- [ ] Mobile/tablet responsive checked
- [ ] All interactive states tested
- [ ] Colors match exactly (use color picker)
- [ ] Typography specifications correct
- [ ] Spacing (padding/margins) accurate
- [ ] Icons match design
- [ ] Shadows/borders match
- [ ] Animations match timing/easing

---

## Quick Reference

| Element | What to Check | Tool |
|---------|---------------|------|
| Colors | Hex values exact | Browser color picker |
| Spacing | Padding/margin px | DevTools computed styles |
| Typography | Font, size, weight | DevTools font panel |
| Layout | Width, height, position | DevTools box model |
| States | Hover, active, focus | Manual interaction |
| Responsive | Breakpoint behavior | DevTools device mode |

---

**Remember:** Pixel-perfect implementation builds user trust and brand consistency.




### Bug_Report_Templates

# Bug Report Templates

Standard templates for clear, reproducible, actionable bug documentation.

---

## Standard Bug Report Template

```markdown
# BUG-[ID]: [Clear, Specific Title]

**Severity:** Critical | High | Medium | Low
**Priority:** P0 | P1 | P2 | P3
**Type:** Functional | UI | Performance | Security | Data | Crash
**Status:** Open | In Progress | In Review | Fixed | Verified | Closed
**Assignee:** [Developer name]
**Reporter:** [Your name]
**Reported Date:** YYYY-MM-DD

---

## Environment

| Property | Value |
|----------|-------|
| **OS** | [Windows 11 / macOS 14 / Ubuntu 22.04] |
| **Browser** | [Chrome 120 / Firefox 121 / Safari 17] |
| **Device** | [Desktop / iPhone 15 / Samsung S23] |
| **Build/Version** | [v2.5.0 / commit abc123] |
| **Environment** | [Production / Staging / Dev] |
| **URL** | [Exact page URL] |

---

## Description

[2-3 sentences clearly describing what the bug is and its impact]

---

## Steps to Reproduce

**Preconditions:**
- [Any setup required before reproduction]
- [Test account: user@test.com]

**Steps:**
1. [Navigate to specific URL]
2. [Perform specific action]
3. [Enter specific data: "example"]
4. [Click specific button]
5. [Observe the issue]

**Reproduction Rate:** [Always / 8 out of 10 times / Intermittent]

---

## Expected Behavior

[Clearly describe what SHOULD happen]

---

## Actual Behavior

[Clearly describe what ACTUALLY happens]

---

## Visual Evidence

**Screenshots:**
- [ ] Before state: [attached]
- [ ] After state: [attached]
- [ ] Error message: [attached]

**Video Recording:** [Link if available]

**Console Errors:**
```
[Paste any console errors here]
```

**Network Errors:**
```
[Paste any failed requests here]
```

---

## Impact Assessment

| Aspect | Details |
|--------|---------|
| **Users Affected** | [All users / Subset / Specific role] |
| **Frequency** | [Every time / Sometimes / Rarely] |
| **Data Impact** | [Data loss / Corruption / None] |
| **Business Impact** | [Revenue loss / User frustration / Minimal] |
| **Workaround** | [Describe workaround if exists, or "None"] |

---

## Additional Context

**Related Items:**
- Feature: [FEAT-123]
- Test Case: [TC-456]
- Similar Bug: [BUG-789]
- Figma Design: [URL if UI bug]

**Regression Information:**
- Is this a regression? [Yes / No]
- Last working version: [v2.4.0]
- First broken version: [v2.5.0]

**Notes:**
[Any additional context that might help fix the issue]

---

## Developer Section (To Be Filled)

### Root Cause
[Developer fills in after investigation]

### Fix Description
[Developer describes the fix approach]

### Files Changed
- [file1.js]
- [file2.css]

### Fix PR
[Link to pull request]

---

## QA Verification

- [ ] Fix verified in dev environment
- [ ] Fix verified in staging
- [ ] Regression tests passed
- [ ] Related test cases updated
- [ ] Ready for production

**Verified By:** [QA name]
**Verification Date:** [Date]
**Verification Build:** [Build number]
```

---

## Quick Bug Report Template

For minor issues or fast documentation.

```markdown
# BUG-[ID]: [Title]

**Severity:** Low | Medium
**Environment:** [Browser, OS, Build]

## Issue
[One paragraph description]

## Steps
1. [Step 1]
2. [Step 2]
3. [Step 3]

## Expected
[What should happen]

## Actual
[What happens]

## Screenshot
[Attached]
```

---

## UI/Visual Bug Template

For design discrepancy reports.

```markdown
# BUG-[ID]: [Component] Visual Mismatch

**Severity:** Medium
**Type:** UI
**Figma Design:** [URL to specific component]

## Design vs Implementation

### Expected (Figma)
| Property | Value |
|----------|-------|
| Background | #0066FF |
| Font Size | 16px |
| Font Weight | 600 |
| Padding | 12px 24px |
| Border Radius | 8px |

### Actual (Implementation)
| Property | Expected | Actual | Match |
|----------|----------|--------|-------|
| Background | #0066FF | #0052CC | No |
| Font Size | 16px | 16px | Yes |
| Font Weight | 600 | 400 | No |
| Padding | 12px 24px | 12px 24px | Yes |
| Border Radius | 8px | 8px | Yes |

## Screenshots

**Figma Design:**
[Screenshot of Figma component]

**Current Implementation:**
[Screenshot of implemented component]

**Side-by-Side:**
[Comparison image]

## Impact
Users see inconsistent branding. Component appears different from approved design.
```

---

## Performance Bug Template

For speed, memory, or resource issues.

```markdown
# BUG-[ID]: [Feature] Performance Degradation

**Severity:** High
**Type:** Performance

## Performance Issue

**Affected Area:** [Page/Feature/API]
**User Impact:** [Page load slow / App unresponsive / High resource usage]

## Metrics

| Metric | Expected | Actual | Variance |
|--------|----------|--------|----------|
| Page Load Time | < 2s | 8s | +300% |
| API Response | < 200ms | 1500ms | +650% |
| Memory Usage | < 100MB | 450MB | +350% |
| CPU Usage | < 30% | 95% | +217% |

## Environment
- Data size: [Number of records]
- Network: [Connection type]
- Device specs: [RAM, CPU]

## Reproduction
1. [Load page with X records]
2. [Perform action]
3. [Observe slow response]

## Evidence
- Performance trace: [Link]
- Network waterfall: [Screenshot]
- Memory profile: [Screenshot]

## Baseline
- Previous version: [v2.4.0]
- Previous metric: [2s load time]
- Regression since: [v2.5.0]
```

---

## Security Bug Template

For vulnerabilities and security concerns.

```markdown
# BUG-[ID]: [Security Issue Title]

**Severity:** Critical
**Type:** Security
**OWASP Category:** [A01-A10]
**CONFIDENTIAL - DO NOT SHARE PUBLICLY**

## Vulnerability

**Type:** [XSS / SQL Injection / Auth Bypass / etc.]
**Risk Level:** Critical | High | Medium | Low
**Exploitability:** [Easy / Moderate / Difficult]

## Description
[Describe the vulnerability without providing exploit code]

## Impact
- [ ] Data exposure
- [ ] Privilege escalation
- [ ] Account takeover
- [ ] Service disruption
- [ ] Other: [specify]

**Affected Data:** [User PII / Payment info / etc.]

## Proof of Concept
[Describe how to verify the issue exists - sanitize any sensitive data]

## Recommended Fix
[High-level recommendation for remediation]

## References
- [CVE if applicable]
- [OWASP reference]

## Disclosure
- Internal report date: [Date]
- Expected fix date: [Date]
- Public disclosure: [N/A / Date if applicable]
```

---

## Crash/Error Bug Template

For application crashes and unhandled errors.

```markdown
# BUG-[ID]: [Crash/Error Description]

**Severity:** Critical
**Type:** Crash

## Error Details

**Error Type:** [Crash / Exception / Hang / White Screen]
**Error Message:**
```
[Exact error message]
```

**Stack Trace:**
```
[Full stack trace]
```

## Reproduction

**Frequency:** [Always / Intermittent]

1. [Step to trigger crash]
2. [Step 2]
3. [App crashes / shows error]

## Environment
- OS: [Version]
- App Version: [Build]
- Memory available: [If relevant]
- Device: [Model]

## Logs
```
[Relevant log entries before crash]
```

## Impact
- User data lost: [Yes/No]
- Session terminated: [Yes/No]
- Recovery possible: [Yes/No]
```

---

## Severity Definitions

| Level | Criteria | Response Time | Examples |
|-------|----------|---------------|----------|
| **Critical** | System down, data loss, security breach | < 4 hours | Login broken, payment fails, data exposed |
| **High** | Major feature broken, no workaround | < 24 hours | Search not working, checkout fails |
| **Medium** | Feature partial, workaround exists | < 1 week | Filter missing option, slow load |
| **Low** | Cosmetic, rare edge case | Next release | Typo, minor alignment, rare crash |

---

## Priority vs Severity Matrix

|  | Low Impact | Medium Impact | High Impact | Critical Impact |
|--|-----------|---------------|-------------|-----------------|
| **Rare** | P3 | P3 | P2 | P1 |
| **Sometimes** | P3 | P2 | P1 | P0 |
| **Often** | P2 | P1 | P0 | P0 |
| **Always** | P2 | P1 | P0 | P0 |

---

## Bug Title Best Practices

**Good Titles:**
- "[Login] Password reset email not sent for valid email addresses"
- "[Checkout] Cart total shows $0 when discount code applied twice"
- "[Dashboard] Page crashes when loading more than 1000 records"

**Bad Titles:**
- "Bug in login" (too vague)
- "It doesn't work" (no context)
- "Please fix ASAP!!!" (emotional, no information)
- "Minor issue" (unclear what the issue is)

---

## Bug Report Checklist

Before submitting:
- [ ] Title is specific and descriptive
- [ ] Steps can be reproduced by someone else
- [ ] Expected vs actual clearly stated
- [ ] Environment details complete
- [ ] Screenshots/evidence attached
- [ ] Severity/priority assigned
- [ ] Checked for duplicates
- [ ] Related items linked




---

## 🚀 Usage

**Reference this template:** `@skill-qa-test-planner.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
