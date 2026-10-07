# LLM Double Auction

A computational implementation of a competitive double auction using large language model (LLM) agents, inspired by Vernon L. Smith's classic experimental study of competitive market behavior.

## Overview

This project explores whether a market environment based on the competitive double-auction institution can be populated with LLM-controlled buyers and sellers. The current implementation uses 11 buyers and 11 sellers, each with a private reservation value or cost, operating over repeated market periods. Agents observe public market activity while keeping their own reservation information private.

The project is strictly a **proof of concept**, not a completed empirical study. The immediate research objective is to establish a functioning LLM-based experimental market and examine whether different prompting conditions produce different patterns of market behavior.

## Experimental Treatments

The platform manipulates the strategic decision guidance provided within the agent system prompts across three distinct baseline conditions. These experimental configurations evaluate behavioral changes under varying degrees of structural guidance rather than asserting a strict hierarchy of prompt effectiveness.

|Treatment|Structural Guidance Provided to LLM Agents|
|-|-|
|**Minimal**|Enforces basic role boundaries, private valuation limits, core market rules, and minimal decision scaffolding.|
|**Standard**|Utilizes identical structural information but injects a clear objective framing focused on individual profit maximization.|
|**Scaffolded**|Implements an explicit cognitive checklist forcing the agent to systematically evaluate reservation metrics, active spreads, transaction histories, and prior decisions.|

## Economic Environment

The underlying simulation framework deploys exactly 11 buyers and 11 sellers, restricting each trader to a single active unit per period across 5 continuous market rounds. The buyer reservation values scale from 0.75 to 3.25, perfectly mirroring the seller cost assignments. This structural symmetry yields a theoretical competitive equilibrium price of **2.00**, an equilibrium quantity of **6**, and a maximum positive-surplus allocation benchmark of **7.50**. While the global supply and demand schedules remain invariant, trader-specific valuation assignments are randomized computationally using a fixed seed to evaluate distribution robustness.

## Model

The reported initial runs utilize the parameters outlined below. The deployment of a small, open-source model running via a localized interface represents the specific computational and resource constraints under which this proof of concept was constructed.

* **Model Architecture:** `phi3:mini` via a local Ollama interface
* **Hyperparameters:** Temperature: 0.7
* **Deterministic Seeds:** Valuation Seed: 42 | Scheduler Seed: 42

## Initial Results

Three exploratory, single-run experiments were completed to benchmark the baseline properties of each treatment condition. The aggregate outputs are detailed below:

|Treatment|Trades|Average Price|Surplus|Efficiency|Decisions|Runtime|
|-|-:|-:|-:|-:|-:|-:|
|Minimal|20|2.2000|18.75|50.00%|297|78.54 min|
|Standard|18|1.5139|24.75|66.00%|290|84.00 min|
|Scaffolded|2|0.9500|2.75|7.33%|323|88.08 min|

While these initial results demonstrate substantial behavioral divergence across the three prompt conditions, each treatment environment was observed only once. Consequently, these isolated outcomes **do not establish causal treatment effects or statistical significance**. They function exclusively as preliminary evidence that the computational framework can successfully capture variations in market dynamics when subjected to alternative prompting architectures.

## Repository Structure

```text
llm-double-auction/
├── README.md
├── LICENSE
├── requirements.txt
├── .gitignore
│
├── notebooks/
│ ├── Minimal.ipynb
│ ├── Standard.ipynb
│ └── Scaffolded.ipynb
│
├── docs/
│ └── experiment\\\_design.md
│
├── results/
│ ├── initial\\\_runs/
│ │ ├── Minimal/
│ │ │ └── period\\\_results.csv
│ │ ├── Standard/
│ │ │ └── period\\\_results.csv
│ │ └── Scaffolded/
│ │ └── period\\\_results.csv
│ └── summary/
│ └── treatment\\\_comparison.csv
│
├── figures/
│
└── data/
└── README.md
```

## Reproducing the Experiment

This environment runs inference entirely through a local server instance rather than making network requests to a paid external API. Execution times and resource loads depend heavily on the host hardware architecture. To set up and execute the auction pipelines, run the following steps in your terminal:

```bash
# 1. Clone the repository and navigate into the root directory
git clone https://github.com
cd llm-double-auction

# 2. Install Python dependencies
pip install -r requirements.txt

# 3. Ensure Ollama is installed and pull the required model
ollama pull phi3:mini

# 4. Launch the local notebooks to run the desired treatment pipeline
jupyter notebook notebooks/

## Results and Data Logging
The three initial experiments were completed before machine-readable decision-level logging was added to the project. The repository therefore includes the period-level and treatment-level results that were available from those experiments. These files should not be interpreted as complete raw decision logs. Future versions of the experiment should persist decision-level market states, agent actions, quotations, transactions, model metadata, and period outcomes automatically.

## Relation to Vernon L. Smith
This project is conceptually grounded in the classic laboratory double-auction rules studied by Vernon L. Smith in \\\*"An Experimental Study of Competitive Market Behavior."\\\* Crucially, this software architecture serves as a computational adaptation designed for generative text models rather than a literal replication of traditional human-subject laboratory procedures. Variations regarding artificial agent reasoning loops, automated computational stopping conditions, persistent electronic memory stores, and randomized transaction matching algorithms are comprehensively detailed in `docs/experimental\\\_design.md`.

## Limitations

As an initial proof of concept, the current project has several methodological and hardware constraints that limit the generalizability of the findings:
\\\* \\\*\\\*Sample Size Constraints:\\\*\\\* Each prompting treatment contains only a single completed execution run, meaning no formal statistical inference or treatment effects can be validated.
\\\* \\\*\\\*Model Scaling Limitations:\\\*\\\* The reliance on `phi3:mini` was dictated by strict local hardware constraints; behavioral patterns observed under this model cannot be assumed to scale identically to cloud-hosted frontier LLMs.
\\\* \\\*\\\*Computational Cost:\\\*\\\* The multi-agent sequential pipeline requires heavy local processing overhead, which naturally restricts high-frequency independent replications.
\\\* \\\*\\\*Systemic Variables:\\\*\\\* The software implementation introduces specialized algorithmic constraints that alter price discovery mechanisms compared to human baselines, and agent behaviors remain highly sensitive to hyper-specific semantic shifts in prompt wording.

## Future Work
Future research vectors can leverage this framework to investigate independent replications to establish statistical significance, test scaling effects using larger language models, and evaluate alternative agent memory architectures. Additionally, the engine can be expanded to analyze agent adaptation under fluctuating supply and demand curves, variant matching institutions, and direct human-versus-LLM comparative criteria. The broader, long-term research path involves developing fully controlled computational economic environments where large cohorts of autonomous agents can be evaluated under systematically manipulated institutional parameters.

## License
This project is licensed under the terms of the MIT License. See the `LICENSE` file for details.


