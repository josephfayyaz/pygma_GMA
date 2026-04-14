# Yousef Readiness Master Plan for `pygma` Analysis

Date: 2026-04-14  
Source document: `/Users/youseffayyaz/Documents/GitHub/pygma_GMA/proposal.pdf`

## Purpose

This document translates the project proposal into an execution-ready plan for Yousef. Its goal is to help Yousef understand:

- what the project is really trying to build
- what General Morphological Analysis (GMA) means in practical terms
- what Yousef specifically owns
- what must be delivered in the `analysis/` module
- how to proceed under different technical and project scenarios
- what to prioritize so the work is publishable, testable, and useful to the rest of the team

## 1. Proposal in Plain Language

`pygma` aims to become the first open-source Python library for General Morphological Analysis (GMA). The proposal frames this as a major gap in the current software landscape: GMA is used in serious strategy, policy, and futures work, but the available tool support is proprietary and not Python-native.

The proposal is not just about building a library. It is also about:

- shipping a real Python package
- publishing a JOSS software paper
- creating a credible research and conference story
- making the project attractive to future contributors

The team is organized by module ownership:

- Ali owns `core/` and `io/`
- Sajjad owns `ai/`
- Yousef owns `analysis/`
- Parastoo owns `visualization/`

For Yousef, this means the role is not peripheral. The proposal presents `analysis/` as one of the core reasons the package is valuable. In particular, the phrase "agentic scenario search" is part of the proposal's differentiation strategy, so Yousef's work directly supports one of the library's headline claims.

## 2. The Core Concept You Must Understand

### 2.1 What GMA is

General Morphological Analysis is a structured way to explore complex problems that cannot be reduced to a single formula or optimization target.

The method starts by defining:

- a set of important parameters or dimensions of the problem
- a set of possible values for each parameter

Together, these form a **morphological field**.

Example:

- Technology: Solar, Wind, Nuclear, Hydro
- Ownership: Public, Private, Mixed
- Financing: Taxes, Bonds, Private Capital
- Time Horizon: Short, Medium, Long

The full solution space is the Cartesian product of those values. That gives all possible combinations.

### 2.2 What CCA does

Cross-Consistency Assessment (CCA) filters the raw solution space by marking which value pairs are compatible and which are not.

This is critical because the raw Cartesian product is too large and too unrealistic. CCA turns "all mathematically possible combinations" into "the combinations that remain logically or contextually feasible."

In practice, this means:

- the raw field is broad
- the CCA rules eliminate impossible or contradictory combinations
- the remaining configurations become the actual decision/scenario space

### 2.3 What `ConfigurationSpace` means for you

The proposal states that Ali owns the core engine, including `ConfigurationSpace`. That means your analysis work should not recreate the core representation of the feasible space. Your job is to build the layer that works **on top of** that feasible space.

For Yousef, the key mindset is:

- core builds the valid space
- analysis interprets the valid space
- analysis reduces, groups, ranks, and explores it

### 2.4 What scenario archetypes are

Once the space has been reduced by CCA, the remaining configurations can still be large. The proposal expects Yousef to help turn this large feasible set into interpretable scenario groups.

That is why clustering is in scope.

The goal is not clustering for its own sake. The goal is to answer:

- what kinds of scenarios exist in the feasible space?
- what are the typical patterns?
- what edge cases exist?
- what conditions drive the shape of the solution space?

### 2.5 What "agentic scenario search" should mean in v0.1

The proposal uses the phrase "agentic scenario search," but for v0.1 this should be interpreted conservatively and rigorously.

Recommended interpretation:

- deterministic and auditable search over feasible configurations
- guided exploration using explicit goals or driver constraints
- generation of representative, boundary, and robust scenarios
- notebook-first experimentation before public API hardening

Not recommended for v0.1:

- open-ended autonomous agents with vague behavior
- opaque LLM-driven scenario selection without auditability
- dependencies that make the analysis module impossible to test deterministically

## 3. What the Proposal Assigns to Yousef

The proposal explicitly assigns Yousef:

- ownership of `analysis/`
- responsibility for solution-space reduction
- responsibility for k-modes clustering
- responsibility for sensitivity analysis
- responsibility for agentic scenario exploration strategies

The proposal also gives Yousef a specific primary output:

- scenario analysis engine
- research notebook

The proposal's immediate action for Yousef is:

