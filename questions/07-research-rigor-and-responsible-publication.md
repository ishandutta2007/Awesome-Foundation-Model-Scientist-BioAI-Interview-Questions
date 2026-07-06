# 7. Research Rigor & Responsible Publication

Reproducibility, honest benchmarking, and the genuine dual-use considerations specific to publishing and releasing powerful biological foundation models.

---

### Q: What does it mean for bio foundation model research to be reproducible, and what specific practices support this given the scale and cost of the work? 🟡

**Answer:**
Reproducibility here means that another researcher (or your own team, later) can verify a paper's reported results — both by re-running the exact reported experiments given the same code/data/configuration, and, more ambitiously, by independently replicating the core scientific finding using an independently-implemented approach.

Supporting practices: **releasing code, exact training configurations, and (where licensing/scale permits) trained model checkpoints** rather than only reporting summary results in a paper; **clearly documenting exact data sources, curation/deduplication steps, and evaluation splitting protocols** (given how much reported performance depends on these choices, as discussed in sections 3 and 4) rather than treating data processing as an unremarkable implementation detail unworthy of the same rigor as the modeling contribution itself; and **being transparent about compute budgets and the number of configurations/seeds tried** before arriving at reported results, since foundation model research often involves substantial hyperparameter/configuration search, and selectively reporting only the best result out of many attempts (without disclosing this) can make a method look more reliable or robust than it actually is.

**Follow-ups:**
- Why might releasing exact evaluation data splits be just as scientifically important as releasing model code/weights, given the concerns about evolutionary redundancy discussed in section 3?

---

### Q: What is "cherry-picking" or "researcher degrees of freedom" in the context of reporting bio foundation model results, and how would you guard against this in your own work? 🔴

**Answer:**
Researcher degrees of freedom refers to the many small, seemingly reasonable methodological choices a researcher makes throughout a project (which specific benchmark tasks to report, which baseline comparisons to include, which random seeds or hyperparameter configurations to present) — when these choices are made or adjusted after seeing preliminary results (even unintentionally, driven by a natural desire to present a favorable result), the final reported results can end up substantially more favorable than what a genuinely pre-specified, unbiased evaluation protocol would have produced, without this necessarily reflecting any deliberate dishonesty.

