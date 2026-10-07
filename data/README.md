# Data

This project does not use a conventional external dataset for the double-auction environment.

Buyer reservation values and seller costs are generated directly from the experimental design. The underlying schedules are:

- Buyers: 3.25 to 0.75 in increments of 0.25
- Sellers: 0.75 to 3.25 in increments of 0.25

Trader-specific assignments are randomized using a fixed seed and are documented in `docs/experimental_design.md`.

The reported initial experimental results are stored under `results/`.

No external dataset is required to reproduce the market environment itself.