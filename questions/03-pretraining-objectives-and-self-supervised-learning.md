# 3. Pretraining Objectives & Self-Supervised Learning

The self-supervised objectives used to pretrain biological foundation models, and how scaling behavior plays out in this domain.

---

### Q: Explain masked language modeling as applied to protein sequences, and what biological assumption underlies why this objective produces useful representations. 🟢

**Answer:**
Masked language modeling for proteins randomly masks some fraction of residues in an input sequence and trains the model to predict the identity of the masked residues based on the surrounding (unmasked) sequence context — directly analogous to masked language modeling objectives used for text.

The underlying biological assumption: **a protein's sequence has been shaped by evolutionary selection to maintain proper folding and function**, so the identity of any given residue is not arbitrary but is statistically constrained by its surrounding sequence context (which residues are compatible with maintaining the protein's structural/functional integrity at that position, given the rest of the sequence). A model that learns to accurately predict masked residues from context is therefore implicitly forced to learn something about these underlying structural/functional constraints, even though it's never given an explicit structure or function label — this is the same underlying logic discussed in section 1's explanation of why transfer learning works for protein language models.

**Follow-ups:**
- Why might the specific masking strategy (e.g., what fraction of residues to mask, whether masking is random or structured in some way) meaningfully affect what a model learns during pretraining?

---

### Q: How do contrastive learning objectives differ from masked/generative pretraining objectives, and give an example of where contrastive learning has been applied in a biological foundation model context. 🟡

**Answer:**
Contrastive learning objectives train a model to produce representations where semantically/biologically related examples ("positive pairs") are pulled close together in representation space, while unrelated examples ("negative pairs") are pushed apart — rather than directly predicting masked tokens or generating sequence content as in generative/masked pretraining objectives. This requires defining what counts as a meaningful "positive pair" for the specific data/task at hand, which is itself often a nontrivial and important design decision.

An example application: in single-cell genomics, contrastive objectives have been used where different cells (or different views/augmentations of the same cell's data) that are believed to represent similar underlying biological states are treated as positive pairs, encouraging the model to learn representations that cluster biologically similar cells together — analogous in spirit to how contrastive learning is used in computer vision to learn representations invariant to non-meaningful variations (like image augmentations) while remaining sensitive to meaningful semantic differences. Choosing biologically appropriate positive/negative pairs (e.g., what counts as a meaningful "augmentation" or "similar" example for a given biological data type) is a central and often underappreciated design decision that directly determines what the contrastive objective will actually teach the model to represent.

**Follow-ups:**
- How would you decide what constitutes a meaningful positive pair for a contrastive pretraining objective on a new biological data type you haven't worked with before?

---

### Q: What are "scaling laws," and what has research on scaling laws for language/vision models suggested about the relationship between model size, data size, and compute? Why might this not transfer straightforwardly to biological foundation models? 🔴

**Answer:**
Scaling laws describe empirically observed, often remarkably predictable relationships between a model's performance (typically measured as pretraining loss) and factors like model size (number of parameters), training data size, and compute budget — a well-known finding in large language model research is that performance improves smoothly and predictably as these factors scale up (following roughly power-law relationships), and there exist compute-optimal tradeoffs between model size and data size for a given compute budget.

Why this might not transfer straightforwardly to biological foundation models: **available high-quality unlabeled biological data is far more limited** than the scale of text/image data used in the largest general-purpose foundation models, meaning bio foundation models may hit data availability limits well before compute or model-size limits become the binding constraint — the "compute-optimal" tradeoff point derived from text/vision scaling studies doesn't automatically apply when data, rather than compute, is the scarcer resource. Additionally, **the relationship between pretraining loss and actual downstream scientific usefulness is less well-established for biological objectives** than for language modeling — a lower masked-residue-prediction loss doesn't automatically or linearly translate into better performance on the scientifically meaningful downstream tasks practitioners actually care about (see section 4), so scaling laws framed purely in terms of pretraining loss may not tell the full story of whether scaling is actually worth the investment for a given bio foundation model application.

**Follow-ups:**
- If you found that a bio foundation model's pretraining loss kept improving with more model parameters and compute, but this stopped translating into meaningfully better downstream task performance past a certain scale, how would you interpret and act on this finding?

---

### Q: What is "data leakage via evolutionary redundancy," and why is it a specific, serious concern when pretraining and evaluating protein language models on public sequence databases? 🔴

**Answer:**
Public protein sequence databases contain enormous evolutionary redundancy — many sequences are closely related to each other (e.g., orthologs across many closely related species, or many isolates of the same protein family) rather than representing independent, unique samples of "protein sequence space." If a model's pretraining data and its downstream evaluation/held-out test set are naively split (e.g., a plain random split of individual sequences) without accounting for this evolutionary relatedness, a model can appear to generalize well on evaluation partly because it has effectively already "seen" very similar sequences during pretraining, rather than because it has learned genuinely generalizable biological patterns.

This is directly analogous to the scaffold-splitting concerns discussed for molecular data in the AI Drug Discovery Scientist companion repo, and the phylogenetically/cluster-aware splitting concerns discussed in the Computational Biologist and ML Engineer (Biotech) companion repos — the appropriate mitigation is similar: **clustering sequences by evolutionary relatedness (e.g., using sequence identity thresholds or phylogenetic grouping) and ensuring evaluation sequences are sufficiently evolutionarily distinct from anything seen during pretraining**, rather than relying on a naive random split, when the goal is to assess genuine generalization to novel protein families or evolutionarily distant sequences rather than memorization of near-duplicate sequences.

**Follow-ups:**
- How would you specifically design an evaluation protocol to test whether a protein language model generalizes to a genuinely novel protein family with few or no close evolutionary relatives in the pretraining data?

---

### Q: What is "catastrophic forgetting," and why is it a relevant concern when fine-tuning a pretrained biological foundation model on a narrow downstream task? 🟡

**Answer:**
Catastrophic forgetting refers to a model losing previously learned general capabilities/knowledge (acquired during broad pretraining) as it's further trained (fine-tuned) on a new, narrower task — the fine-tuning process can overwrite or degrade the broad, general-purpose representations that made the pretrained model valuable in the first place, especially if fine-tuning uses a high learning rate, runs for many steps, or is performed on a small, narrow dataset that doesn't reflect the full diversity the model originally learned from.

This is a relevant concern in the bio foundation model context because a common workflow involves fine-tuning a broad pretrained model (e.g., a protein language model pretrained across many protein families) on a much narrower, small labeled dataset for a specific task (e.g., predicting a specific property for one protein family) — if this fine-tuning process degrades the model's broader learned representations, subsequent attempts to apply the "fine-tuned" model to a different, related task may perform worse than the original pretrained model would have, or worse than starting fresh with the original pretrained checkpoint. This is part of why parameter-efficient fine-tuning approaches (discussed in the ML Engineer (Biotech) companion repo) and careful learning-rate/regularization choices during fine-tuning are common practical mitigations.

**Follow-ups:**
- How would you experimentally test whether fine-tuning has caused meaningful catastrophic forgetting in a specific bio foundation model, before deciding whether the fine-tuned checkpoint is safe to reuse for other downstream applications?
