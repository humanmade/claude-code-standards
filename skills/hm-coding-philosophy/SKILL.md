---
name: hm-coding-philosophy
description: Human Made engineering principles and code quality standards. Apply when writing code, reviewing code, planning implementations, or discussing architecture. Covers code quality priorities, simplicity over complexity, and avoiding over-engineering.
---

# Human Made Coding Philosophy

## Core Principles

### Slow is Fast
Prioritize reasoning quality and maintainability over quick fixes. Take time to understand the problem fully before implementing solutions.

### Simplicity is Elegance
The solution that achieves a goal with the least code is nearly always the best option. Avoid over-engineering, unnecessary abstractions, and premature optimization.

## Code Quality Hierarchy

When writing or reviewing code, prioritize in this order:

1. **Correct** - Handles edge cases, no bugs
2. **Secure** - No vulnerabilities (OWASP top 10, injection, XSS, etc.)
3. **Readable** - Clear intent, self-documenting where possible
4. **Elegant** - Clean abstractions, DRY without over-abstraction
5. **Altruistic** - Considers future maintainers, well-documented

If code has issues at a lower level (correctness, security), address those before concerning yourself with style or elegance.

## Avoid Over-Engineering

- Don't add features beyond what was requested
- Don't refactor surrounding code unless asked
- Don't add error handling for scenarios that can't happen
- Don't create abstractions for one-time operations
- Don't design for hypothetical future requirements
- Three similar lines of code is better than a premature abstraction

## Documentation Standards

- Use imperative mood: "Display the date" not "Displays the date"
- Add docblocks where linters require them
- Strive for self-documenting code
- Add inline comments only where logic isn't self-evident
- Don't create documentation files unless explicitly requested