- read Ritchey (2006, 2011)
- design the agentic scenario exploration strategy on top of the `ConfigurationSpace` API

The proposal's timeline assigns Yousef's analysis-module delivery to:

- Phase 2
- Weeks 3-6

The proposal's team agreements also impose these expectations on Yousef:

- write tests for the module
- aim for at least 85% coverage in the analysis module
- participate in weekly syncs
- review incoming PRs affecting the owned module
- operate within GitHub issue labels and shared project decisions

## 4. What You Should Deliver

The best way to interpret the proposal is that Yousef should deliver five concrete assets.

### 4.1 A stable `analysis/` API

Recommended public surface:

- `reduce_field(config_space, *, fixed_conditions=None, excluded_conditions=None, return_metadata=True)`
- `cluster_configurations(config_space, *, n_clusters, random_state=0, init="Huang")`
- `SensitivityAnalyzer(config_space)`

### 4.2 A deterministic scenario-exploration workflow

This should begin as notebook-oriented workflow logic rather than a frozen public API.

The workflow should accept:

- explicit driver conditions
- target values or constraints
- a mode of exploration

The workflow should produce:

- representative scenarios
- boundary scenarios
- robust scenarios

### 4.3 A research notebook

The notebook should demonstrate:

- the raw field
- the reduced feasible space
- counts before and after filtering
- scenario clusters and archetypes
- sensitivity results
- a few exemplar scenarios with plain-language explanation

### 4.4 Tests

At minimum:

- reduction correctness tests
- clustering determinism tests
- clustering failure tests
- sensitivity ranking tests
- mixed-condition inference tests

### 4.5 Research notes for the paper

You should keep a lightweight notebook or Markdown log answering:

- what analysis features were implemented
- why those features matter methodologically
- how reproducibility and auditability were preserved
- what example case supports the JOSS narrative

## 5. Technical Interpretation of the `analysis/` Module

### 5.1 `reduce_field`

Purpose:

- narrow the valid configuration space using fixed or excluded conditions
- expose how much the space shrinks
- make filtering auditable and explainable

Minimum behavior:

- accept a valid `ConfigurationSpace`
- support fixing parameter values
- support excluding parameter values
- return a filtered subspace
- return counts before and after reduction
- return metadata showing what conditions caused reductions

Recommended output structure:

- filtered configurations
- original count
- reduced count
- reduction ratio
- applied filters
- elimination summary by parameter and value

### 5.2 `cluster_configurations`

Purpose:

- group feasible configurations into interpretable archetypes

Why k-modes:

- configurations are categorical, not numeric
- k-means is the wrong default because it assumes Euclidean numeric centroids
- k-modes fits the proposal's emphasis on categorical scenario structure

Recommended behavior:

- accept the feasible configurations from `ConfigurationSpace`
- encode each configuration as a categorical record keyed by parameter/value names
- run k-modes using a fixed seed
- return cluster assignments, cluster modes, and simple quality summaries

Recommended quality summaries:

- cluster sizes
- within-cluster mismatch counts
- distance of each member to its cluster mode
- representative member closest to each mode

### 5.3 `SensitivityAnalyzer`

Purpose:

- reveal which parameters, values, or constraints most influence the feasible space

Recommended v0.1 definition:

- deterministic impact analysis, not probabilistic uncertainty analysis

Recommended outputs:

- parameter impacts: how much the feasible count changes when a parameter is fixed or constrained
- value impacts: how much each specific value shrinks or preserves the space
- constraint impacts: which pairwise constraints or filters are most structurally important

Important caution:

Do not oversell "sensitivity" as if it were a statistical global sensitivity framework. In this project, sensitivity should mean structural influence inside the feasible configuration space.

### 5.4 Scenario exploration modes

Recommended exploration modes:

- `representative`: archetypal scenarios that best summarize each cluster
- `boundary`: edge scenarios that are maximally different from other feasible scenarios
- `robust`: scenarios that remain feasible under many driver selections or imposed conditions

This is where the project can honestly support the proposal's "agentic" claim without becoming methodologically weak.

## 6. Master Execution Plan

## Phase 0 - Immediate orientation

Objective:

- understand the proposal, the method, and the technical dependencies

Actions:

- read `proposal.pdf` carefully
- read the parts of Ritchey focused on inference models, linked fields, scenario fields, and auditability
- write a 1-page concept summary in your own words
- prepare 8-10 clarification questions for Ali about the `ConfigurationSpace` contract

