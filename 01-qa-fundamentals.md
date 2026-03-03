# QA Fundamentals & Concepts

## Table of Contents
1. [Core QA Concepts](#core-qa-concepts)
2. [Testing Types](#testing-types)
3. [Bug Lifecycle](#bug-lifecycle)
4. [Testing Methodologies](#testing-methodologies)
5. [Common QA Interview Questions](#common-qa-interview-questions)

---

## Core QA Concepts

### What is Quality Assurance?

**Definition:** Quality Assurance (QA) is a process-driven approach to ensure that software products meet specified quality standards and customer requirements.

**Key Points:**
- Focuses on **process improvement** to prevent defects
- Ensures software reliability, performance, and usability
- Involves both **manual and automated testing**
- Part of the broader Software Development Life Cycle (SDLC)

**Difference between QA and Testing:**

| QA | Testing |
|---|---|
| Process-oriented | Product-oriented |
| Prevents defects | Finds defects |
| Focuses on process improvement | Focuses on test execution |
| Proactive approach | Reactive approach |

---

## Testing Types

### 1. **Smoke Testing**
- **Definition:** Initial testing to verify that basic functionality works
- **Purpose:** To catch critical defects early
- **When:** After build deployment
- **Example:** Testing login functionality, homepage load
- **Scope:** Limited to critical features

### 2. **Sanity Testing**
- **Definition:** Testing specific functionality after minor code changes
- **Purpose:** To verify that the changes work as expected
- **When:** After bug fixes or small updates
- **Example:** Testing search feature after pagination fix
- **Scope:** Focused on affected areas

### 3. **Regression Testing**
- **Definition:** Re-running existing test cases to ensure new changes don't break existing functionality
- **Purpose:** To maintain software stability
- **When:** After any code modification
- **Tools:** Automation frameworks (Selenium, TestNG)

### 4. **Functional Testing**
- Tests that features work as per requirements
- Validates inputs, outputs, and data processing
- Includes UI testing, database testing, API testing

### 5. **Non-Functional Testing**
- **Performance Testing:** Speed, response time, throughput
- **Security Testing:** Vulnerabilities, data protection
- **Load Testing:** System behavior under load
- **Usability Testing:** User experience

### 6. **User Acceptance Testing (UAT)**
- Done by end-users to verify business requirements
- Final validation before production release

---

## Bug Lifecycle

### Stages of a Bug

```
1. NEW → 2. ASSIGNED → 3. IN PROGRESS → 4. RESOLVED → 5. CLOSED/REOPENED
```

#### Detailed Flow:

**1. NEW**
- Tester identifies and logs the bug
- Bug is recorded in defect tracking system (JIRA, Bugzilla)

**2. ASSIGNED**
- Bug is reviewed by QA lead/manager
- Assigned to appropriate development team
- Severity and priority are set

**3. IN PROGRESS**
- Developer acknowledges the bug
- Starts working on fixing the issue

**4. RESOLVED**
- Developer fixes the bug and marks it as resolved
- Testing team validates the fix

**5. VERIFIED/CLOSED**
- Tester confirms the fix works
- Bug is closed
- If issue persists, bug moves back to IN PROGRESS

**6. REOPENED** (if applicable)
- If the bug reoccurs, it can be reopened

### Bug Severity vs Priority

| Aspect | Severity | Priority |
|--------|----------|----------|
| Definition | Impact on system | Urgency of fix |
| Levels | Critical, Major, Minor, Trivial | P1, P2, P3, P4 |
| Determined By | Tester | Project Manager/Team Lead |
| Example | P1: Login feature broken | Priority 1: Fix before release |

---

## Testing Methodologies

### 1. **Waterfall**
- Linear, sequential approach
- Each phase must be completed before next
- Testing happens after development
- Suitable for projects with fixed requirements

### 2. **Agile**
- Iterative, incremental approach
- Testing happens concurrently with development
- Short sprints (1-4 weeks)
- Continuous feedback and adaptation
- Suitable for projects with evolving requirements

### 3. **V-Model**
- Testing is aligned with development phases
- For each development phase, there's corresponding testing phase
- High defect detection rate

---

## Common QA Interview Questions

### Q1. What is the difference between Smoke and Sanity Testing?

**Answer:**

| Feature | Smoke Testing | Sanity Testing |
|---------|---------------|----------------|
| Definition | Initial validation of basic functionality | Quick validation after minor fixes |
| Scope | Broad, covers main features | Narrow, focused on specific areas |
| Depth | Shallow testing | Shallow to medium depth |
| When | After build deployment | After bug fixes or patches |
| Example | Testing login, homepage load | Testing search after pagination fix |
| Documentation | Formal, documented | Often informal |
| Time | 30 min - 2 hours | 15-30 minutes |

**Real Example:**
- **Smoke Test:** Deploy new build → Test login, homepage, navigation
- **Sanity Test:** Fix login button styling → Test login feature

---

### Q2. What is the difference between Regression and Retesting?

**Answer:**

| Feature | Regression | Retesting |
|---------|-----------|-----------|
| Definition | Testing to ensure new changes don't break existing features | Re-running failed tests after fix |
| Scope | Entire application | Only failed test cases |
| When | After any code modification | After bug fix |
| Purpose | Prevent unintended side effects | Verify bug fix works |
| Test Cases | Old test cases from repository | Same failed test case |
| Tools | Automation tools (Selenium) | Manual or automation |

**Example:**
- Bug: Login fails on Firefox
- **Retesting:** Run login test on Firefox after fix
- **Regression:** Run entire test suite to ensure nothing else broke

---

### Q3. Explain the Software Development Life Cycle (SDLC)

**Answer:**

The SDLC is a structured process that development and QA teams follow:

**Phases:**

1. **Planning & Requirements** (QA: Review requirements)
2. **Design** (QA: Create test plan)
3. **Development** (QA: Prepare test cases)
4. **Testing** (QA: Execute tests)
5. **Deployment** (QA: UAT, smoke testing)
6. **Maintenance** (QA: Monitor & retest)

**QA Involvement:**
- Understand requirements early
- Create comprehensive test plans
- Identify test scenarios
- Execute automated & manual tests
- Report defects
- Verify fixes
- Ensure quality before release

---

### Q4. What are Equivalence Partitioning and Boundary Value Analysis?

**Answer:**

#### **Equivalence Partitioning (EP)**
Dividing input data into groups (partitions) where all data in a group behaves similarly.

**Example:** Age field (0-150 years)
- Partition 1: Invalid (< 0) → Expected: Error
- Partition 2: Valid (0-150) → Expected: Accepted
- Partition 3: Invalid (> 150) → Expected: Error

Test Cases:
- Age = -5 (from invalid partition)
- Age = 25 (from valid partition)
- Age = 200 (from invalid partition)

#### **Boundary Value Analysis (BVA)**
Testing at the boundaries of input domains.

**Example:** Age field (0-150 years)
- Test: -1, 0, 1 (lower boundary)
- Test: 149, 150, 151 (upper boundary)

---

### Q5. What is Test Plan and its Key Components?

**Answer:**

**Definition:** A test plan is a document that outlines the testing strategy, scope, resources, and schedule for a project.

**Key Components:**

1. **Test Objectives**
   - What we're testing
   - Why we're testing

2. **Scope**
   - What features to test
   - What features NOT to test (out of scope)

3. **Test Strategy**
   - Approach: Manual, Automation, or both
   - Testing types: Functional, non-functional, etc.

4. **Test Cases**
   - Detailed test scenarios
   - Expected results

5. **Resources & Tools**
   - Team members required
   - Tools needed (JIRA, Selenium, etc.)

6. **Schedule**
   - Timeline for testing phases
   - Milestones and deadlines

7. **Risk & Mitigation**
   - Potential risks
   - Mitigation strategies

8. **Entry & Exit Criteria**
   - Conditions to start testing
   - Conditions to complete testing

---

### Q6. What is the Bug Lifecycle with Example?

**Answer:**

**Scenario:** E-commerce login feature bug

```
STEP 1: NEW
- Tester discovers: "Login button doesn't work on Chrome"
- Logs in JIRA with details:
  - Title: Login button unresponsive on Chrome
  - Steps to reproduce: Open app → Click login button → No response
  - Expected: Login form appears
  - Actual: Nothing happens
  - Severity: Critical (blocking main feature)
  - Priority: P1 (fix immediately)

STEP 2: ASSIGNED
- QA Lead reviews the bug
- Assigns to Frontend Developer (John)
- Status: ASSIGNED

STEP 3: IN PROGRESS
- John acknowledges the bug
- Investigates the code
- Status: IN PROGRESS

STEP 4: RESOLVED
- John fixes the issue (updates JavaScript event listener)
- Updates JIRA: "Fixed click handler in login component"
- Status: RESOLVED
- Moves to Testing for verification

STEP 5: VERIFICATION
- Tester (you) tests the fix
- Login button works on Chrome ✓
- Status: CLOSED/VERIFIED

OR

STEP 5B: REOPENED
- If tester finds login still doesn't work
- Comment: "Login still not working on Chrome"
- Status: REOPENED → Back to IN PROGRESS
```

---

### Q7. What is STLC (Software Testing Life Cycle)?

**Answer:**

**Definition:** STLC is a set of activities conducted during testing phase of SDLC.

**Phases:**

1. **Planning**
   - Define testing scope
   - Identify testing types
   - Resource planning

2. **Analysis**
   - Review requirements
   - Define test scenarios
   - Identify testable requirements

3. **Design**
   - Create test cases
   - Prepare test data
   - Design test environment

4. **Execution**
   - Execute test cases
   - Report defects
   - Document results

5. **Closure**
   - Prepare test summary reports
   - Lessons learned
   - Final approval

---

## Summary

✅ **QA ensures quality** through process improvement  
✅ **Different testing types** serve different purposes  
✅ **Bug lifecycle** is crucial for tracking and fixing issues  
✅ **Testing methodologies** vary by project type  
✅ **SDLC & STLC** structure the development and testing process  

---

**Next:** [Java for QA](02-java-qa.md)