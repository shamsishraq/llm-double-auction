# Experiment Design

## 1\. Overview

This project implements a computational version of a competitive double auction inspired by Vernon L. Smith's experimental study of competitive market behavior. The immediate objective is to establish a proof of concept for using large language model (LLM) agents as traders in an experimentally controlled market environment. The implementation places LLM-controlled buyers and sellers in a repeated double auction with private reservation values or costs and publicly observable market activity.

We tested three prompting treatments using the same underlying market environment: **Minimal**, **Standard**, and **Scaffolded**. These treatments differ in the amount of decision guidance provided to the LLM agents. They should be regarded as experimental manipulations rather than as a ranking of prompting strategies. The three completed experiments reported in this repository are initial single-run observations. They are descriptive and exploratory and are not sufficient to establish statistically reliable treatment effects.

## 2\. Economic Benchmark

The experimental design is based on the competitive double-auction institution described by Vernon L. Smith in "An Experimental Study of Competitive Market Behavior." In Smith's experiments, buyers receive private maximum willingness-to-pay values and sellers receive private minimum willingness-to-accept values. Traders can publicly submit bids or offers and can accept standing quotations. Once a trader completes a transaction, that trader leaves the market for the remainder of the period. The supply and demand schedules determine the potential gains from trade. Repeated market periods allow observed transaction prices and quantities to be compared with the competitive equilibrium implied by the underlying schedules. The present implementation follows this general institutional logic. However, we made several adaptations required for LLM-based computation, and to fit device constraints.

## 3\. Trader Population and Valuations

Each experimental period consists of 11 buyers and 11 sellers, with exactly one unit available to each trader per period.

The baseline buyer reservation values are configured as:
`3.25, 3.00, 2.75, 2.50, 2.25, 2.00, 1.75, 1.50, 1.25, 1.00, 0.75`

The baseline seller reservation costs are configured as:
`0.75, 1.00, 1.25, 1.50, 1.75, 2.00, 2.25, 2.50, 2.75, 3.00, 3.25`

The underlying valuation and cost schedules are therefore symmetric around the theoretical competitive equilibrium. The trader-specific assignments were randomized using a fixed valuation seed. The resulting assignments used in the reported runs were:

|Trader|Role|Reservation|
|-|-|-:|
|B1|Buyer|1.50|
|B2|Buyer|2.50|
|B3|Buyer|2.75|
|B4|Buyer|1.25|
|B5|Buyer|2.00|
|B6|Buyer|1.75|
|B7|Buyer|1.00|
|B8|Buyer|2.25|
|B9|Buyer|3.25|
|B10|Buyer|3.00|
|B11|Buyer|0.75|
|S1|Seller|1.75|
|S2|Seller|1.50|
|S3|Seller|1.25|
|S4|Seller|3.25|
|S5|Seller|2.00|
|S6|Seller|1.00|
|S7|Seller|2.50|
|S8|Seller|2.75|
|S9|Seller|0.75|
|S10|Seller|2.25|
|S11|Seller|3.00|

## 4\. Theoretical Competitive Equilibrium

Based on the valuation and cost schedules detailed above, the theoretical competitive equilibrium yields:

* **Equilibrium Price:** 2.00
* **Equilibrium Quantity:** 6
* **Maximum Positive-Surplus Allocation:** 7.50

These metrics establish the baseline benchmark used to evaluate transaction prices, quantities, total surplus, market efficiency, and price dispersion.

## 5\. Market Institution

The implementation uses a computational double-auction environment. During each period, the market operates under the following rules:

1. All traders begin the period in an active state.
2. Buyers and sellers know only their own private reservation value or cost.
3. Traders cannot directly observe the reservation values of their peers.
4. Market quotations and transaction history (the tape) are publicly viewable.
5. Active agents make sequential decisions based on a randomized trader ordering system.
6. Permitted actions include: submitting a bid, submitting an ask, accepting a standing quotation, or passing.
7. Completing a transaction immediately removes both the buyer and seller from the active market for the rest of that period.
8. Transaction prices and sequential market actions are logged continuously.
9. The period terminates once a configured stopping condition, stagnation limit, or main decision limit is reached.

The implementation uses randomized trader decision ordering.

## 6\. Information Structure

The architecture maintains a strict boundary between private trader information and public market data.

### Private Information

Each individual agent has access to:

* Their assigned role (Buyer/Seller).
* Their specific reservation value or cost.
* A personal log of current-period actions and completed trades.
* Their own accumulated profit metrics.
* A historical record of their past private experiences.

