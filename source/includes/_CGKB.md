# Contextual Graph Knowledge Base

We present *Contextual Graph Knowledge Base (CGKB)* as the TM memory implementation of *HTMI*. This shall be consistent with design outlines of *HTMI*

## Design

Recall that TM is equivalent to `{Fn, I}`. CGKB serves as the implementation for `I`. To also satisfy the approximation of human-human interaction, we have the following design outlines:

- **graph**: just as all enumerable data types, this supports TM completeness. Besides, its generality and features are established mathematically. `Fn` includes generic TM functions and graph operations on `I`, which is information encoded in graph nodes and edges that richly represent properties and relationships.

- **context**: the collective term for fundamental information types. To make TM operations tractable, contextualization restricts the scope to a manageable subgraph. The context filters are: privacy (public/private knowledge), entity, ranking, time, constraints, graph properties. 

- **ranking**: graph indexing analogous to how humans rank memories, for tractability. Ranking can use emotions (human analogy), LSTM (long-short term memories) for quick graph search.

- **learning**: consistent with the "inquiry" feature of HTMI, CGKB will extend itself by inquiring for missing information.


#### Terminologies

- **auto-planning**: the brain can build up and provide plans as causal graph automatically, such as in the simple case of getting a friend's phone number, or in the more complex case of playing chess. This utilizes all features of the brain enumerated above.
- **canonicalized input** `<fn, i_p>`: where `fn` is a TM function, and `i_p` the partial information needed for `fn`'s functional arguments. The output of the interface in HTMI on taking human input, for the TM to use. CGKB will attempt to extract the full information `i` required for `fn`.
- **causality**: The directed structure of the graph. Used in search, planning, dependencies, chronology, etc.
- **cognition**: graph operations as `Fn` on `I`, to mimic human cognition.
- **constraint**: e.g. existence, soundness.
- **function execution** `fn(i)`: Execute TM function `fn ∈ Fn` on information `i ∈ I` to yield output
- **graph**: the entire CGKB graph
- **norm**: preferred defaults to resolve ambiguities.
- **plan**: The causal graph of the necessary information in `I` to execute in `Fn`.
- **plan execution**: Extraction of information by traversing plan in reverse causal order from leaves to root, using the supplied `i_p` and contexts. Returns a subgraph for the extraction of `i`


## Formal Theory

#### Definitions

- `g`: The entire graph of CGKB
- `g_i`, `h`: placeholder for any subgraph of `g`
- `Contextualize`: the Contextualize algorithm
- `c_0`: the initial context, determined from `fn, i_p`
- `<fn, i_p>`: the canonicalized input from the interface of HTMI after parsing a human input, where `fn` is a TM function, and `i_p` the partial information needed for `fn`'s execution.
- `filter`: fields used to filter context, i.e. constrain the expansion of initial context in the `Contexualize` algorithm; thus far they are `{privacy, entity, ranking, time, constraints, graph properties}`
- `scope`: the lists of fulfilled and unfulfilled information, `scope_f, i_u` respectively for the extraction of `i` for `fn(i)`.
- `c`: contextual (knowledge) graph, or the contextualized subgraph, i.e. the output `Contextualize(g, c_0, i_p)`; `c ⊂ g`
- `i ∈ I`: the complete information needed to compute `fn(i)`. It is encoded within `g`; obviously there exists a smallest subgraph `h ⊂ g` that sufficiently encodes `i`. Equivalently `i` is the union of partial information `i_1, i_2, ...`
- `k`: knowledge, i.e. the complete information extractable from `g`. Note `i ⊂ k`, but `fn(i) = fn(k)` since the TM function `fn` only computes using the needed information.
- `k_h`: the information extractable from subgraph `h ⊂ g`. Note `k = k_g`.
- `Ex`: the extraction operator to extract knowledge from a graph, e.g. `k = Ex(g), k_h = Ex(h)`
- `-*->`: graph path, or 'derives'. We say `g_1 -*-> g_2` if `g_1` is connected to `g_2`, and `k_1 -*-> k_2` if `k_1` derives `k_2`.


#### Axioms

