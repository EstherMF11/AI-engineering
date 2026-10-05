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

## Context Engineering Takeaway

Context engineering is attention management.

The objective is not to show the model everything you know. The objective is to make the information needed for the next correct decision easy to find, trustworthy, and hard to confuse with noise.

---

## 11. Token Economics: What `/context` Is Measuring

The `/context` command shows how much of the available context window the current session occupies. That percentage is more than a capacity gauge: it helps explain the session's cost, speed, and reliability.

Imagine that every time a team meets, each participant receives the complete transcript of all previous meetings before answering the next question. The first meeting is cheap. By meeting eighty, even a one-sentence question arrives with a large reading assignment.

An agentic conversation works in a similar way. The model does not keep a private, permanent memory of earlier API calls. The client must provide the information it should remember again on the next call.

This section matters for different reasons:

- **Developers:** it explains why long sessions become slower and more expensive, and when compaction, clearing, or subagents help.
- **Product leaders and founders:** it explains why sending an entire repository or tool catalog has a recurring cost rather than a one-time cost.
- **Non-technical readers:** the essential idea is that retained conversation history must be processed again whenever the model responds.

---

## 12. What Is a Token?

A token is a small unit of text processed by a language model. In English, one token averages roughly four characters, although the real number varies by language, punctuation, code, and formatting.

Everything supplied to or generated by the model is measured in tokens:

- Your request.
- Persistent instructions.
- Files opened by the agent.
- Documentation retrieved from another system.
- Diffs and command output.
- Tool definitions.
- The model's answer.

There are two main categories:

- **Input tokens:** everything sent to the model so that it can produce the next response.
- **Output tokens:** the response generated by the model.

In many coding-agent sessions, input is the larger category because the model repeatedly receives project instructions, conversation history, code, and tool results. Output still matters, but shortening answers alone rarely addresses the main source of growth.

---

## 13. Why Long Conversations Compound

Suppose each turn adds the same amount of new information, represented by $t$ tokens. Without compaction, the model sees approximately:

$$
t + 2t + 3t + \cdots + nt
= t\frac{n(n+1)}{2}
$$

The exact bill depends on the provider, prompt caching, model pricing, and which messages the client retains. The pattern still matters: when history is repeatedly included, total input grows much faster than the final message alone suggests.

A useful approximation for one turn is:

$$
\mathrm{Input} \approx
\mathrm{Instructions}
+ \mathrm{ToolSchemas}
+ \mathrm{ConversationHistory}
+ \mathrm{RetrievedContent}
+ \mathrm{CurrentRequest}
$$

This explains a common surprise: a short question late in a large session may cost more and respond more slowly than a detailed question in a fresh session.

Prompt caching can reduce the price of repeated prefixes on supported platforms, but cached text still occupies context and can still contribute to distraction or context rot. Lower billing does not make irrelevant context useful.

Commands such as `/context` and `/cost` are therefore session instruments, not trivia:

- `/context` shows how full the working window is.
- `/cost`, where supported, shows accumulated usage or estimated spend.
- `/compact` replaces older detail with a summary.
- `/clear` starts again without carrying the active conversation forward.

The important lever is not making every sentence tiny. It is avoiding the repeated transport of information that no longer helps.

---

## 14. Three Token-Saving Myths

Many optimization tips contain a small truth but exaggerate its practical impact.

### Myth 1: Remove Every Comment

Unnecessary generated comments can make code noisy, so avoiding them is often good engineering hygiene. The token savings are usually minor compared with repeatedly loading large files, command output, or conversation history.

Use a "no unnecessary comments" rule to improve code quality, not as the center of a cost strategy.

### Myth 2: Always Use Extremely Short or "Caveman" Prompts

A compressed but ambiguous request often creates extra clarification and repair turns. Those additional turns carry the whole active context again.

A slightly longer prompt that states the outcome and success criteria can be cheaper overall because it improves first-pass acceptance.

### Myth 3: English Automatically Solves Token Cost

Tokenization efficiency differs between languages, and English may use fewer tokens for some content. That difference is usually small compared with architectural choices such as session length, file selection, tool schemas, and subagent isolation.

Use the language that communicates the task accurately unless measurement shows language choice is a real bottleneck.

When evaluating any saving tip, ask:

1. Does it reduce recurring **input**, or only a small amount of output?
2. Does the saving remain meaningful in a real coding task?
3. Does it preserve clarity and first-pass accuracy?
4. Is the evidence independent, or published by the tool's creator?

---

## 15. The Changes That Actually Matter

### Compact or Restart Long Sessions

Use `/compact` when the task should continue but the transcript contains detail that can be summarized. Use `/clear` or a fresh session when the next task does not need the current history.

A good continuation summary preserves:

- The goal.
- Decisions and their reasons.
- Files changed.
- Validation already completed.
- Known failures or constraints.
- The next action.

It does not preserve every exploratory dead end.

### Isolate Verbose Work in Subagents

