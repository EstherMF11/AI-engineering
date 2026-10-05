# Harness Engineering

## 1. The Experiment That Changes Your Perspective

Claude Opus solves many more problems when it works in a well-prepared environment. The model has not changed; what changes is everything around it.

**Key idea:** a powerful AI can perform poorly if it does not have good tools, instructions, and context.

---

## 2. What Is the Harness?

The *harness* is the working environment prepared for the AI:

- The files it can access.
- The tools it can use.
- The rules it must follow.
- The information it receives.
- The ability to execute and verify changes.
- The memory available between tasks.

It is like hiring an excellent programmer: they will perform much better with access to the repository, documentation, terminal, and tests than by working only from a description sent through chat.

---

## 3. "Engineer the Harness, Not the Prompt"

This means you should not focus only on writing the perfect prompt.

Instead of repeating:

> "Do it better, think step by step, and do not make mistakes."

It is more effective to provide:

- Access to the relevant code.
- The project's conventions.
- Tools for searching and editing.
- Tests that define when the work is complete.
- Permission to run those tests.

The behavior improves because you have improved the working system, not because you have found magic words.

---

# The Three Pillars

## 4. Pillar 1: Tool

The tool is not just the model. It is the combination of the model and the environment in which it works.

It includes:

- Reading and editing files.
- A terminal.
- Repository search.
- MCP and external services.
- Subagents.
- Memory.
- Planning modes.
- Security rules.

That is why the same model can produce different results in Cursor, Claude Code, VS Code, or a custom application. Each environment allows it to observe and do different things.

**Diagnostic question:**
Does the AI actually have the ability to perform and verify the task?

---

## 5. Pillar 2: Context

Context is the information the AI has in mind when making a decision.

Having access to the entire repository does not mean the entire repository should be included in the conversation. Too much information can hide what matters.

The goal is to select:

- The file where the problem is located.
- The related functions.
- The relevant tests.
- The necessary conventions.
- The observed errors.

It is like investigating a malfunction: you need the manual and measurements related to that component, not every document produced by the company.

**Diagnostic question:**
Is the AI seeing the right information at the right time?

---

## 6. Pillar 3: Prompt

The prompt defines the specific work you want to accomplish.

A good prompt should clarify:

- What needs to change.
- What behavior is expected.
- What constraints exist.
- How the result will be verified.

For example, "improve this function" is ambiguous. In contrast:

> "Reduce duplicate queries without changing the public API, and verify the result with the existing tests."

This establishes both a task and a success criterion.

**Diagnostic question:**
Does the AI know exactly what result it needs to achieve?

---

## 7. Why the Three Pillars Are Co-Equal

No pillar can completely replace the others:

- A good tool with the wrong context makes the wrong decisions.
- Perfect context without tools only allows changes to be suggested.
- A precise prompt without access to the code forces the model to guess.
- A powerful model in a poor environment wastes its capabilities.

The final quality depends on the combination:

$$
\mathrm{Result} \approx \mathrm{Tool} \times \mathrm{Context} \times \mathrm{Prompt}
$$

Multiplication helps explain it: if one factor is very low, it harms the entire result.

---

## 8. Prompt, Context, and Harness Engineering

They are three related levels:

- **Prompt engineering:** improving the request.
- **Context engineering:** controlling the information the model receives.
- **Harness engineering:** designing the entire system in which the model works.

The harness contains the context and prompts, as well as tools, memory, permissions, and validation processes.

The progression is real, although presenting it as "three eras" is mainly a teaching device for explaining how the industry has evolved. The boundaries between the terms are not always exact.

---

## 9. The Meta-Message

When two developers use the same model, the difference in productivity may come from how they work with it.

The more effective developer usually:

- Prepares the environment better.
- Selects the context more carefully.
- Defines verifiable outcomes.
- Allows the AI to run tests.
- Preserves useful instructions in memory.

That is why learning only the commands of a specific tool has limited value. Understanding these principles allows you to transfer the method to other tools.

---

# Answers to the Questions

## 10. "So, Does It Matter Which Model I Use?"

Yes. A more capable model provides a better foundation.

However, choosing a good model does not guarantee good results. Once a sufficient level has been reached, improving its environment may produce more value than constantly switching models.

The model determines what it **could** do; the harness influences what it **actually achieves**.

---

## 11. "Aren't These Just Three Ways of Saying That You Need to Use It Well?"

The difference lies in the diagnosis:

- If it cannot run tests, the **tool** is failing.
- If it does not know a project convention, the **context** is failing.
- If it does not understand what "done" means, the **prompt** is failing.

Separating them allows you to correct the right cause.

---

## 12. "Why Trust Such Different Figures?"

A figure should not be interpreted as a permanent property of the model.

A result depends on:

- The benchmark.
- The available tools.
- The language used.
- The complexity of the repository.
- The evaluation method.
- The context provided.

The important thing is not to memorize "35 points," but to understand the pattern: **the same model can perform very differently when its working conditions change**.

---

## 13. Final Summary

- The model matters, but it does not work in isolation.
- The harness determines what it can observe, do, and verify.
- Context should be selected, not accumulated.
- The prompt should define the task and its success criteria.
- The three pillars must work together.
- This mental model is transferable between tools, even when their commands and names change.