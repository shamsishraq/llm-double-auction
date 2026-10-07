# Quickstart Experiments

Simplified, reproducible versions of the three experimental treatments (`MinimalQS.ipynb`, `StandardQS.ipynb`, and `ScaffoldedQS.ipynb`) for running new replications.

The notebooks are intended for **running new replications of the experiment**. They automatically create a timestamped output directory containing the experiment log and figures. The original notebooks and the results in `results/initial<span>_</span>runs/` are preserved separately.

## 1. What these notebooks do

Each quickstart notebook runs one of the three LLM prompting treatments using the same double-auction experiment:

* 11 buyers and 11 sellers
* 5 market periods
* Local Ollama with `phi3:mini`
* Temperature = 0.7
* Fixed experiment parameters and random seeds
* Automatic logging of decisions, trades, prompts, market states, and summary results

The notebooks also automatically create a new timestamped directory under:

```text
results/replications/<Treatment>/
```

For example:

```text
results/
└── replications/
└── Minimal/
└── 20260915<span>_</span>143022/
├── experiment_log.csv
├── transaction_prices.png
└── market_efficiency.png
```

Each experiment run receives its own directory, so previous replications are not overwritten.

## 2. Test the setup first

Before conducting a full experiment, open the appropriate quickstart notebook and go to **Cell #3**. Change <code>TEST_RUN = False</code> to <code>TEST_RUN = True</code>, and run the entire notebook. The test run uses a much smaller configuration so that the experiment can be checked without waiting for a full run. It verifies that the notebook can:

* connect to Ollama,
* run the model,
* execute the market,
* create the replication directory,
* write the experiment log, and
* generate the figures.

After the test finishes, check the newly created directory under:

```text
results/replications/<Treatment>/
```

It should contain:

```text
experiment_log.csv
transaction_prices.png
market_efficiency.png
```

If these files are present and the notebook finishes without errors, the logging and output system is working.

## 3. Conduct the full experiment

If you have completed the test, return to **Cell #3** and change <code>TEST_RUN = True</code> back to <code>TEST_RUN = False</code>; else, keep the existing code. Then run the entire notebook again. This restores the full experimental configuration described in "## 1. What these notebooks do". The full experiment can take a substantial amount of time because every market decision requires a local LLM response. When the experiment finishes, the notebook will print the exact output directory. It will be located at:

```text
results/replications/<Treatment>/<timestamp>/
```

For example:

```text
results/replications/Standard/20260915<span>_</span>181547/
```

As mentioned, the directory will contain a csv (log.csv) and two png (prices.png, efficiency.png) files. This is the main record of the replication. It contains the logged decisions, model responses, prompts, market states, trades, period summaries, and experiment summary.

### Figures

<code>transaction_prices.png</code> shows transaction prices across periods, and <code>market_efficiency.png</code> shows market efficiency across periods. Each new full run receives a new timestamped directory. **Do not replace or delete previous replication directories if you want to preserve the experimental record.**