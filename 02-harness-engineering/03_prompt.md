# Prompt Engineering for Coding Agents

## The Main Idea

A technical prompt is not a spell. It is a work order.

A useful work order tells the agent:

- What outcome is required.
- How completion will be verified.
- Which boundaries must not be crossed.
- Where the relevant information can be found.
- What to do when something is unclear.

The exact wording matters less than whether the task is understandable and testable.

> A prompt without a way to verify success is only a request for something that looks plausible.

---

## 1. What Changed in Prompt Engineering?

Early prompt advice often focused on phrases such as:

- "Think step by step."
- "You are an expert software engineer."
- "Take a deep breath."

These phrases sometimes helped older models produce longer or more structured answers. Modern reasoning models can usually decide how much internal reasoning a task requires. Repeating generic thinking instructions may add noise without improving the result.

This does not mean prompts no longer matter. It means their useful purpose has become clearer:

- Do not try to control the model's private reasoning process.
- Clearly describe the result you need.
- Provide evidence the agent can use to check its work.
- State project-specific restrictions.
- Keep the request as short as the task allows.

The best prompt is not the most elaborate one. It is the smallest prompt that produces the desired outcome reliably.

---

## 2. Quick Glossary

- **Harness or scaffolding:** the tools, instructions, permissions, memory, and restrictions surrounding the model.
- **Context engineering:** deciding what information the model should have available at each point in the task.
- **Context rot:** the loss of reliability that can occur as the context grows and important information competes with noise.
- **`AGENTS.md`:** a persistent project instruction file containing conventions, commands, and common traps.
- **Subagent:** a separate agent with its own context window that performs a focused task and returns its conclusion.
- **Plan mode:** a read-only phase in which the agent investigates and proposes an approach before receiving permission to change anything.
- **Adaptive thinking:** the model adjusts how much reasoning it uses instead of following a fixed chain-of-thought instruction.
- **First-pass acceptance:** the proportion of generated work accepted without another correction cycle.
- **Zero-shot:** asking the model to perform a task without giving it examples first.
- **Few-shot:** including examples to demonstrate the expected pattern.

---

## 3. The Durable Parts of a Good Prompt

The useful parts of traditional prompt engineering are the same qualities found in a good engineering ticket.

### A Clear Outcome

Describe the result, not every keystroke.

Weak:

> Create login code.

Better:

> Implement login and registration in the FlowSync frontend using the existing authentication API.

The second version defines where the change belongs and what capability should exist afterward.

### Explicit Success Criteria

Success criteria turn an opinion into a check.

Examples:

- Registration sends a request to `POST /api/v1/auth/signup`.
- Login sends a request to `POST /api/v1/auth/login`.
- Successful login opens a protected page that loads `GET /api/v1/account/profile`.
- Invalid credentials display a useful message.
- An existing email produces a specific registration error.
- Existing tests still pass.

This is usually the highest-value part of the prompt. It gives the agent a finish line and gives the reviewer a shared basis for acceptance.

### Boundaries

State what the task must not change.

Examples:

- Do not modify the backend.
- Do not add a form library unless the current stack cannot meet the requirement.
- Preserve the public API.
- Do not replace existing design-system components.

Boundaries prevent an apparently successful solution from creating unnecessary work elsewhere.

### References

Point to useful sources instead of copying them into the prompt.

Examples:

- Follow the conventions in `AGENTS.md`.
- Reuse the existing shadcn/ui components.
- Inspect the backend validator before deciding which fields the form needs.
- Use the neighboring profile page as the routing pattern.

References keep the prompt focused while allowing the agent to retrieve detail when needed.

### A Clarification Rule

Tell the agent when it should stop and ask.

Example:

> If the ticket and backend validator disagree about required fields, ask before implementing the form.

This avoids silent assumptions at decisions where multiple valid solutions exist.

---

## 4. A Reusable Technical Prompt Template

```text
CONTEXT
You are working in the FlowSync frontend, which uses React, Tailwind,
and shadcn/ui. Follow the repository conventions in AGENTS.md.

OUTCOME
Implement login and registration using the existing authentication API.

SUCCESS CRITERIA
- Registration calls POST /api/v1/auth/signup.
- Login calls POST /api/v1/auth/login.
- Successful login redirects to a protected page that loads
  GET /api/v1/account/profile.
- Invalid credentials and duplicate email addresses display clear,
  specific messages.
- Relevant tests pass.

BOUNDARIES
- Do not change the backend.
- Do not add a form library without first explaining why it is needed.
- Reuse the existing UI components and routing patterns.

REFERENCES
- Project conventions: AGENTS.md
- Form fields: inspect the backend authentication validator
- UI components: existing shadcn/ui components in the frontend

CLARIFICATION
If a required behavior is not defined by the ticket, tests, validator,
or an existing pattern, ask before choosing one.
```

Not every task needs every heading. Use only the structure that removes meaningful ambiguity.

---

## 5. Why Success Criteria Matter Most

Compare these two requests:

> Implement the endpoint.

and:

> Implement the endpoint so that authorized requests return `200` with the expected payload, invalid input returns `422`, unauthenticated requests return `401`, and the integration tests pass.

The first request encourages a plausible implementation. The second provides observable proof.

A specification is essentially a durable, versioned prompt with explicit acceptance criteria. The difference is not the underlying idea, but its lifetime and level of formality.

---

## 6. Common Prompting Mistakes

### Vague Outcomes

"Build a dashboard" leaves almost every decision undefined. The agent must invent the users, data, layout, and acceptance criteria.

### Excessive Micromanagement

