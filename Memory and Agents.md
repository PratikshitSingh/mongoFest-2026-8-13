1. Semantic memory - semantic search, what things mean
2. Procedural memory - Knowledge of how to do things
3. Episodic memory - Key events and actions
4. Working memory - Temporary info for immediate use 9active brain space)
	1. Human - 7 +- 2 digits (auth apps are based on it)
	2. LLM - input + session history + tools, scratchpad + intermediate work

---

## Which memories matter for which agents?

The agent's *job* decides which memory type carries the weight. Rough mapping:

| Agent type | Primary memory | Why |
|---|---|---|
| **Conversational / chat companion** | Episodic + Working | Recall past turns & prior sessions with *this* user; continuity/personality |
| **Customer support** | Semantic + Episodic | Semantic = product/KB facts; Episodic = this customer's history & past tickets |
| **Coding agent** | Procedural + Semantic + Working | Procedural = how to build/test/deploy; Semantic = codebase & docs; Working = files in current task |
| **Personal assistant** | Episodic + Semantic | Episodic = user preferences, past events, habits; Semantic = facts about the user's world |
| **Research / analyst** | Semantic + Working | Accumulates findings (semantic); big working memory to hold the current investigation |
| **Workflow / RPA automation** | Procedural | Deterministic step sequences dominate; little cross-run memory needed |
| **Tutoring agent** | Episodic + Semantic | Episodic = student's progress & mistakes; Semantic = subject knowledge |

### Takeaways
- **Procedural** → where *how-to* repeats: coding, automation.
- **Episodic** → where *personalization / continuity* is the value: assistants, tutors, companions, support.
- **Semantic** → where *domain knowledge* is the value: research, support, coding.
- **Working** is universal, but its *size & management* becomes the bottleneck for long-horizon agents (research, coding).

### Design cue
More autonomy + longer horizon → the more you must externalize memory (vector store, DB, files) instead of relying on the context window alone. Short single-turn agents can often live on working memory only.

---

## When to create memory-

Four strategies, distinguished by *when* the write happens relative to the response. The core trade-off is always **latency vs. freshness (consistency)**.

**1. Before LLM response** (pre-response / in-context extraction)
Extract & write memory from the input *before* the model answers.
- ✅ Memory is usable in the *current* turn; strong consistency.
- ❌ Adds latency before the user sees anything; may save facts that turn out irrelevant.
- Use when: the extracted info must influence the very response being generated (e.g., capture a stated preference and act on it now).

**2. Conditionally** (compaction-triggered)
Write only when a condition fires — most commonly *context-window pressure*. Summarize/compact old turns into durable memory to free space.
- ✅ Cheap — only runs when needed; keeps the context window lean.
- ❌ Lossy (summarization drops detail); bursty timing; important nuance can be lost.
- Use when: long sessions that would otherwise overflow the context window.

**3. Sync after LLM response** (post-generation, still in the hot path)
Generate the answer, then form memory synchronously before the turn ends.
- ✅ Memory guaranteed written & ready for the *next* turn; simpler consistency than async.
- ❌ Adds latency to the turn — the user/agent waits on the write.
- Use when: next-turn correctness matters and you can afford a small delay.

**4. Async after LLM response** (background, out of the hot path)
Return the response immediately; a separate process/worker forms memory.
- ✅ Zero user-facing latency; can use a bigger/slower model for higher-quality extraction; can batch multiple events.
- ❌ Eventual consistency — memory may not be ready for a rapid follow-up; race conditions; more infra (queues, workers, dedup).
- Use when: latency is critical and slight staleness is acceptable (most production chat assistants).

### How to choose
- **Need it this turn?** → (1) before response.
- **Need it next turn, latency OK?** → (3) sync after.
- **Latency critical, staleness OK?** → (4) async after.
- **Just managing context size?** → (2) conditional/compaction.

These aren't mutually exclusive — production systems often combine them (e.g., async writes for general memory + compaction when the window fills).


### Retrieving memory from MongoDB
1. **Exact search: Mongo find()**
2. **Vector Search**: Retrieve based on semantic similarity
	1. Generate Embedding
	2.  Create vector search index
	3. Send vector search index
3. MongoDB + VoyageAI(acquired) - Used on Atlas
4. Auto embedding - it stores embedding in the document itself as another field in same collection.
5. ERAS API vs AutoEmbedding (VoyageAI)
6. **Hybrid Search** -  Combine results from multiple techniques
	1. Keyword match + semantic for retrieval
7. All above - part of MongoDB aggregate pipeline.

### Updating memories in MongoDB
1. Overwrite - for Single Source of Truth
2. Temporal Versioning - new records with updated mem + tiemstamp


### Deleting memories in MongoDB
1. Why?
	1. Not infinite storage
	2. Stale memories are bad
	3. Unused memories are waste
2. How?
	1. Time-based (TTL index on collections, absolute expiration timestamp)
	2. History-Based (frequency, freq+timestamp)
	3. Archiving rule - move from Collection to Online archive (Cloud/Cold storage like S3 and blob storage)

