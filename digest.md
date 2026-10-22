# Transformer Study Guide — "Attention Is All You Need"

## Why this paper matters

Vaswani et al. (2017), "Attention Is All You Need" (arXiv:1706.03762), introduced the Transformer: the first sequence transduction architecture to rely **solely on attention mechanisms, dispensing with recurrence and convolutions entirely**. Every dominant model since — BERT, GPT, T5, and every modern LLM, plus the encoders inside diffusion and multimodal systems — is a Transformer or a direct descendant. The reason it won is not just accuracy but *trainability*: by removing the sequential dependency of recurrence, the architecture parallelizes across the whole sequence, which is exactly what made scaling on GPUs (and later TPUs) practical. Reading the source gives you the vocabulary that the entire modern NLP stack still uses: queries, keys, values, multi-head attention, positional encodings, residuals + layer norm, and the encoder–decoder split. If your group understands this one paper, most later architecture papers read as deltas on it.

## Key numbers

Quoted exactly as stated in the paper:

> "Our model achieves 28.4 BLEU on the WMT 2014 English-to-German translation task, improving over the existing best results, including ensembles, by over 2 BLEU."

> "On the WMT 2014 English-to-French translation task, our model establishes a new single-model state-of-the-art BLEU score of 41.8 after training for 3.5 days on eight GPUs, a small fraction of the training costs of the best models from the literature."

Training cost and BLEU, as reported in Table 1 (newstest2014):

| Model | EN-DE BLEU | EN-FR BLEU | Training cost (FLOPs) |
| --- | --- | --- | --- |
| Transformer (base) | 27.3 | 38.1 | 3.3·10^18 |
| Transformer (big) | **28.4** | **41.8** | 2.3·10^19 |

Supporting training-cost detail from Section 5.2: 8 NVIDIA P100 GPUs; base models trained 100,000 steps (~0.4 s/step, 12 hours total); big models trained 300,000 steps (~1.0 s/step, 3.5 days total).

Context for those costs (also Table 1): GNMT + RL Ensemble was 1.8·10^20 FLOPs for 26.30 EN-DE; ConvS2S Ensemble was 7.7·10^19 for 26.36 EN-DE — so even the base Transformer beat them at roughly 1/20th the compute.

(Note for close readers: the abstract and Table 1 report 41.8 for the big model on EN-FR, while the Section 6.1 text mentions 41.0 — a known minor inconsistency between the abstract and the results prose; either way it was the single-model state of the art at a fraction of the cost.)

## Paper-to-video map

Each shortlisted video from the chosen channel (3Blue1Brown) paired with the paper concept it complements:

| Video (3Blue1Brown) | ID / Link | Paper concept it complements |
| --- | --- | --- |
| "Large Language Models explained briefly" | [LPZh9BOjkQs](https://www.youtube.com/watch?v=LPZh9BOjkQs) | Section 4, "Why Self-Attention" — the O(1) sequential-operations / parallelism argument, framed as "before 2017, most language models would process text one word at a time... transformers soak it all in at once, in parallel." Best first watch: orients the whole cycle in 8 minutes. |
| "Transformers, the tech behind LLMs \| Deep Learning Chapter 5" | [wjZofJX0v4M](https://www.youtube.com/watch?v=wjZofJX0v4M) | Section 3, "Model Architecture" — end-to-end data flow (tokenize → embeddings → alternating attention and MLP blocks → next-token distribution), matching the paper's Figure 1 pipeline; also situates the original encoder–decoder translation use case. |
| "Attention in transformers, step-by-step \| Deep Learning Chapter 6" | [eMlx5fFNoYc](https://www.youtube.com/watch?v=eMlx5fFNoYc) | Section 3.2, "Attention" — builds the query/key/value intuition behind scaled dot-product attention and multi-head attention, explicitly anchored to the 2017 "Attention is All You Need" paper. The visual complement to Equations 1–3. |
| "How might LLMs store facts \| Deep Learning Chapter 7" | [9-Jl0dxWQs8](https://www.youtube.com/watch?v=9-Jl0dxWQs8) | Section 3.3, "Position-wise Feed-Forward Networks" — the paper's most under-explained component, which holds the majority of parameters. Gives a concrete toy example (storing "Michael Jordan plays basketball") of what the FFN sub-layer does beyond attention. |

Suggested viewing order after finishing the paper: Chapter 6 (attention, the core mechanism), Chapter 5 (full pipeline), Chapter 7 (the FFN half), then the brief LLM video as a wrap-up of why the architecture took over.

---

Companion tracker: "Transformer Study Tracker" (Google Sheets) — one row each for the paper, the sections read, and the shortlisted videos.