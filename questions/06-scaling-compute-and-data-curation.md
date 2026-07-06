# 6. Scaling, Compute & Data Curation

The research-infrastructure side of foundation model work: distributed pretraining, data curation and deduplication at scale, and interpreting scaling behavior in practice.

---

### Q: What data curation and deduplication challenges are specific to assembling a large-scale pretraining dataset from public biological sequence databases? 🟡

**Answer:**
- **Extreme evolutionary redundancy** (discussed in section 3): public sequence databases contain enormous numbers of near-identical or closely related sequences (many isolates/orthologs of the same underlying protein or genomic region), and naively training on all of this redundant data both wastes compute on repeatedly reinforcing already well-represented sequence patterns and can bias the model's learned representations toward over-represented families at the expense of genuinely rare or underrepresented ones — deduplication and appropriate reweighting/downsampling of highly redundant clusters is a standard, important data curation step, analogous in spirit to deduplication practices in large language model pretraining corpora.
- **Data quality and annotation reliability variability:** public databases vary considerably in the quality and reliability of their sequence and annotation data (some entries reflect well-validated experimental characterization, others reflect automated, unvalidated computational predictions) — a careful pretraining data curation pipeline needs to account for and potentially filter or weight by this quality variability, rather than treating all database entries as equally trustworthy ground truth.
- **Taxonomic/phylogenetic imbalance:** public sequence databases substantially over-represent certain well-studied model organisms and heavily-sequenced taxonomic groups relative to the true diversity of life, and training naively on this imbalanced data risks producing a model that's disproportionately good at well-represented organisms/families and comparatively poor at underrepresented ones — deliberate rebalancing or stratified sampling strategies are an active area of practical data curation effort to address this.

**Follow-ups:**
- How would you decide on an appropriate sequence-identity threshold for deduplication/clustering, balancing the goal of reducing redundancy against the risk of discarding genuinely informative, only-slightly-different sequence variants?

---

### Q: How would you approach distributed training for a very large biological sequence foundation model, and what specific engineering challenges arise beyond those in standard large language model training? 🔴

**Answer:**
Standard large-scale distributed training techniques apply (data parallelism, model/tensor parallelism, pipeline parallelism, mixed-precision training), but a few considerations are specifically salient for biological sequence data (this connects closely to the distributed training discussion in the ML Engineer (Biotech) companion repo, from a more research-focused angle here):
- **Highly variable sequence lengths within a single training corpus** (e.g., proteins ranging from very short peptides to very long multi-domain proteins, or genomic regions of wildly varying length) create real batching efficiency challenges — naive padding to a fixed maximum length wastes substantial compute on padding tokens, motivating more sophisticated length-bucketing or dynamic batching strategies to maximize effective compute utilization.
- **Data loading pipelines need to handle domain-specific file formats and preprocessing efficiently** at the throughput required to keep large GPU clusters fed, which can become a genuine bottleneck if the data loading pipeline wasn't specifically engineered and profiled for this (echoing the data-loading-bottleneck discussion in the ML Engineer (Biotech) companion repo).
- **Checkpointing and experiment management need to account for the research-iterative nature of foundation model development** — research teams often run many parallel pretraining experiments varying architecture, objective, and data curation choices, requiring robust experiment tracking and the ability to efficiently compare and reproduce results across many large, expensive training runs, which is as much an experiment-management/tooling challenge as a pure distributed-systems engineering challenge.

**Follow-ups:**
- How would you design a length-bucketing/dynamic batching strategy to minimize wasted compute on padding, given a pretraining corpus with a very wide distribution of sequence lengths?

---

### Q: How would you interpret and act on an observed scaling curve during a bio foundation model pretraining research project — e.g., deciding whether it's worth scaling up further given a fixed compute budget? 🔴

**Answer:**
- **Fit and extrapolate the empirical scaling trend carefully**, being appropriately cautious about extrapolating far beyond the range of scales actually tested — scaling relationships observed at smaller scales don't always hold precisely at much larger scales, and confidently over-extrapolating a limited scaling curve to justify a very large compute investment is a common and risky mistake.
- **Distinguish between pretraining-loss scaling and downstream-task-performance scaling** (see section 3's discussion of why this distinction matters especially for bio foundation models) — a compute allocation decision should ideally be informed by how scaling translates into the actual downstream metrics practitioners care about, not just the pretraining objective's loss curve in isolation, since these can decouple in biology more than in some other domains.
- **Consider whether data availability, rather than compute or model size, is the actual binding constraint** (as discussed in section 3) — if scaling gains are already showing diminishing returns due to limited unique/non-redundant training data rather than insufficient model capacity or compute, further scaling up model size or compute alone may not be the most productive next investment, and effort might be better directed toward data curation, augmentation, or acquiring new experimental data instead.
- **Weigh the marginal scientific/practical value of further scaling against its real compute cost**, since at some point continued investment in scale may have a lower expected return than pursuing a different research direction entirely (e.g., better pretraining objectives, better evaluation methodology, or better data curation) — a mature research scientist should be able to make and defend this kind of resource-allocation judgment, not just default to "bigger is always better."

**Follow-ups:**
- Your scaling curve shows diminishing but still positive returns on pretraining loss with additional compute, but downstream task performance on your most important benchmark has plateaued. How would you interpret this, and what would you recommend doing next?

---

### Q: What experiment tracking and reproducibility practices are especially important for large-scale foundation model pretraining research, given the very high cost of any single training run? 🟡

**Answer:**
- **Rigorously log and version every factor that could affect a run's outcome** — exact code version, data curation pipeline version, hyperparameters, random seeds, hardware/infrastructure configuration — since with training runs this expensive, being unable to reproduce or correctly attribute the cause of a specific result (positive or negative) represents a substantial wasted investment, not just an inconvenience (this echoes and intensifies the general reproducibility discipline discussed in the Computational Biologist and ML Engineer (Biotech) companion repos).
- **Build in robust checkpointing** so that a long, expensive training run can be resumed cleanly after an infrastructure failure or interruption, without needing to restart from scratch — especially critical given that very large training runs may span many days and are more likely to encounter at least some infrastructure interruption over that time.
- **Run smaller-scale validation/pilot experiments before committing to a very large, expensive run** wherever feasible, to catch bugs or clearly poor configuration choices cheaply before they're baked into a much more costly full-scale run — a disciplined "test small before scaling up" culture is one of the highest-leverage practices for avoiding wasted large-scale compute investment.
- **Maintain a clear, organized record connecting specific research decisions/hypotheses to the specific experiments run to test them**, since foundation model research often involves many parallel exploratory experiments, and without disciplined tracking it becomes easy to lose track of what's actually been tried and what conclusions can genuinely be drawn from the accumulated experimental history.

**Follow-ups:**
- A very expensive, multi-week pretraining run just completed and the results look surprisingly bad compared to a much smaller pilot run. What would you check first to determine whether this reflects a genuine scaling issue versus an infrastructure/configuration bug?