Outputs:

- personal concept notes
- a glossary
- a dependency checklist for the analysis module

Done when:

- you can explain GMA, CCA, inference model, and scenario archetypes without looking at the paper

## Phase 1 - Interface alignment with core

Objective:

- lock the minimum interface you need from Ali's `ConfigurationSpace`

You should request or confirm:

- iteration over valid configurations
- access to parameter names and value names
- ability to apply fixed-condition filtering
- access to compatibility or provenance metadata if available
- stable serialization or adapters if notebook work starts early

Recommended contract to align on:

- list or iterator of feasible configurations
- parameter metadata
- filtering hooks
- count methods

Outputs:

- a short interface contract note
- one canonical example the team agrees to support first

Recommended canonical example:

- the energy-policy field referenced in the proposal

Done when:

- you can build the analysis layer without guessing core behavior

## Phase 2 - Reduction and sensitivity foundation

Objective:

- ship the exact analysis primitives first

Actions:

- implement `reduce_field`
- implement `SensitivityAnalyzer`
- create a synthetic toy field with known exact answers
- verify exact count changes under filters
- document the meaning of each reported metric

Outputs:

- reduction utility
- sensitivity engine
- tests with known expected results

Done when:

- every filtering operation is auditable and deterministic

## Phase 3 - Archetype extraction

Objective:

- turn large feasible spaces into interpretable scenario groups

Actions:

- add a k-modes wrapper
- test multiple `k` values in a notebook, likely 3 through 8
- select the smallest interpretable `k` for the example case
- return cluster modes and representative members

Outputs:

- clustering utility
- cluster summary tables
- example archetype descriptions

Done when:

- a reviewer can understand what each cluster represents and why

## Phase 4 - Scenario exploration workflow

Objective:

- build the notebook-first "agentic" layer

Actions:

- define a simple query format: drivers, goals, fixed conditions, excluded conditions, mode
- implement representative mode
- implement boundary mode
- implement robust mode
- add plain-language scenario explanations in the notebook

Outputs:

- exploration notebook
- reusable helper functions
- example outputs that can support the paper and presentations

Done when:

- you can answer practical questions like:
- "what is a representative scenario?"
- "what is an extreme but still feasible scenario?"
- "which scenarios remain valid under multiple constraint choices?"

## Phase 5 - Publication support

Objective:

- make the analysis module easy to describe in a paper and demo

Actions:

- capture methodology notes as you build
- document why k-modes was chosen
- document what "agentic scenario search" concretely means in this implementation
- provide one strong example narrative for the energy-policy case

Outputs:

- notes for JOSS wording
- notebook cells suitable for screenshots or figures
- a short explanation of methodological validity

Done when:

- Ali can describe the analysis module in the JOSS paper without inventing missing rationale

## 7. Different Scenarios and What to Do

### Scenario A - Core API is ready on time

Plan:

- build directly on `ConfigurationSpace`
- keep the analysis API thin and clean
- prioritize tests and example notebook immediately

Best move:

- this is the ideal path; use it to maximize integration quality

### Scenario B - Core API is delayed

Risk:

- your work blocks waiting for Ali

Response:

- prototype against a temporary adapter over Python lists or pandas-style records
- keep the adapter shaped like the intended `ConfigurationSpace`
- swap in the real core implementation once available

Best move:

- never block your entire module on core completion if the interface can be approximated safely

### Scenario C - Configuration counts become too large

Risk:

- exact clustering or full-space exploration becomes slow

Response:

- keep reduction exact
- add sampling or lazy evaluation only after exact reduction
- label all approximate behavior explicitly
- keep the public API honest about approximation

Best move:

- do not compromise correctness in the reduction layer just to speed up exploration

### Scenario D - Clusters are not interpretable

Risk:

- k-modes returns technically valid but conceptually weak archetypes

Response:

- compare multiple `k` values
- examine cluster modes manually
- supplement clustering with representative-case selection and boundary scenarios
- prefer interpretability over algorithmic complexity

Best move:

- if clustering is weak, the fallback is not "more ML"; it is clearer scenario summarization

### Scenario E - "Agentic" scope becomes vague or inflated

Risk:

- the team starts promising more than can be defended

Response:

- define the term narrowly: guided scenario exploration over feasible configurations
- keep exploration deterministic where possible
- treat LLM help as optional explanation, not mandatory core logic

Best move:

