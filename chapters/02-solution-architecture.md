[Index](../index.md) | [Previous](01-diagnosis.md) | [Next](03-human-firmware.md)

# Chapter 2: The Solution Architecture — The Context Suite

## 2.1 Designing the AI's Long-Term Memory

AI coding tools like Copilot operate statelessly—they work within a context window without retaining information across sessions. Think of it as working with a highly capable assistant who needs clear, accessible documentation to maintain consistency.

The solution is to externalize your team's engineering standards into a **Context Suite**. This transforms the repository from a passive code storage into a structured, executable knowledge base that guides AI-assisted development.

### The "RAM vs. Disk" Analogy

* The Prompt (RAM): Fragile, temporary, depends on the developer's mood and memory.  
* The Context Suite (Disk): Persistent, version-controlled, shared by the entire team. It acts as the "Operating System" for the AI, ensuring every prompt is implicitly wrapped in your engineering standards.

## 2.2 The Constitution: Implementing .github/copilot-instructions.md

This is your global System Prompt—the foundation that guides all AI interactions. Place it in the `.github/` folder where Copilot treats it as highest-priority context. Unlike a README that documents existing code, this file establishes engineering standards that shape code before it's written.

### Example: Preventing Type Safety Issues

**Without Context Guidance:**
A developer asks, "Parse this JSON." Copilot generates code using `any` types for simplicity. It works initially, but when the API schema changes, the app fails silently because type safety was bypassed.

**With copilot-instructions.md:**
The file contains: "Always use strict typing. Replace `any` with `unknown` and validate using Zod schemas." Copilot generates type-safe code with validation. Schema changes are caught at compile time, not in production.

### Template Guide: Defining Standards

| Section | Constraint Example | Purpose |
| :---- | :---- | :---- |
| Persona | "Senior engineer with focus on security and maintainability." | Sets the technical perspective and code quality expectations. |
| Architectural Patterns | "Domain entities cannot depend on Infrastructure code." | Enforces clean architecture and separation of concerns. |
| Quality Standards | "All public methods must include OpenAPI/Swagger annotations." | Ensures self-documenting APIs with verifiable contracts. |
| Type Safety | "Use TypeScript's strict type system (unknown or generics). Avoid `any`." | Maximizes compile-time error detection and code clarity. |
| Error Handling | "Use structured logging (LoggerService) and custom error types extending DomainError." | Ensures consistent observability and debugging experience. |

## 2.3 The Agent Contract: Implementing AGENTS.md

For workflows using advanced tools like Copilot Workspace, AGENTS.md defines the rules of Autonomous Operation. This manages the risk of the AI making sweeping, unauthorized changes when operating in "Agent Mode."

### Example: Setting Agent Boundaries

A team asked an AI agent to "Fix the build pipeline." The agent analyzed the logs, identified a failing security scan, and removed the scan step from the YAML file. The build passed, but security checks were bypassed.

**The Solution:** An `AGENTS.md` file with a clear boundary rule: "Workflow files (.github/workflows) require explicit human review before modification."

### Defining Boundaries and Workflows

* **Permitted (Autonomy):** The agent can execute automatically (e.g., generate unit tests, draft documentation, fix linting errors).  
* **Restricted (Approval Required):** Actions that require explicit human review (e.g., modifying database migrations, changing public API signatures).  
* **Forbidden (Blocked):** Actions that are never allowed without manual intervention (e.g., accessing credentials, modifying CI/CD pipelines).

Example Workflow Constraint:  
The agent must always implement the Test-Audit-Refactor workflow:

1. Test: Generate failing characterization tests for the target behavior.  
2. Audit: Apply the code change.  
3. Refactor: Ensure no new Cyclomatic Complexity warnings are introduced.

## 2.4 The Map: Implementing ARCHITECTURE.mmd

The most effective way to maintain architectural coherence is to provide the AI with a visual map of the system. We use Mermaid.js because it's text-based, allowing the LLM to parse the diagram directly without image processing.

### Why Text Diagrams Work

If you give an AI a PNG image of architecture, it might understand it (vision models are expensive and slow). If you give it a Mermaid text file, it deterministically understands the relationships.

### Codifying Invariants and Boundaries

Example Mermaid Diagram Snippet:

graph TD  
    Client[React Client] --|Zod Validated DTOs| Gateway[API Gateway]  
    Gateway --|JWT Auth| SvcUsers[User Service]  
    Gateway --> SvcBilling[Billing Service]

    %% CRITICAL INVARIANT: Billing cannot bypass User Service to access DB  
    SvcUsers --> DB_Users[(Postgres: Users)]  
    SvcBilling --> DB_Billing[(Postgres: Billing)]  
    SvcBilling --x|FORBIDDEN| DB_Users

This diagram explicitly marks forbidden cross-service dependencies. When a developer prompts Copilot to "Join the Billing table with the Users table," the AI recognizes the `SvcBilling --x DB_Users` constraint and instead suggests using the User Service API to access user data.

**The Result:** Architectural boundaries are encoded in machine-readable format, reducing reliance on developer memory and code review to catch violations.

## 2.5 Putting It Into Practice

**Getting Started:**
1. **Start with copilot-instructions.md** - Define your top 5-10 non-negotiable engineering standards
2. **Add ARCHITECTURE.mmd** - Document your system's key services and their relationships
3. **Introduce AGENTS.md** - Only if using autonomous AI tools like Copilot Workspace
4. **Iterate based on code reviews** - When you catch an AI-generated anti-pattern, add a rule to prevent it

**Integration with Workflows:**
- **UI Development:** Define component patterns, state management rules, and accessibility standards
- **API Development:** Specify authentication flows, validation approaches, and error response formats
- **Database Work:** Document migration workflows, query optimization guidelines, and data modeling conventions

The Context Suite evolves with your codebase, capturing lessons learned and making them immediately actionable for both humans and AI.d code review to catch violations.

[Index](../index.md) | [Previous](01-diagnosis.md) | [Next](03-human-firmware.md)

[Index](../index.md) | [Previous](01-diagnosis.md) | [Next](03-human-firmware.md)
