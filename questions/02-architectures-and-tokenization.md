# 2. Architectures & Tokenization for Biological Data

Adapting transformer architectures and tokenization schemes to sequences that behave quite differently from natural language text.

---

### Q: What are the main tokenization strategies used for DNA/genomic sequence foundation models, and what tradeoffs does each involve? 🟡

**Answer:**
Common approaches:
- **Single-nucleotide tokenization:** treating each individual base (A, C, G, T) as a token — simple and avoids introducing any arbitrary segmentation, but produces very long token sequences for a given amount of genomic content, which is computationally expensive for transformer architectures whose compute/memory cost typically scales at least quadratically with sequence length (for standard attention) unless more efficient architectures are used.
- **Fixed-length k-mer tokenization:** treating overlapping or non-overlapping fixed-length substrings (k-mers, e.g., 6-mers) as tokens — reduces effective sequence length compared to single-nucleotide tokenization, but introduces an arbitrary choice of k that may not align with biologically meaningful boundaries, and overlapping k-mer schemes can introduce redundancy/correlation between adjacent tokens that some architectures need to handle carefully.
- **Learned subword tokenization (e.g., byte-pair-encoding-style approaches adapted to DNA):** learning a vocabulary of variable-length frequent subsequences from the training data, similar in spirit to how subword tokenization works for text — can adaptively capture frequently recurring motifs, but the resulting tokens may not correspond to any specific biologically meaningful unit, and results can be sensitive to the specific corpus used to learn the vocabulary.

The core tradeoff across all these approaches is between **sequence length (and therefore computational cost)** and **preserving fine-grained, potentially biologically important single-base-pair resolution** — a coarser tokenization scheme that reduces sequence length can risk losing sensitivity to biologically important single-nucleotide-level information (e.g., precisely localizing the effect of a single point mutation), which is a genuinely active area of architectural research and debate rather than a fully settled question.

**Follow-ups:**
- Why might single-nucleotide resolution be particularly important for certain downstream genomic tasks (e.g., precisely localizing the functional impact of a specific point mutation), and how would you evaluate whether a coarser tokenization scheme is losing meaningful information for such a task?

---

### Q: How does tokenization for protein sequences typically differ from DNA sequence tokenization, and why? 🟢