>1. Knowledge is encoded in a graph `g`, and decoded using the `Ex` operator.
2. Knowledge is deriverable, and this is reflected in its graph encoding by connectedness. Let `k_1 = Ex(g_1), k_2 = Ex(g_2)`, if `k_1 derives k_2`, i.e. `k_1 -*-> k_2`, then there must exists a corresponding path `g_1 -*-> g_2`, s.t. `(k_1 ∪ k_2)` is extractable from the connected component CC of `g_1`, i.e. `(k_1 ∪ k_2) ⊂ Ex(CC(g_1))`.
3. Base knowledge (graph sink) is the most basic knowledge, and is the source of the derivation path. If the path is cyclic, arbitrarily choose the last-encountered node as the basic knowledge. Base knowledge resolves all knowledge along the derivation path by gradual substitutions.


## Contextualize algorithm

Let there be given graph `g`, initial context `c_0`, partial information `i_p`.

**input**: `g, c_0, i_p`

**output**: contextual knowledge graph `c`

**enumerate**:

1. Initialize `scope` from `c_0, i_p`
2. while `scope` is not completely fulfilled, do:
  1. BFS expansion on `c_0` using the unfulfilled scope, `filters` and `i_p`, and
    1. if context is expanded, update scope, continue; 
    2. else, apply `learning` with `inquire` to expand `g` (but not context and scope); then retry on success or break on failure.
3. Return the context `c`, along with the fulfilled scope for direct access of `i`.


We also call the resulting context `c` the contextual knowledge graph, and the extractable knowledge `k_c = Ex(c)` the contextual knowledge, obtained by using `g, c_0, i_p, filters`. Note also `fn(i) = fn(k_c)`, thus the resulting contextual knowledge is sufficient for TM computation.

We prove below that this algorithm yields knowledge that is `g-bounded complete`; it also proves that the algorithm is correct.


## g-bounded Completeness Theorem

#### Facts

1. The initial context `c_0` can be disjoint (multiple graph components)
2. Thus the resulting context `c` can also be a graph with disjoint components, each containing at least a node from `c_0`.
3. The resulting context `c` is due to the provided `g, c_0, i_p, filters`.


#### Definition

Let there be given canonicalized input `<fn, i_p>` with TM function `fn`, partial information `i_p`, graph `g`, initial context `c_0`, and let context `c = Contextualize(g, c_0, i_p)`, and its extracted knowledge `k_c = Ex(c)`. Let `k_g = Ex(g)` be the complete knowledge extractable from `g`.

>We say `k_c` is `g-bounded complete` *iff* `fn(k_c) = fn(k_g)`.

#### Lemma

>`k_g` is `g-bounded complete`.

**Proof**: `fn(k_g) = fn(k_g)` by identity. □

#### Theorem

>`k_c` is `g-bounded complete`.

**Proof**: The initial context `c_0` is obtained from `<fn, i_p>`. The `scope` of the `Contextualize` algorithm is initialized with all the necessary information needed for the resulting context and `fn`. 

When the algorithm terminates with all the scopes fulfilled, by the axioms, we obtain in `c` all the necessary basic knowledge to resolve `c_0` entirely, thus yielding the necessary and sufficient `i` for `fn(i)`. The algorithm is correct.

The context `c` is thus the smallest subgraph in `g` that encodes the complete information `i` needed to compute `fn(i) = fn(k_c)`, thus extending the context any further, even to `g`, does not add to the already-complete `i`, since we know `fn(k_g) = fn(i)`. Thus, combining the two equalities, we get `fn(k_c) = fn(k_g)`. □



## CGKB algorithm

Let there be given the graph `g` of CGKB, canonicalized input `<fn, i_p>` from human input parsed by the interface of HTMI, where `fn` is a TM function and `i_p` the partial function for executing `fn`.

**input**: `g, fn, i_p`

**output**: TM output utilizing contextual knowledge `fn(i)`

**enumerate**:

1. Auto-planning: set `plan = Contextualize(g, fn, i_p)`.
2. Contextual knowledge extraction: set `c = Contextualize(g, plan, i_p)`.
3. Extract knowledge `k_c = Ex(c)` from `c` (and its `scope`), compute and return `fn(k_c) = fn(i)`.


`fn` is used as the initial context for extracting a `plan`. The `plan` is used for TM to automatically extract the contextual knowledge needed for computation. This is similar to AI planning, except the plan is already encoded in `g` when the CGKB learns, thus the planning is automatic.