A long sequence of implementation steps may force the agent into an approach that does not fit the actual codebase. Specify necessary constraints, but allow the agent to use repository evidence.

### The Megaprompt

Combining extensive background, repeated conventions, examples, and multiple tasks consumes context and hides the main objective.

### No Definition of Done

Without tests or observable behavior, generated changes may look reasonable while failing continuous integration or missing the actual business need.

### Several Tasks in One Turn

Unrelated tasks create competing goals and make validation unclear. Split them into a sequence of focused requests.

### Repeating Persistent Instructions

Do not paste `AGENTS.md` into every prompt if the agent already loads it. Include only information unique to the current task.

### Asking for More Reasoning Instead of Better Evidence

"Think harder" is not a substitute for providing the failing test, relevant file, error output, or expected behavior.

---

## 7. Coding Workflows That Matter More Than Clever Wording

### Spec-Driven Development

Write the expected behavior and acceptance criteria before implementation begins. This changes the order of the work and removes ambiguity early.

### Plan, Then Execute

The agent first reads the code and proposes a plan without editing. A human reviews the assumptions and scope before allowing implementation.

Use this for changes that affect several files, layers, or public interfaces.

### Test First

Ask for a failing test that represents the desired behavior, then implement until the test passes. This creates an executable success criterion.

### Refactor with Anchors

For a large refactor:

1. Map the current design without changing it.
2. Agree on invariants that must remain true.
3. Approve the migration plan.
4. Apply small, reversible changes.
5. Validate after each step.

### Critic or Adversarial Review

Have another agent, or a separate session, attempt to disprove that the implementation is correct. Its job is to find broken assumptions, missing cases, and regressions rather than confirm the original work.

Independent approaches can outperform one very long reasoning attempt. Research on test-time scaling suggests that comparing several independent solutions may be more useful than simply asking one model to reason for longer.

---

## 8. The Three Pillars Work Together

Prompt quality cannot compensate for the wrong tool or poor context.

### Step 1: Characterize the Task

Ask:

- Does it affect one file or many?
- Must commands be executed?
- Is the codebase familiar?
- Can the work happen asynchronously?
- How much review time is available?

### Step 2: Choose the Tool

- Small inline change: use completion.
- Multi-file change with commands: use a terminal agent.
- Well-defined background work: consider a cloud agent.
- Focused review or security analysis: use a specialized tool.

### Step 3: Prepare the Context

Check:

- Is `AGENTS.md` current?
- Which files are clearly relevant?
- What should the agent discover through search?
- Should a subagent explore an unfamiliar area?
- Is the current context already too crowded?

### Step 4: Write the Prompt

Provide:

- Outcome.
- Success criteria.
- Boundaries.
- References.
- A clarification rule where necessary.

### Step 5: Execute and Verify

- Review the plan before approving broad changes.
- Run the relevant tests and checks.
- Compare the result with every success criterion.
- Use an independent review for risky work.

---

## 9. Diagnose the Weak Pillar

Different failures point to different causes:

- **The output ignores project conventions:** improve the context and `AGENTS.md`.
- **The code is valid but solves the wrong problem:** improve the prompt and success criteria.
- **The tool cannot inspect, edit, or validate the required scope:** choose a different tool category or improve the harness.
- **The agent forgets earlier decisions:** reduce, summarize, or isolate context.
- **The solution passes local checks but misses edge cases:** strengthen tests or add adversarial review.

Do not replace the tool automatically when a result is poor. First identify whether the bottleneck is the tool, context, prompt, or validation.

---

## 10. Different Roles, Same Principle

### For Developers

The most useful habit is writing the acceptance criteria before implementation. Role-playing instructions matter far less than a concrete definition of done.

### For Product and Management

A prompt is a written assignment. Objective, acceptance criteria, boundaries, and references are also the structure of a strong ticket for a human team.

### For Non-Technical Readers

Keep one rule:

> A task cannot be completed reliably if nobody can explain how to recognize a correct result.

That principle applies equally to people and agents.

---

## 11. Common Questions

### Is old prompt-engineering advice useless?

No. Reclassify it.

Magic phrases are unreliable. Clear structure, constraints, evidence, success criteria, and clarification rules remain valuable because they reduce ambiguity rather than manipulate the model.

### If prompting is less differentiating, why study it?

It is still necessary, and prompt mistakes are cheap to prevent. Rewriting a request takes a minute; discovering after implementation that the request described the wrong outcome costs much more.

### How much project context belongs in the prompt?

Only task-specific information. Do not repeat conventions already loaded from persistent instructions. Point to files and sources instead of copying their contents unless a specific excerpt is essential.

### Should every prompt include a role?

No. Add a role only when it introduces a real constraint or useful perspective, such as asking for an adversarial security review. Generic claims such as "you are an expert" rarely define useful behavior.

### Should examples be included?

Try zero-shot first. Add examples when the expected format or behavior remains ambiguous, not automatically.

---

## 12. Five Prompting Practices

1. Start with the desired outcome and explicit success criteria, not a list of implementation steps.
2. State important boundaries, such as preserving an API or avoiding new dependencies.
3. Tell the agent to ask before acting when a meaningful ambiguity cannot be resolved from project evidence.
4. Keep prompts for reasoning models direct and compact; split megaprompts into focused tasks.
5. Try zero-shot before adding examples that consume context and may overconstrain the solution.

## Final Takeaway

Modern prompt engineering is closer to specification writing than persuasive wording.

Choose the right tool, provide focused context, define the outcome, make success observable, state the boundaries, and verify the result. When those pieces are in place, the prompt can usually be short.
