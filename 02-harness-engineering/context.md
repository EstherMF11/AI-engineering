# Context Engineering

## A Simpler Mental Model

Think of an AI model's context as a workbench, not a warehouse.

A workbench should contain the few tools and documents needed for the current job. If you cover it with the entire contents of the warehouse, everything technically remains available, but finding and using the right item becomes harder.

That is the central idea of context engineering:

> Give the model the most useful information for the current decision, not the largest possible amount of information.

---

## 1. Five Terms to Know

- **Context window:** everything available to the model during the current turn, including the conversation, files it has read, and persistent instructions. Its capacity is limited.
- **Agentic search:** the agent discovers information when needed by searching text, listing files, and opening relevant results instead of loading the whole project in advance.
- **RAG (retrieval-augmented generation):** documents are divided and indexed ahead of time so that relevant passages can be retrieved later. This works well for stable documentation, but an index can become outdated when code changes frequently.
- **LSP (Language Server Protocol):** the system editors use to understand code symbols and relationships. It can distinguish a specific function or class from unrelated text with the same name.
- **Compaction:** replacing a long conversation with a shorter summary and continuing from that summary. It recovers context space but loses some original detail.

---

## 2. A Large Context Window Is Capacity, Not a Target

A common assumption is that a one-million-token window should be filled with the repository, documentation, issue history, logs, and messages.

The problem is not only whether all that information fits. The model must also identify which parts matter, connect them correctly, and ignore thousands of distractions.

More context can therefore produce worse results because:

- Important facts compete with irrelevant details.
- Similar-looking information can lead the model down the wrong path.
- Key instructions may be buried in the middle.
- Reasoning across many unrelated facts becomes harder.
- Old information may conflict with the current state of the project.

A bigger window makes previously impossible tasks possible. It does not make indiscriminate context loading a good strategy.

---

## 3. Context Rot

*Context rot* describes the decline in model performance as the amount of input grows, even while the input remains within the model's advertised limit.

Research such as Chroma's *Context Rot: How Increasing Input Tokens Impacts LLM Performance* (2025) reports that tested models became less reliable as context increased. The decline depended on distractors, information placement, similarity between the question and the evidence, and the amount of reasoning required.

*Lost in the Middle* (Liu et al., 2024) identified a related effect: models often use information near the beginning and end of a prompt more reliably than information buried in the middle.

The practical conclusion is simple:

> Being inside the token limit does not guarantee that the model is using the context well.

### Course Heuristic, Not a Vendor Rule

The following thresholds are working guidelines rather than official limits:

- At roughly **50%**, pay attention to declining signal quality.
- At roughly **70%**, consider compaction or a fresh session.
- At roughly **90%**, expect repetition, lost decisions, and subtle inconsistencies.

The exact point varies by model and task. Context quality and structure matter more than a universal percentage.

---

## 4. What Belongs in Context?

Include information that changes the model's next decision:

- The exact task and success criteria.
- Relevant architecture and project conventions.
- Files directly involved in the behavior.
- Current errors, logs, or failing tests.
- Constraints that cannot be inferred from the code.
- Decisions already made during the task.

Usually leave out:

- Entire directories added "just in case."
- Documentation unrelated to the current task.
- Long command output when only the failure matters.
- Duplicate explanations of the same rule.
- Old plans that no longer describe the current approach.
- Information the agent can quickly discover itself.

A useful test is:

> Am I at least reasonably confident that this information will affect the next decision?

If not, let the agent search for it when needed.

---

## 5. A Small `AGENTS.md` Beats a Large Manual

`AGENTS.md` should contain durable, high-value project knowledge that a new contributor would need before making changes. It should not try to reproduce all project documentation.

A compact example for FlowSync might look like this:

```markdown
# AGENTS.md - FlowSync

## Overview
Monorepo with `backend/` (AdonisJS API) and `frontend/` (React + Vite).
Authentication uses access tokens. Data uses SQLite and Lucid ORM.

## Important conventions
- Create migrations; never edit the generated schema manually.
- Serialize API responses through transformers.
- Validate requests with VineJS validators, not inside controllers.
- Use Luxon `DateTime` for dates.
- Do not add dependencies without explaining the need in the PR.

## Commands
- `node ace migration:run` - apply migrations
- `npm run test` - run backend tests
- `npm run lint` - run linting and formatting checks

## Common traps
- The user profile must be returned through a transformer.
- Authentication is token-based, not session-based.
```

Good context files share five properties:

