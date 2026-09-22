# Design Guidance (discovery, approaches, presentation)

## Understanding the idea

- Check out the current project state first (files, docs, recent commits)
- Before asking detailed questions, assess scope: if the request describes multiple independent subsystems (e.g., "build a platform with chat, file storage, billing, and analytics"), flag this immediately. Don't spend questions refining details of a project that needs to be decomposed first.
- If the project is too large for a single spec, help the user decompose into sub-projects: what are the independent pieces, how do they relate, what order should they be built? Then brainstorm the first sub-project through the normal design flow. Each sub-project gets its own spec → plan → implementation cycle.
- Once scope is settled, establish actor coverage (see `actor-coverage.md`) — a compact statement or the full table — and resolve material uncertainty with the user before other detailed questions
- For appropriately-scoped projects, ask focused questions to refine the idea — choose the format that makes discovery efficient: a single question, a small thematic batch of related questions, or a concise numbered list
- Batch when the questions are related and independently answerable and batching avoids conversational delay; use a single follow-up when one answer determines what to ask next, or when a point is ambiguous or sensitive
- Keep questions open and concrete — never embed your preferred solution, and never restrict the user to agent-generated options. Multiple-choice may make a question easier to grasp, but it must not stop the user from giving another answer. Leave room for answers neither model anticipated
- Focus on understanding: purpose, constraints, success criteria

## Exploring approaches

- Propose 2-3 different approaches with trade-offs
- Present options conversationally with your recommendation and reasoning
- Lead with your recommended option and explain why
- YAGNI ruthlessly - remove unnecessary features from every approach and design

## Presenting the design

- Once you believe you understand what you're building, present the design
- Scale each section to its complexity: a few sentences if straightforward, up to 200-300 words if nuanced
- Ask after each section whether it looks right so far; these checks make the complete design a coherent review candidate, not yet the final approval
- Cover: architecture, components, data flow, error handling, testing
- Be ready to go back and clarify if something doesn't make sense

## Design for isolation and clarity

- Break the system into smaller units that each have one clear purpose, communicate through well-defined interfaces, and can be understood and tested independently
- For each unit, you should be able to answer: what does it do, how do you use it, and what does it depend on?
- Can someone understand what a unit does without reading its internals? Can you change the internals without breaking consumers? If not, the boundaries need work.
- Smaller, well-bounded units are also easier for you to work with - you reason better about code you can hold in context at once, and your edits are more reliable when files are focused. When a file grows large, that's often a signal that it's doing too much.

## Working in existing codebases

- Explore the current structure before proposing changes. Follow existing patterns.
- Where existing code has problems that affect the work (e.g., a file that's grown too large, unclear boundaries, tangled responsibilities), include targeted improvements as part of the design - the way a good developer improves code they're working in.
- Don't propose unrelated refactoring. Stay focused on what serves the current goal.
