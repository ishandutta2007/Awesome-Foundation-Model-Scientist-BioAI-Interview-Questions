# Additional Resources

A short list of resources for going deeper on foundation models for biology. (Not exhaustive — PRs welcome.) This is one of the fastest-moving subfields in ML — treat any specific model, benchmark, or architecture mentioned here as a snapshot in time, and check recent preprints before an interview.

## Foundational Reading
- Key protein language model papers (e.g., the ESM family of models and related work) — read at least one foundational paper in depth, including its evaluation methodology and stated limitations, not just its headline results.
- Key genomic sequence foundation model papers (e.g., work on Nucleotide Transformer-style models and related long-context genomic language models).
- Key single-cell foundation model papers (e.g., work on Geneformer, scGPT, and related approaches) for the single-cell-specific modeling and evaluation challenges discussed in section 5.
- General deep learning foundations: transformer architecture papers, scaling laws papers (from the broader NLP/vision literature), and self-supervised learning survey papers, since this role assumes strong general ML research fundamentals as a base.

## Key Benchmarks & Datasets (good to be conversationally familiar with)
- Established protein function/structure/mutational-effect benchmark suites used widely in the protein language model literature.
- UniProt, Pfam, and PDB as foundational sequence/structure/annotation databases commonly used for pretraining and evaluation.
- Large-scale public single-cell atlases used for pretraining and benchmarking single-cell foundation models.

## Communities & Discussion
- Machine Learning for Structural Biology (MLSB) and similar workshop venues (e.g., at NeurIPS, ICML) for a sense of current research directions and open problems.
- bioRxiv and arXiv's q-bio category for the latest preprints — this field moves fast enough that published, peer-reviewed papers alone will lag meaningfully behind current frontier research.
- r/MachineLearning and r/bioinformatics for broader discussion threads.

## Companion Repos (adjacent, complementary content)
- **Computational Biologist** — core algorithms, structural/systems biology, statistical methods.
- **Genomics Data Scientist** — NGS pipelines, variant calling, statistical genomics.
- **AI Drug Discovery Scientist** — cheminformatics, generative molecule design, structure-based design.
- **ML Engineer (Biotech)** — production engineering, MLOps, and infrastructure for deploying models built using the research covered in this repo.

## Staying Current
- This is genuinely one of the fastest-evolving areas in applied ML research — new architectures, pretraining objectives, and benchmarks appear frequently. Prioritize following active research groups and recent preprints over relying on any static reference, including this repo, for anything specific.
- Dual-use and responsible-release norms for powerful biological design models are actively evolving through both scientific community discussion and emerging policy — check current guidance from relevant scientific bodies and policy organizations if this is directly relevant to your work, rather than relying on a fixed personal understanding formed at one point in time.