- preserve methodological credibility over marketing language

### Scenario F - The paper deadline arrives before the exploration layer is mature

Risk:

- unfinished "agentic" features threaten the release or paper submission

Response:

- ship `reduce_field`, clustering, and sensitivity as the stable analysis contribution
- present scenario exploration as experimental notebook functionality
- make the paper claim precise and modest

Best move:

- deliver a strong deterministic module rather than a weakly justified autonomous layer

## 8. Recommendations and Suggestions

### Technical recommendations

- keep `analysis/` independent from LLM dependencies
- design all outputs to be easy for Parastoo to visualize later
- use stable, named categorical representations rather than opaque integer encodings in user-facing outputs
- wrap third-party k-modes behavior behind your own interface so you are free to change internals later
- keep metadata rich enough to support notebook interpretation and paper writing

### Research recommendations

- treat auditability as a first-class requirement
- keep a short reading log for Ritchey and connect each reading insight to one implementation decision
- document the exact meaning of "robust", "boundary", and "representative" in the notebook
- avoid jargon unless you can operationalize it

### Collaboration recommendations

- align with Ali early on interface details, not late
- ask Sajjad to keep any AI integration optional from your perspective
- give Parastoo stable tabular outputs as early as possible so visual work can start before your module is "perfect"
- keep issue scopes small and clearly labeled under `analysis`

### Testing recommendations

- use toy fields with exact known counts for truth-based tests
- keep at least one example that mixes internal and external conditions
- make deterministic tests mandatory for all clustering code paths
- test bad inputs explicitly, including empty spaces, invalid filters, and impossible cluster requests

### Paper and presentation recommendations

- frame the analysis module as the bridge from "feasible combinations" to "interpretable scenarios"
- highlight reproducibility and audit trail, since these are academically strong claims
- use the energy-policy example as the recurring demonstration case
- prepare one figure or table that shows how the space shrinks and then becomes clustered into archetypes

## 9. What You Should Learn from Ritchey

You do not need to become a historian of the method. You need the parts that directly inform the design.

Focus on these questions:

- How does GMA move from raw parameter combinations to defensible scenario spaces?
- What is the role of linked fields and inference models?
- How are representative scenarios identified and discussed?
- Why is auditability essential?
- What makes a morphological model useful to decision makers rather than just computationally interesting?

Practical takeaway:

The analysis module should feel like a transparent reasoning aid, not a black-box recommender.

## 10. First-Week Checklist for Yousef

- read the proposal end to end
- write a short concept summary in your own words
- review Ritchey with emphasis on inference and linked-field reasoning
- list the minimum `ConfigurationSpace` features you need
- schedule alignment with Ali
- choose the first canonical example
- define the three exploration modes in plain language
- draft the expected notebook story before writing code

## 11. Questions You Should Resolve Early

- What exact interface will `ConfigurationSpace` expose?
- Will the feasible space be materialized, iterable, or lazily generated?
- What metadata from CCA will be available to analysis?
- What example field is considered the official first benchmark?
- How should impossible filter combinations be reported?
- What output format will Parastoo want for downstream charts?
- How much of the exploration layer belongs in the library versus notebook-only utilities?

## 12. Definition of Done for Yousef's Role

Yousef's role is successfully delivered when all of the following are true:

- `analysis/` provides deterministic reduction, clustering, and sensitivity functionality
- the module works on top of the core feasible-space representation rather than bypassing it
- there is a notebook that demonstrates representative, boundary, and robust scenario exploration
- tests show correctness on toy and example cases
- outputs are interpretable enough to support visualization and paper writing
- the implementation can honestly support the proposal's claims without overpromising

## 13. Final Practical Priority Order

If time gets tight, prioritize in this order:

1. `reduce_field`
2. `SensitivityAnalyzer`
3. `cluster_configurations`
4. research notebook
5. experimental scenario exploration helpers

This order preserves methodological strength, enables paper writing, and protects the project from overreaching.

## 14. Bottom Line

Your role is to turn a feasible configuration space into a useful analytical engine.

The important shift is this:

- do not think of your work as "adding AI"
- think of it as making the reduced morphospace explorable, explainable, and publishable

If you deliver:

- exact reduction
- interpretable clustering
- defensible sensitivity analysis
- a clear notebook for scenario exploration

then you will have delivered the part of the proposal that gives `pygma` analytical depth and makes the project substantially stronger for both JOSS and future contributors.