The context `c` and its `scope` are extracted from the `plan` (or CGKB learns from the human otherwise). We then extract the contextual knowledge, `k_c`, containing information `i ⊂ k_c`, for computing and returning `fn(k_c) = fn(i)`.



**Proof**: To prove that the algorithm is correct, we must show that it is **Human-bounded Turing complete**, as outlined in [HTMI](./HTMI.md). If so, CGKB can be used by HTMI.

Recall again that TM is equivalent to `{Fn, I}`. We know that knowledge `k` or information `i ⊂ k` is encoded in the graph `g` of CGKB. For any `fn ∈ Fn`, we can obtain a sufficient context `c` such that `i ⊂ k_c = Ex(c)` for computation `fn(i)`. We show below.

The graph `g` of CGKB supports TM completeness, and thus without human restrictions, CGKB with TM is TM complete, as spanned by `Fn(I)`. 

In HTMI, `g` is used by queries from a human, which restrict its effective power. Note `g` is also built upon the TM's interactions with a human via `learning`: and thus `g` of CGKB is **Human-bounded Turing complete** inductively: at every step for context `c ⊂ g`, if CGKB can directly answer to a human, then it is already so; otherwise, it `learns` from the human and extends its `g` to be so, and it can answer the human with its new knowledge.

Finally, for each `fn ∈ Fn` and its corresponding context `c`, the contextual knowledge `k_c` is **g-bounded complete** by the theorem above, which implies `fn(k_c) = fn(k_g)`. So, the context `c` is always sufficient for emulating `fn(k_g)` for the **Human-bounded Turing complete** `g`. Therefore, CGKB is **Human-bounded Turing complete**. □



## Examples

We provide simple examples of CGKB below. The real possibilities are only limited by **Human-bounded Turing complete**, as proven above. In principle, one can use the brain for any AI tasks, such as playing chess, acting as a generic knowledge base, perform basic cognition, carry out basic functions. Note that in practice, implementation will need to cover more specific details.

#### when `g` has learned the knowledge

Given the graph `g` of CGKB that knows how to call a person,

- Human input: `"Call John."`
- HTMI interface canonicalize input to: `<fn = call, i_p = "John">`, where `"John"` is tagged as `proper noun` by a NLP POS Tagger.
- Algorithm `CGKB(g, fn = call, i_p = "John")`:
  1. Auto-planning: `plan = Contextualize(g, call, "John") = (call)-[requires]->(phone_number)-[requires]->(person)`
  2. Contextual knowledge extraction: `c = Contextualize(g, plan, "John") = (me)-[knows]->(John)`, where the node John contains his phone number. As stated in the `Contextualize` algorithm, the operation utilizes filters with graph ranking such as emotions, LSTM, time, contraints, or ambiguity resolution, to provide the result context graph `c`.
  3. Extract knowledge `k_c = Ex(c) = { name: "John", phone: "(234)-567-8900"}`, and execut the function `call("(234)-567-8900")`.


#### when `g` doesn't have the knowledge

Given the graph `g` of CGKB that **does not** know how to call a person,

- Human input: `"Call John."`
- HTMI interface canonicalize input to: `<fn = call, i_p = "John">`, where `"John"` is tagged as `proper noun` by a NLP POS Tagger.
- Algorithm `CGKB(g, fn = call, i_p = "John")`:
  1. Auto-planning: `plan = Contextualize(g, call, "John") = (call)-[requires]->(phone_number)`. The TM will call `learning` to inquire missing information from the human, update `g`, and recall the algorithm to yield `(call)-[requires]->(phone_number)-[requires]->(person)`. The rest follows as before.
  2. Contextual knowledge extraction: Suppose the human doesn't know any "John"; TM will attempt to learn from the human, update CGKB and continue with the task accordingly. This happens for any missing knowledge.


#### explain the "thought process"

Another powerful feature of CGKB is that a human can inquire about the TM's thought process, like "explain how do you call a person?". In this case, 

- `fn = explain_thought_process`, 
- `i_p = "how do you call a person?"`
- `plan = (explain_thought_process)-[require]-(requirements)...`
- `c = (call)-[requires]->(phone_number)`

And the TM may return `fn(k_c) = "(call)-[requires]->(phone_number)"` and the response.