### Public Information

All agents simultaneously observe:

* Current standing bids.
* Current standing asks.
* The latest transaction prices.
* The rolling market-tape activity log.
* Public performance summaries from prior periods.

Other traders' private reservation values remain private.

## 7\. Agent Memory

The implementation provides persistent memory across periods. The private memory contains recent information about the trader's own previous actions, trades, and profits. Public market history contains information about previous market periods. The memory mechanism is an implementation feature rather than a claim that the LLM has human-like learning. It is included so that the experiment can examine behavior in a repeated market rather than treating every period as completely independent.

## 8\. LLM Model and Computational Environment

We conducted the experiments locally using the `phi3:mini` model via Ollama. Local deployment was selected because the available hardware environment could not practically support larger open-source models at scale. The model interacts with the pipeline through the local generation endpoint: `http://localhost:11434/api/generate`

The key runtime parameters for the reported runs are:

* **Model:** `phi3:mini`
* **Temperature:** 0.7
* **Seeds:** Scheduler Seed: 42 | Valuation Seed: 42
* **Market Scale:** 11 Buyers, 11 Sellers, 5 Periods
* **Valuation Bounds:** Min Cost/Value: 0.75 | Max Cost/Value: 3.25 (Step Size: 0.25)
* **Equilibrium Targets:** Price: 2.00 | Quantity: 6
* **Execution Limits:** Main Limit: 60 decisions/period | Stagnation Limit: 15 non-changing actions
* **Features:** Final calls enabled

## 9\. Prompting Treatments

While the underlying market engine and parameters remain fixed, the system manipulates the strategic guidance provided within the agent system prompts. The prompts, therefore, act as a "bridge" between expected human behavior and the LLMs.

### 9.1 Minimal

The Minimal treatment serves as our baseline. The prompt provides basic operational context: the agent's role, its private valuation/cost, a list of permitted actions, and essential market rules.

Agents are broadly instructed to protect their own profit, leverage public market history, and respect privacy boundaries. The system explicitly instructs them to avoid deliberately targeting a known equilibrium or consciously trying to maximize total market surplus. This treatment provides minimal structural decision assistance.

### 9.2 Standard

The Standard treatment includes all baseline details but explicitly anchors the agent’s objective around self-interested optimization. The prompt explicitly instructs the agent that its core goal is to execute decisions it calculates will maximize its personal net profit.

It reinforces standard institutional boundaries: utilizing public tape data, tracking past personal performance, keeping valuations private, and refraining from altruistic behaviors like pursuing maximum total market surplus or forced equilibrium states.

### 9.3 Scaffolded

The Scaffolded treatment provides the most explicit decision checklist. Before deciding, the agent is instructed to consider its private reservation value or cost, standing bids and asks, recent transaction prices, the recent public market tape, information learned from previous periods, and its own previous decisions and trades. The agent is also explicitly told that it may revise its quotation and does not have to accept an available quotation merely because it exists.

As with the other treatments, the agent is not instructed to maximize total market surplus or deliberately target equilibrium.

## 10\. Decision Rules

The large language model is required to produce structural actions sequentially, one at a time. The parsing engine recognizes four discrete command protocols: `BID X`, `ASK X`, `ACCEPT`, and `PASS`, where `X` represents a proposed transaction price. The underlying market mechanism interprets each parsed string response and dynamically updates the environmental market state. When active quotations cross, the software executes a transaction according to the configured crossing logic. Alternatively, a transaction can be produced when an agent explicitly accepts an active standing quotation. Once a buyer or seller completes a transaction, that specific trader is marked inactive and removed from the operational matching pool for the remainder of the current period.

## 11\. Period and Market Stopping Conditions

The main market process is bounded by a maximum number of decisions per period. The implementation also uses a stagnation rule. If a configured number of consecutive decisions produces no meaningful market change, the period can terminate. Final calls are enabled in the reported experiments. These additional calls mean that the total number of recorded decisions can exceed the nominal 60-decision main-market limit. Consequently, the reported decision counts should not be interpreted as a strict maximum of 60 decisions per period.

## 12\. Outcome Measures

Three initial exploratory experiments were executed, mapping exactly to one complete single-run observation for each of the three defined prompting treatments. The aggregate outcomes are recorded within the repository at `results/summary/treatment\_comparison.csv`, with localized granular period-level data stored under `results/initial\_runs/`.

