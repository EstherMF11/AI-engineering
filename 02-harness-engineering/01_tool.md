# Tool Engineering

## 1. The Five Basic Terms

- **MCP:** a universal connector that allows AI to use Jira, databases, and other services. The external system makes certain actions available, and the agent can execute them.
- **Progressive disclosure:** showing a summary first and loading the complete instructions only when they are needed. This prevents unused information from filling the context window.
- **Frontmatter:** configuration placed at the beginning of a Markdown file. It can specify information such as the file's name or description.
- **Symlink:** a file-system shortcut. Two different paths can point to the same content without maintaining two copies.
- **Self-reported:** a result measured and published by the company with an interest in it. It may be accurate, but it is not necessarily comparable with results obtained under different conditions.

---

## 2. The Task First, Then the Tool

The first question should not be:

> Should I use Claude Code, Cursor, Devin, or Copilot?

First, determine:

- Is the task small, or does it affect several files?
- Does it need to run commands?
- Should it work in the background?
- How much oversight do I want?
- What access can I grant it?

Once you have answered these questions, the right category of tool usually becomes clear.

It is like choosing a vehicle: first decide whether you need to transport one person across a city or tons of cargo; then choose a bicycle, car, or truck.

---

## 3. The Four Categories of Tools

### A. AI-Powered Editors

They work inside the IDE and offer suggestions while you program.

They are suitable for:

- Completing functions.
- Making small changes.
- Explaining nearby code.
- Maintaining constant oversight.

The developer retains a high degree of control and accepts or rejects each change.

### B. Terminal Agents

They can explore the repository, modify multiple files, run commands, and verify results.

They are suitable for:

- Refactoring.
- Features that span multiple layers.
- Migrations.
- Builds and tests.
- Technical planning.

Claude Code belongs to this category, which is the focus of the course.

### C. Asynchronous Cloud Agents

They receive a task and work remotely while you do something else. They usually return a change or a pull request proposal.

They are useful for clearly defined work that does not require continuous supervision.

**Devin** belongs to this category. **Devin Desktop**, by contrast, is a local editor. They share a company and a name, but not a way of working.

### D. Specialized Tools

They focus on a specific activity, such as reviewing code, detecting vulnerabilities, or analyzing pull requests.

They do not try to replace the entire development process. Instead, they aim to perform one part especially well.

A senior engineer can combine all four categories. There is no need to choose only one for every task.

---

## 4. Completion vs. Agentic

### Completion

The AI suggests a small amount of code, and you decide whether to accept it.

It is appropriate when:

- The change fits within one function.
- It affects one or two files.
- It does not need to run commands.
- You can easily validate the suggestion.

### Agentic

The AI investigates, plans, modifies files, runs commands, and verifies the result.

It is appropriate when:

- The change spans multiple layers.
- Many files need to be coordinated.
- Tests, builds, or migrations must be run.
- It needs to discover how the project works.

Using an agent to fix one line introduces unnecessary overhead. Using simple autocomplete for a cross-cutting feature can produce incompatible changes across files.

---

## 5. How a Senior Engineer Chooses a Tool

A senior engineer does not usually look only at which model achieved the highest score. Instead, they consider five factors:

1. **Codebase:** its size, age, structure, and complexity.
2. **Language:** some tools work better with particular ecosystems.
3. **Privacy:** where the code is sent and who can store it.
4. **Budget:** the cost of licenses, models, and execution.
5. **Working style:** how much control the developer wants to retain.

Privacy acts as a mandatory filter. If the code cannot leave a controlled environment, the options are limited to local, private, or VPC-deployed solutions.

The tool must also suit the person using it. A powerful autonomous agent offers little value if the developer needs to review every modification immediately.

---

## 6. Why Benchmarks Can Be Misleading

Two companies may claim scores of 87% and 85% using tests that appear identical. However, they may have used:

- Different tools.
- Different prompts.
- More context.
- Additional retries.
- Different validation methods.

The difference may therefore come from the **harness**, not the model.

For a fairer comparison, consult independent evaluations that use consistent conditions.

---

## 7. What the Claude Code Harness Is

The harness is everything that turns the model into an agent capable of working within a project.

It is not just Claude. It also includes:

- Persistent project information.
- Reusable procedures.
- Specialized agents.
- Automation.
- External connections.
- Planning, permissions, and context management.

---

## 8. Memory: `CLAUDE.md` and `AGENTS.md`

These files contain persistent instructions, such as:

- Test commands.
- The project's architecture.
- Coding conventions.
- Important restrictions.
- The definition of when a task is complete.

`AGENTS.md` is intended to work across different tools. Claude Code primarily uses `CLAUDE.md`, so the two files can be linked to maintain a single source of information.

Their purpose is to prevent the user from having to repeat the same rules in every conversation.

---

## 9. Skills

A skill is a reusable procedure.

For example, a skill for creating commits might specify:

1. Review the changes.
2. Run the tests.
3. Check that no secrets are present.
4. Write the message according to the project's convention.

The agent loads the skill's description first and consults the details only when it needs to use them. This is *progressive disclosure*.

---

## 10. Subagents

A subagent is a specialist to whom the main agent delegates part of the work.

It can have:

- Its own instructions.
- Separate context.
- Specific tools.
- Limited permissions.

For example, one agent implements a feature while another tries to find faults in it. Because they work with separate contexts, the reasoning used during implementation does not influence the review.

---

## 11. Hooks

Hooks are automatic commands that run when an event occurs.

For example:

- Formatting a file after it is edited.
- Running a linter before finishing.
- Blocking dangerous operations.
- Checking that the tests pass.

The idea is simple:

> If a rule can be enforced through automation, you should not rely on the model to remember it.

---

## 12. MCP

MCP allows Claude Code to interact with external systems.

For example, it can:

1. Retrieve a Jira ticket.
2. Read its requirements.
3. Modify the code.
4. Run the tests.
5. Link the change to the ticket.

The user does not need to copy all the information from Jira into the chat manually.

---

## 13. Plan Mode, Context, and Permissions

- **Plan mode:** the agent can investigate and propose a plan, but it cannot modify anything yet.
- **Compaction:** summarizes the conversation to preserve important information and free up context space.
- **Permissions:** determine which files, tools, and commands the agent can use.

Together, they provide human control: first you understand what the agent intends to do, and then you decide whether it can act.

---

## Final Idea

The lesson is not asking you to memorize brands. It is teaching you to think in this order:

1. Classify the task.
2. Decide how much autonomy it needs.
3. Choose the appropriate category of tool.
4. Provide the right context.
5. Automate verifiable rules.
6. Limit permissions.
7. Verify the result.

The model is only one component. The real quality depends on how it is connected, informed, controlled, and validated.