Exploration, bug hunts, large stack traces, and broad reviews can consume substantial context. A subagent performs that work in a separate window and returns a focused conclusion.

The tokens are still consumed somewhere, but the main session does not repeatedly carry the entire exploration afterward. Isolation improves both attention and cumulative input usage.

### Stop Loading Files "Just in Case"

Every irrelevant file has three costs:

- It must be processed as input.
- It competes with useful evidence for attention.
- It may be sent again on later turns.

Let the agent discover files through targeted search unless you have a concrete reason to include them.

### Keep Tool Catalogs Focused

Many agent clients send a schema describing each enabled tool so the model knows how to call it. If an MCP server exposes forty tools, those definitions may become recurring input even when the task uses only two of them.

Prefer:

- Enabling only the MCP servers relevant to the task.
- Using progressive tool discovery when the client supports it.
- Exposing a smaller, task-specific tool set.
- Choosing a familiar CLI command when it provides the same result with less setup.

Vendor case studies have reported large reductions from pruning tool definitions, but exact percentages are environment-specific and often self-reported. The mechanism is more transferable than the headline number.

---

## 16. CLI vs. MCP Is a Task Decision

The rule is not "CLI is always better than MCP."

Use a CLI when:

- The model already knows the command well.
- The operation is simple and read-only.
- The output is concise and easy to interpret.
- Loading a large tool catalog would add unnecessary overhead.

For example, a direct command such as `gh pr diff` may be an efficient way to read a pull request diff.

Use MCP when:

- Authentication and structured access are easier through the server.
- The service has no suitable CLI.
- The operation needs a typed, constrained interface.
- The agent must interact with business systems such as tickets or databases.
- The MCP server retrieves a precise result that would otherwise require broad context.

The practical rule is:

> Do not pay a recurring context cost for a large tool catalog when a small, familiar interface solves the task just as well.

---

## 17. Cost, Speed, and Quality Are Connected

Context curation is not only a financial optimization.

### Cost

More recurring input generally means more billed usage. Pricing and caching differ by provider, but unused input never becomes free in an engineering sense.

### Speed

Larger inputs take time to transmit and process. Tool-heavy agents may also spend extra turns selecting among overlapping capabilities.

### Quality

More context introduces distractors, stale information, and competing instructions. As discussed earlier in the lesson, context rot can appear before the advertised window is full.

The same actions often improve all three dimensions:

- Keep the active session focused.
- Retrieve information only when needed.
- Summarize completed exploration.
- Remove unused tools.
- Start a new session when the task boundary changes.

---

## 18. Evaluate Token-Saving Tools Carefully

Skills, proxies, routers, and retrieval tools often promise dramatic savings. Some may be useful, but evaluate the claim separately from the mechanism.

Ask:

- Was the benchmark produced by an independent evaluator?
- Does the reported number measure input tokens, output tokens, or both?
- Was answer quality held constant?
- Does the comparison include tool schemas and retries?
- Does the technique still help on a real repository?
- What privacy or maintenance cost does the extra layer introduce?

A tool can promote a valuable behavior, such as reusing existing code or retrieving narrower context, even when its advertised percentage is not broadly reproducible.

Repository stars measure attention and adoption interest. They do not prove production quality, benchmark validity, or cost savings.

---

## 19. Common Questions About Token Usage

### Should prompts become telegraphic?

No. Optimize for fewer failed turns, not fewer useful words. A clear prompt with explicit success criteria usually costs less than an ambiguous prompt followed by several repair cycles.

### Does a one-million-token window solve the problem?

No. It increases capacity, but it does not remove input cost, processing time, or context rot. A larger room can hold more clutter; it does not organize the room.

### Should beginners track every token?

Usually not. Begin with two habits:

1. Compact or restart when a session becomes long or changes task.
2. Do not load files, documentation, or tools without a reason.

Those habits capture most of the practical benefit without turning token counting into a distraction.

### Are output limits irrelevant?

No. Excessively long responses still cost money and attention. They are simply not the dominant source of growth in many agentic coding sessions. Measure your actual workflow before optimizing.

---

## 20. Token Management Checklist

1. Use `/context` to notice when the active window is becoming crowded.
2. Compact, clear, or start a new session at meaningful task boundaries.
3. Keep prompts clear enough to avoid unnecessary correction turns.
4. Retrieve relevant files instead of preloading an entire repository.
5. Delegate context-heavy exploration to subagents.
6. Enable only the MCP servers and tools needed for the current task.
7. Prefer a simple CLI operation when it provides the same safe, structured result.
8. Judge saving claims by input reduction, maintained quality, and independent evidence.

## Final Takeaway

Every retained piece of context has to earn its place.

The goal is not to minimize tokens at any cost. It is to spend tokens on information that helps the model make the next correct decision. Focused sessions, selective retrieval, compaction, subagents, and lean tool catalogs produce the largest gains because they improve cost, speed, and quality at the same time.