**Answer:**
Protein sequences are composed of a much smaller "alphabet" (the 20 standard amino acids, versus DNA's 4 nucleotide bases), and are typically tokenized at the **single amino-acid-residue level** as the standard default approach, rather than requiring k-mer or learned subword tokenization schemes as commonly explored for DNA.

This is more tractable than DNA's single-nucleotide tokenization concern because protein sequences are inherently much shorter than the genomic regions of interest for DNA foundation models (a typical protein might be a few hundred residues, versus genomic regions of interest that can span many thousands to millions of base pairs) — so the sequence-length/compute-cost tradeoff that motivates coarser tokenization schemes for DNA is generally less pressing for typical single-protein modeling tasks, though it becomes more relevant again for tasks involving very long proteins, multi-protein complexes, or genomic context surrounding a gene.

**Follow-ups:**
- How might tokenization strategy need to change for a model intended to jointly represent both a gene's DNA sequence and its resulting protein product?

---

### Q: What modifications to the standard transformer attention mechanism have been explored to handle very long biological sequences (e.g., whole-genome-scale context), and what problem are they solving? 🔴

**Answer:**
Standard self-attention has compute and memory cost that scales quadratically with sequence length, which becomes prohibitively expensive for genome-scale context (potentially spanning many thousands to millions of tokens) using a naive full-attention transformer architecture.

Approaches explored to address this (an active area of ongoing architectural research, not a single settled solution): **sparse attention mechanisms** that restrict each token to attend only to a structured subset of other tokens (e.g., local windows plus some longer-range connections) rather than the full sequence, reducing compute cost at some potential cost to capturing arbitrary long-range dependencies; **hierarchical/multi-scale architectures** that process sequence information at multiple resolutions (e.g., coarser summarized representations for distant context, finer resolution for local context); and **alternative sequence architectures** (e.g., state-space models and related non-attention-based sequence architectures) that have been explored specifically because they can achieve better scaling behavior with sequence length than standard quadratic attention, while still aiming to capture relevant long-range dependencies. The right choice involves a real tradeoff between computational tractability at genome scale and the risk of degrading the model's ability to capture genuinely important long-range genomic dependencies (e.g., distant regulatory elements affecting a gene far away in sequence position).

**Follow-ups:**
- Why might long-range dependencies be particularly biologically important for genomic sequence modeling specifically (as opposed to, say, protein sequence modeling), motivating extra architectural effort to handle very long context efficiently?

---

### Q: How do structure-aware architectures (e.g., models incorporating graph neural network components alongside sequence-based transformers) differ from purely sequence-based protein models, and when might you want to incorporate structural information directly into the architecture rather than relying on sequence alone? 🔴

**Answer:**
Purely sequence-based protein models (e.g., standard protein language models) learn representations directly from amino acid sequence alone, implicitly capturing structural/functional constraints only insofar as they're reflected in evolutionary sequence patterns (see section 1). Structure-aware architectures instead explicitly incorporate 3D structural information (e.g., representing a protein as a graph where nodes are residues and edges reflect spatial proximity, processed with graph neural network components, sometimes combined with sequence-based transformer components in a hybrid architecture).

You might specifically want explicit structural information incorporated into the architecture when: **the downstream task is fundamentally about spatial/structural relationships** (e.g., predicting protein-protein or protein-ligand binding interfaces, where relevant residues may be far apart in sequence but close in 3D space) that a purely sequence-based model would need to implicitly and imperfectly infer rather than directly represent; or when **reliable structural information is actually available** (from experimental structures or high-confidence predictions) and would provide a more direct, information-rich training signal than relying purely on the indirect evolutionary-sequence proxy for structural constraints. The tradeoff is that structure-aware architectures require structural data (experimental or predicted) to be available and sufficiently reliable, whereas purely sequence-based models can be pretrained on vastly larger sequence-only datasets without this dependency.

**Follow-ups:**
- If you only have high-confidence predicted structures (not experimentally solved ones) for the majority of your training data, how would you think about the risk of a structure-aware model learning to fit artifacts or biases of the structure-prediction method rather than genuine biological signal?

---

### Q: What is the "inductive bias" of a model architecture, and how would you reason about choosing an appropriate inductive bias for a biological sequence foundation model? 🟡

**Answer:**
Inductive bias refers to the built-in assumptions a model architecture encodes about the structure of the data or task, which shape what kinds of patterns the model can learn efficiently from a given amount of data — e.g., convolutional neural networks have a built-in inductive bias toward local, translation-invariant patterns (useful for image data), while standard transformers have a comparatively weaker, more general-purpose inductive bias (relying more on learned attention patterns and generally requiring more data to learn effectively without a strong built-in structural assumption).

For biological sequence data, reasoning about appropriate inductive bias might include: considering whether **local sequence motifs** (suggesting some value in architectural components with strong local/convolutional inductive biases) or **long-range, potentially non-local dependencies** (suggesting a stronger role for full attention-based mechanisms, despite their higher computational cost) are more important for the specific biological signal of interest; and considering **equivariance properties** relevant to the data — e.g., architectures for 3D structural data are often specifically designed to be equivariant to rotations/translations of the molecule in 3D space, since a protein's function shouldn't depend on its arbitrary orientation in a coordinate system, and building this invariance directly into the architecture (rather than requiring the model to learn it purely from data) can substantially improve data efficiency and generalization for structural tasks.

**Follow-ups:**
- Why might a rotation/translation-equivariant architecture be particularly valuable (rather than just a nice-to-have) for a model trained on relatively limited amounts of 3D structural data, compared to a model with no such built-in equivariance?
