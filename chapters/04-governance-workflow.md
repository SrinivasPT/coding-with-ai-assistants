# Chapter 4: Governance & Workflow — The Quality Gates

## 4.1 The Context-First Workflow

The fundamental goal of governance is to automate Context Suite verification before human review. This shifts the workflow from reactive debugging to proactive quality assurance.

### Analogy: The Pilot's Pre-Flight

A pilot doesn't just jump in and fly. They walk around the plane, checking flaps and tires. The Context-First Workflow is your "Walk Around." It costs 5 minutes but prevents the mid-air stall.

### The Daily Ritual: Context Setup

Making context review a routine part of each coding task improves both AI output quality and developer understanding:

1. **Review:** Check relevant sections of `ARCHITECTURE.mmd` and `.github/copilot-instructions.md`.  
2. **Focus:** Close irrelevant editor tabs to maintain clear context.  
3. **Frame:** Write a detailed comment at the top of your working file defining scope, constraints, and dependencies—this enriches the AI's context window with precise guidance.

## 4.2 Mechanical Guardrails: Pre-Commit and CI/CD

If AI generates code quickly, quality checks must be equally fast. Automated tooling serves as the primary defense against technical debt.

### Example: Catching Performance Issues Early

A team used Copilot to write a regex for email validation. The code passed unit tests (which Copilot also generated).

**The Issue:** The regex had catastrophic backtracking (ReDoS vulnerability).  
**The Impact:** An attacker sent a malformed string, consuming excessive CPU on the authentication service.  
**The Solution:** A pre-commit hook using a ReDoS scanner caught the issue before it reached code review.

### A. Pre-Commit Hooks (Immediate Developer Feedback)

These hooks run locally on every git commit to catch issues instantly:

**Secret Scanning:** (gitleaks, trufflehog)  
Catches hardcoded API keys and credentials that AI sometimes includes in generated configuration or boilerplate.

**Strict Typing & Linting:** (tsc --noEmit, ESLint)  
Enforces adherence to type safety rules (avoiding `any`) and maintains consistent code style.

### B. CI/CD Pipeline Checks (Architectural Validation)

These checks run on every Pull Request and focus on architectural integrity and code quality:

**System Integrity Check:** (madge, dependency-cruiser)  
Scans for forbidden cyclic dependencies and verifies compliance with architectural invariants defined in `ARCHITECTURE.mmd`.

**Technical Debt Analysis:** (SonarQube, static analysis)  
Flags PRs that introduce code with elevated defect density or excessive cyclomatic complexity.

**Security Scanning:** (SAST tools)  
Focuses on AI-generated code for common vulnerabilities like SQL injection or XSS.

## 4.3 The Cross-Domain Review Protocol

Effective code review for AI-generated code requires cross-functional perspective, with reviewers focused on system-wide impact rather than just syntax.

### The "Architectural Empathy" Review

The goal is to verify cross-boundary decisions, not just check syntax.

| Developer Submitting | Reviewer Focus | Question the Reviewer Must Ask |
| :---- | :---- | :---- |
| Frontend UI | Backend Logic & Data | "Does this N+1 query threaten our database performance?" |
| Backend API | Frontend Client & UX | "Is this payload size economical? Does the error structure ruin the user experience?" |

### The "Context Contributor" Role

During code review, when AI output reveals gaps in guidance, the reviewer's primary action is to update the Context Suite alongside fixing the code.

**Practice:** "If AI-generated code has issues due to missing context, update `copilot-instructions.md` first, then fix the code."

This approach ensures the AI learns from each review, transforming code review into continuous system improvement.

## 4.4 Implementation Roadmap

**Phase 1: Foundation (Week 1-2)**
1. Set up basic pre-commit hooks (secret scanning, linting)
2. Configure TypeScript strict mode or equivalent for your language
3. Establish the Context-First workflow ritual

**Phase 2: Automation (Week 3-4)**
1. Integrate CI/CD pipeline checks (dependency analysis, complexity metrics)
2. Configure security scanning tools
3. Document the cross-domain review protocol

**Phase 3: Optimization (Ongoing)**
1. Monitor metrics: What issues are caught at each stage?
2. Refine Context Suite based on recurring review comments
3. Adjust automation thresholds based on team capacity and code quality trends

**Integration with Development Workflows:**

**UI Development:**
- Pre-commit: Accessibility linting, component prop validation
- CI/CD: Visual regression tests, bundle size checks
- Review focus: State management patterns, performance implications

**API Development:**
- Pre-commit: OpenAPI schema validation, authentication checks
- CI/CD: Contract testing, API versioning compliance
- Review focus: Data structure efficiency, error handling consistency

**Database Development:**
- Pre-commit: Migration syntax validation, naming conventions
- CI/CD: Migration reversibility tests, query performance analysis
- Review focus: Index strategy, data integrity constraints

The governance framework adapts to your team's specific needs while maintaining consistent quality standards across all AI-assisted development.