Guarding against this: **pre-registering the evaluation protocol** (which specific benchmarks, metrics, and baselines will be reported) before running the full evaluation, where feasible, rather than deciding what to report after seeing which results look best; **reporting results across multiple random seeds with appropriate variance/uncertainty estimates**, rather than reporting a single best-case run; **including a comprehensive, representative set of baselines and benchmarks** (including ones where the proposed method doesn't necessarily win) rather than selectively choosing comparisons that favor the new method; and **being transparent in the paper itself about the full space of configurations/approaches that were tried**, not just the final reported configuration, so readers can appropriately calibrate how much of the reported improvement might reflect a favorable multiple-comparisons effect rather than a genuinely robust finding.

**Follow-ups:**
- How would you handle a situation where your team's internal experiments show a new method winning on 3 out of 5 benchmarks tried, and there's pressure to report only those 3 favorable results in the paper?

---

### Q: What is the dual-use risk consideration specific to publishing or open-sourcing powerful biological sequence-generation or design models (e.g., protein design models), and how would you think about balancing open scientific publication against this risk? 🔴

**Why it's asked:** A serious, field-specific safety topic that has been explicitly and publicly discussed in the scientific literature around protein design and biological sequence generation models — this is not a hypothetical concern but one taken seriously by leading labs and policy bodies in this space.

**Answer:**
Powerful generative models capable of designing novel biological sequences (proteins, and by extension potentially other biological agents) could, in principle, be misused to assist in designing harmful biological agents rather than the beneficial applications (novel enzymes, therapeutics) the research is intended for — this concern has been explicitly raised and discussed by researchers, institutions, and policymakers in this specific research area, and reflects genuine, actively-studied risk rather than speculative alarmism.

Practical considerations for balancing open publication against this risk: **assessing the specific marginal risk a given model/publication actually poses** (e.g., considering whether the capability being published meaningfully lowers barriers to harm beyond what's already achievable through existing, publicly known methods, versus representing a more novel and higher-uplift capability); **considering staged or structured access approaches** for particularly high-risk capabilities (e.g., providing research access to vetted institutions rather than fully open, unrestricted release) as a middle ground between full openness and complete non-disclosure; **screening/safeguarding released tools** (e.g., screening generated outputs against known harmful-sequence databases, similar to mitigations discussed in the AI Drug Discovery Scientist companion repo) as a complementary safeguard alongside publication/access decisions; and **engaging with relevant institutional biosafety and dual-use research oversight processes** rather than treating this as a purely individual researcher judgment call, given the genuinely high stakes and the value of broader institutional and field-level deliberation on norms that are still actively evolving.

**Follow-ups:**
- How would you evaluate whether a specific piece of your research represents a genuinely novel increase in dual-use risk versus incremental research that doesn't meaningfully change the existing risk landscape?

---

### Q: How would you approach deciding on the appropriate level of access/release for a newly developed, powerful bio foundation model — fully open-source, gated/structured access, or closed/internal-only? 🔴

**Answer:**
- **Assess the model's actual capability and risk profile concretely**, rather than applying a blanket policy to all models regardless of their specific capabilities — a general-purpose representation-learning model used mainly for property prediction poses a very different risk profile than a generative sequence-design model, for example, and release decisions should be calibrated to the specific model's actual capabilities.
- **Weigh the scientific and public-health benefits of open access** (accelerating beneficial research broadly, enabling independent verification and extension of the work, supporting the broader open-science norms that have historically driven much progress in this field) against the specific, concrete risks identified for that model.
- **Consider intermediate options** rather than treating this as a strictly binary open-vs-closed choice — e.g., releasing model weights but not certain training data or fine-tuning recipes that could lower barriers to misuse; releasing to a vetted set of research institutions under a data/model use agreement; or releasing an API with usage monitoring and misuse-detection safeguards rather than raw downloadable weights.
- **Recognize this is an evolving area of scientific and policy norm-setting**, not a fully settled question, and stay engaged with current guidance and community/field norms (which are actively developing) rather than relying on a fixed personal policy formed at one point in time.

**Follow-ups:**
- How would your recommended release strategy change if you learned that a closely comparable capability had already been openly released by another research group, versus if your model represented a clearly novel capability advance?

---

### Q: How would you handle a situation where your research team's results contradict or fail to replicate a previously published, well-cited result in bio foundation model research? 🟡

**Answer:**
- **Rule out mundane explanations first** — differences in exact data preprocessing, evaluation protocol, hyperparameters, or software/library versions are common, non-scientifically-interesting causes of apparent non-replication, and should be carefully checked and ruled out (or identified as the actual explanation) before concluding the original result itself doesn't hold up.
- **Attempt to reproduce the original result as closely as possible first**, using the original paper's released code/data where available, before drawing conclusions about a genuinely new/independent implementation's differing results — this helps isolate whether the discrepancy is about the original claim itself or about your specific reimplementation.
- **Communicate a failure to replicate carefully and constructively**, clearly distinguishing between "we couldn't reproduce this under conditions X, Y, Z" and a stronger claim like "the original result is wrong or was misreported" — the former is a much more defensible and useful contribution to the field, while the latter requires a much higher bar of evidence before asserting.
- **Consider that a genuine, well-documented failure to replicate is itself a valuable scientific contribution** worth sharing with the community (e.g., as a technical report, a comment/correspondence, or context within your own paper), rather than something to quietly discard, since it can help the broader field calibrate confidence in specific results and identify sources of fragility in reported findings.

**Follow-ups:**
- How would you decide whether a failure to replicate a specific published result is significant enough to warrant a public correction/comment, versus just informally noting it internally and moving forward with your own research?