* **Minimal Treatment:** Completed 20 total trades across 297 total decisions, yielding an average transaction price of 2.2000. Total realized surplus reached 18.75, translating to an overall market efficiency of 50.00% over a local execution runtime of 78.54 minutes.
* **Standard Treatment:** Completed 18 total trades across 290 total decisions, yielding an average transaction price of 1.5139. Total realized surplus reached 24.75, translating to an overall market efficiency of 66.00% over a local execution runtime of 84.00 minutes.
* **Scaffolded Treatment:** Completed 2 total trades across 323 total decisions, yielding an average transaction price of 0.9500. Total realized surplus reached 2.75, translating to an overall market efficiency of 7.33% over a local execution runtime of 88.08 minutes.

## 13\. Initial Experimental Runs

Three initial exploratory experiments were executed, mapping exactly to one complete single-run observation for each of the three defined prompting treatments. The aggregate outcomes are recorded within the repository at `results/summary/treatment\_comparison.csv`, with localized granular period-level data stored under `results/initial\_runs/`.

* **Minimal Treatment:** Completed 20 total trades across 297 total decisions, yielding an average transaction price of 2.2000. Total realized surplus reached 18.75, translating to an overall market efficiency of 50.00% over a local execution runtime of 78.54 minutes.
* **Standard Treatment:** Completed 18 total trades across 290 total decisions, yielding an average transaction price of 1.5139. Total realized surplus reached 24.75, translating to an overall market efficiency of 66.00% over a local execution runtime of 84.00 minutes.
* **Scaffolded Treatment:** Completed 2 total trades across 323 total decisions, yielding an average transaction price of 0.9500. Total realized surplus reached 2.75, translating to an overall market efficiency of 7.33% over a local execution runtime of 88.08 minutes.

## 14\. Interpretation of the Initial Runs

The three initial runs produced substantially different market outcomes. However, each treatment was run only once. The observed differences therefore cannot establish that one prompting treatment systematically produces different economic outcomes from another. The results should instead be interpreted as evidence that the implemented LLM market can produce materially different market behavior under different prompt treatments, and as motivation for further investigation. The experiment is consequently a proof of concept rather than a completed causal study.

## 15\. Relationship to Smith's Experiments

This implementation adapts Smith's double-auction framework to an autonomous environment through several key modifications:

* Substitution of human subjects with local LLM agents
* Sequential text generation for decision execution
* Computational memory persistence across trading periods
* Randomized algorithmic decision ordering
* Software-enforced stopping and stagnation limits

The repository therefore presents the implementation as an LLM-based adaptation of the Smith-style double auction, not as an exact reproduction of Smith's original experimental procedure.

## 16\. Limitations

As an initial proof of concept, this framework possesses several methodological and computational limitations that bound the generalizability of the findings:

* **Sample Size and Hardware Constraints:** Each treatment condition features only a single completed run, meaning no formal statistical inference regarding treatment effects can be justified. Furthermore, the reliance on a small open-source model (`phi3:mini`) was dictated by strict local hardware constraints; the behavioral patterns observed here cannot be assumed to generalize to larger, cloud-hosted frontier LLMs.
* **Computational Cost and Logging:** The execution of these multi-agent simulations requires substantial local computation time, which naturally restricts high-frequency independent replications. Additionally, the current software iteration did not persist complete decision-level logs directly to disk. The repository instead preserves period-level and transaction-level outputs, meaning comprehensive step-by-step model metadata and rejected quotation trajectories cannot be fully reconstructed in this version.
* **Institutional and Prompt Dependencies:** The translation of human market institutions into software loops necessarily introduces artificial rules that alter price convergence, efficiency, and bidding velocity compared to human baselines. Finally, because LLMs are highly sensitive to semantic modifications, the observed outputs represent hyper-specific responses to the exact prompt phrasing used, rather than universal laws of agent behavior.

## 17\. Future Research

The immediate purpose of this repository is to establish the feasibility of the experimental environment. Future work can investigate:

* Independent replications of each treatment.
* Larger and more capable language models.
* Alternative prompting and information structures.
* Different memory architectures.
* Changes in supply and demand.
* Alternative market institutions.
* Learning and adaptation across repeated periods.
* Robustness across valuation assignments.
* Human-versus-LLM comparisons.
* Systematic analysis of price convergence and market efficiency.

A longer-term research direction is to develop controlled synthetic economic environments in which LLM agents can be studied under systematically varied economic institutions and conditions. That direction is beyond the scope of the present proof of concept.

