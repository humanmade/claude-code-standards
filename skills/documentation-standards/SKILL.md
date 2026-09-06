---
name: documentation-standards
description: Human Made documentation standards for prose, instructions and code examples. Apply when writing or reviewing documentation, README files, Architecture Decision Records (ADRs), handbook pages, code comments, docblocks, pull request descriptions or commit messages.
---

# Human Made Documentation Standards

How to write clear, useful documentation. Applies to the Engineering Handbook, project documentation, Architecture Decision Records (ADRs) and more. Covers prose, instructions and code examples.

Documentation is written primarily for humans. These guidelines focus on making things clear and easy for people to understand and use. The same principles also help AI agents work effectively.

## Principles

1. **Write for the whole team.** Documentation is primarily for humans, and that includes everyone: engineers, delivery, product, client stakeholders and new starters. Use natural, plain English. Consider structure and readability. Avoid excessive business or technical jargon, and spell out all acronyms on first use.
2. **Be succinct.** Say what you mean clearly and directly. Delete filler, repetition and waffle.
3. **Lead with what matters.** State the outcome, rule or decision before explaining the background or implementation.
4. **Make context explicit.** State the scope, assumptions, prerequisites, dependencies and constraints that affect the reader. Do not rely on unwritten knowledge.
5. **Maintain one source of truth.** Keep each fact in one authoritative place and link to it elsewhere. Include a short summary only when the reader needs it to understand why the source matters.
6. **Make decisions visible.** State what was chosen and why. Include material constraints, trade-offs and rejected alternatives where they help the reader understand the decision.
7. **Make instructions executable.** For procedures, use numbered steps in the order they must be completed. Give each step one action. Use exact names, commands and locations, and identify placeholders clearly.
8. **Living, never finished.** Update documentation when the system or decision changes. Remove, archive or clearly mark content that is no longer valid.

## Code examples

Let the code carry the detail. Use the smallest example that demonstrates the point.

State whether an example is illustrative or ready to run. Identify placeholders and required context. Include expected output or a verification step when correctness would otherwise be unclear.

Add prose only when it explains something the code does not make apparent.

## Tone and voice

1. **Clear and direct.** Use plain English. Remove unnecessary words, filler and emphasis.
2. **Explain what is not obvious.** Assume the reader is capable but may lack context. Do not explain unrelated fundamentals.
3. **Professional and restrained.** Avoid promotional language, motivational asides, emoji and exclamation marks. Do not sound formal or distant.
4. **Precise and consistent.** Use established terminology and follow the glossary. Add a missing term to the glossary rather than inventing alternatives.
5. **Honest about trade-offs.** State relevant constraints, limitations and exceptions. Do not present a contextual decision as universally correct.
6. **Prefer active voice.** Name the person, role or system responsible when ownership matters. Use passive voice when the actor is unknown or irrelevant.

## Tense and person

- Use British English.
- Use second person ("you") when addressing the reader.
- Use present tense to describe how something works.
- Use the imperative for instructions: "Add the block", not "You should add the block".
- Use "we" only when it identifies a meaningful owner or collective decision.

## Anti-patterns

- Filler or emphasis words such as "simply", "just", "obviously" and "clearly"
- Narrating information already apparent from the code or heading
- Repeating information maintained elsewhere
- Relying on implied or unwritten context
- Using vague references such as "this", "it" or "the system" when the subject is unclear
- Introducing new terminology for an existing concept
- Using unexplained acronyms or placeholders
- Giving instructions without prerequisites or a verification step
- Hedging with "typically", "usually" or "in most cases" when no genuine variation exists
- Being so terse that the reader must infer important meaning
- Leaving obsolete instructions in place without a warning

## See also

- [Writing for the Handbook](https://engineering.hmn.md/writing-for-the-handbook/) — what belongs in the handbook and how pages are classified, structured, named and maintained