1. **Short and high-signal:** unusual rules matter more than obvious facts.
2. **Focused on essential commands:** link to full documentation instead of copying it.
3. **Explicit:** say what to do and what not to do.
4. **Current:** stale instructions are more dangerous than missing instructions.
5. **Limited to knowledge:** enforce deterministic rules with hooks or scripts instead of hoping the model remembers them.

For tools that use different instruction filenames, keep one source of truth. For example, Claude Code can reference `AGENTS.md` from `CLAUDE.md` rather than duplicating the same rules.

---

## 6. The Four Context Engineering Operations

### Write: Store Information Outside the Conversation

Move information that is not needed for the current turn into files:

- Session notes.
- Decisions.
- Intermediate results.
- Reusable project instructions.

The agent can load it later when it becomes relevant. Persistence does not require permanent presence in the context window.

### Select: Retrieve Only What Is Needed

In a large repository, let the agent search for the code related to the task. It can use file listing, text search, symbol search, and targeted reads.

This is often better than loading the whole repository or relying only on an old static index. The source code remains the current source of truth.

### Compress: Keep Decisions, Remove Transcript Detail

When the conversation becomes long, summarize:

- Current state.
- Decisions and their reasons.
- Files changed.
- Validation already performed.
- Remaining work.
- The next step.

Then compact the session or start a new one from that summary. The goal is to preserve working state without preserving every sentence.

### Isolate: Give Verbose Work Its Own Context

Delegate context-heavy work to a subagent with a separate context window. Examples include:

- Exploring an unfamiliar subsystem.
- Searching for a difficult bug.
- Reading a large stack trace.
- Performing an adversarial review.

The subagent returns only the findings needed by the main task. Ten thousand tokens of exploration might become a two-thousand-token conclusion.

---

## 7. Agentic Search, RAG, and LSP Solve Different Problems

These approaches are complementary:

| Approach | Best suited for | Main tradeoff |
| --- | --- | --- |
| Agentic search | Current source code and focused investigation | May require several tool calls |
| RAG | Large, relatively stable documentation collections | The index can become stale or retrieve fragments without enough structure |
| LSP-based search | Symbols, definitions, references, and code relationships | Depends on language-server support and a valid project setup |

Use agentic search when the repository itself is the freshest source. Use RAG when information is large and stable. Use LSP when the question depends on code structure rather than matching words.

---

## 8. MCP as a Context Filter

MCP servers connect agents to external tools and information sources. Their value in context engineering is selective retrieval.

Instead of pasting an entire ticket system, documentation site, or code index into the conversation, the agent asks for the exact item it needs:

- One Jira ticket.
- The current documentation for one API.
- The callers of one symbol.
- A specific database schema.

Examples mentioned in this area include Context7 for current library documentation and code-search systems such as Serena or CodeGraph. The product name is less important than the retrieval mechanism: bring back a precise answer instead of dumping a full information source into context.

Treat popularity and vendor benchmarks cautiously. Repository stars measure attention, not production quality, and claims such as reduced tool calls are often self-reported. Evaluate:

- Whether results are accurate and current.
- Whether the tool saves meaningful context or time.
- Whether it respects privacy requirements.
- Whether it works with the project's languages.
- Whether its maintenance cost is justified.

---

## 9. Common Questions

### If large windows degrade, why do vendors advertise them?

Capacity and effective use are different measurements. A large window allows the model to attempt tasks that would not fit otherwise, but irrelevant input can still reduce quality.

### Does every project need a context file?

Create one for projects you revisit. Keep it short. Include the surprising conventions, essential commands, and known traps that a new teammate would need on the first day.

### If the agent can search, why curate context at all?

Allowing targeted search is itself a form of curation. The danger usually comes from loading information in advance without knowing whether it matters, not from the agent retrieving evidence for a specific question.

### Is a perfect prompt more important than context?

Usually not. A moderately written request with the right evidence is often more useful than an elegant request surrounded by irrelevant or outdated information.

---

## 10. A Practical Context Engineering Kit

1. Create a short, high-signal `AGENTS.md` for every repository you regularly use. Keep it below roughly 200 lines and update it when conventions change.
2. Curate instead of accumulating. Add information because it affects the task, not because it might be useful someday.
3. Use subagents for exploration, bug searches, large logs, and other context-heavy work.
4. Compact or restart long agentic sessions before the model begins losing decisions. Turn counts such as 15-20 are reminders, not strict limits.
5. Treat context files like code: version them, review changes, and remove outdated instructions.

## Final Takeaway

Context engineering is attention management.

The objective is not to show the model everything you know. The objective is to make the information needed for the next correct decision easy to find, trustworthy, and hard to confuse with noise.
