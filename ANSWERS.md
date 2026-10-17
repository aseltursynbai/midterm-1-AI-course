# Midterm 1 Answers

**GitHub username:** aseltursynbai
**Session code:** kettle-62
## Part 1: Retrieval and cost decision
### 1a & 1b. Retrieval Metrics & Candidates Summary
| Candidate / Config | Recall@1 | Recall@3 | Recall@5 | MRR@10 | 95% Wilson Interval (Recall@k) | Annual Cost ($) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Config A** (Paragraph, ~256 t) | 0.433 | 0.650 | 0.817 | 0.569 | — | — |
| **Config B** (Sentence, ~64 t) | 0.383 | 0.583 | 0.733 | 0.510 | — | — |
| **Candidate (A, 3)** | — | **0.650** | — | — | [0.524, 0.758] | $5,622.30 |
| **Candidate (A, 5)** | — | — | **0.817** | — | [0.701, 0.894] | $8,241.02 |
| **Candidate (B, 3)** | — | **0.583** | — | — | [0.457, 0.699] | $2,270.76 |
| **Candidate (B, 5)** | — | — | **0.733** | — | [0.608, 0.828] | $2,654.12 |

### 1c. Annual Cost Model Details
* **Budget limit:** $8,300 / year
* **Requests per day:** 7,500 (2,737,500 / year)
* **Traffic shares & Costs per request:**
  * **EN (32%):**
    * Config (A,3): input = 206 + 39 + 3×242 = 971 t; cost = (971×1 + 97×5)/1M = $0.001456
    * Config (A,5): input = 206 + 39 + 5×242 = 1455 t; cost = (1455×1 + 97×5)/1M = $0.001940
    * Config (B,3): input = 206 + 39 + 3×77 = 476 t; cost = (476×1 + 97×5)/1M = $0.000961
    * Config (B,5): input = 206 + 39 + 5×77 = 630 t; cost = (630×1 + 97×5)/1M = $0.001115
  * **RU (33%):**
    * Config (A,3): input = 206 + 67 + 3×414 = 1515 t; cost = (1515×1 + 166×5)/1M = $0.002345
    * Config (A,5): input = 206 + 67 + 5×414 = 2343 t; cost = (2343×1 + 166×5)/1M = $0.003173
    * Config (B,3): input = 206 + 67 + 3×132 = 669 t; cost = (669×1 + 166×5)/1M = $0.001499
    * Config (B,5): input = 206 + 67 + 5×132 = 933 t; cost = (933×1 + 166×5)/1M = $0.001763
  * **KK (35%):**
    * Config (A,3): input = 206 + 112 + 3×697 = 2409 t; cost = (2409×1 + 279×5)/1M = $0.003804
    * Config (A,5): input = 206 + 112 + 5×697 = 3803 t; cost = (3803×1 + 279×5)/1M = $0.005198
    * Config (B,3): input = 206 + 112 + 3×222 = 984 t; cost = (984×1 + 279×5)/1M = $0.002379
    * Config (B,5): input = 206 + 112 + 5×222 = 1428 t; cost = (1428×1 + 279×5)/1M = $0.002823

---

### 1d. Recommended Decision

**Chosen candidate:** (A, 5)

Candidate (A, 5) is within the $8,300 annual budget at $8,241.02/year and achieves the highest point recall of 0.817 (95% CI: [0.701, 0.894]). Its confidence interval strictly exceeds Candidate (B, 3) (CI: [0.457, 0.699]) without overlap, confirming a statistically significant retrieval improvement. While its interval overlaps with Candidate (A, 3) (recall 0.650, CI: [0.524, 0.758]) and Candidate (B, 5) (recall 0.733, CI: [0.608, 0.828]), (A, 5) remains the top-performing model under budget. Per-language recall for (A, 5) is: English 85.0% (17/20), Russian 80.0% (16/20), Kazakh 80.0% (16/20). Russian and Kazakh users are served slightly worst due to higher tokenization sub-word fragmentation.

### 1e. Concrete Next Measurement

Increase the evaluation test set from 60 to 300 questions (100 per language) to shrink the 95% Wilson confidence intervals from ~±0.10 to ~±0.04. This will determine if the observed accuracy gain of (A, 5) over the much cheaper Candidate (B, 5) ($2,654.12/yr) is statistically significant, potentially saving $5,586 annually.
## Part 2: A document pipeline you can defend
### 2a. Pipeline Comparison Table
| Pipeline | Overall Accuracy | Weakest Cell (Lang, Cond) | Weakest Accuracy | 95% Wilson Interval | Annual Cost ($) | PII Pages Leaving Country |
| :--- | :---: | :--- | :---: | :---: | :---: | :---: |
| **Pipeline H** (Hosted API Abroad) | **87.8%** (158/180) | Kazakh, degraded | **70.0%** (21/30) | [0.521, 0.833] | $17,041.95 | **459,900** |
| **Pipeline L** (On-Premises Model) | **80.0%** (144/180) | Kazakh, degraded | **70.0%** (21/30) | [0.521, 0.833] | **$10,800.00** | **0** |
*Calculation details:*
* **Volume:** 7,000 pages/day × 365 = 2,555,000 pages/year.
* **PII Pages:** 2,555,000 × 0.18 = 459,900 pages/year.
* **Pipeline H Image Tokens:** $\lceil 1654 / 28 \rceil \times \lceil 2339 / 28 \rceil = 60 \times 84 = 5,040$ input tokens per page.
* **Pipeline H Cost per Page:** $(5040 \times 1 + 324 \times 5) / 1,000,000 = \$0.00667$.
* **Pipeline H Annual Cost:** $2,555,000 \times \$0.00667 = \$17,041.85$.
* **Pipeline L Capacity:** $\lceil 7000 / 4000 \rceil = 2$ servers needed[cite: 9].
* **Pipeline L Annual Cost:** $2 \times \$450 \times 12 = \$10,800.00$[cite: 9].
### 2b. Memo to Head of Operations
**MEMORANDUM**
**To:** Head of Operations  
**From:** AI Systems Engineer  
**Subject:** Selection and Risk Analysis for Automated Invoice OCR Pipeline  
Both document pipelines exhibit noticeable performance degradation on lower-quality scans. Pipeline H achieves 87.8% overall accuracy but drops to 70.0% (95% CI: [0.521, 0.833]) on degraded Kazakh documents. Pipeline L reaches 80.0% overall accuracy and shares the exact same weakest point on degraded Kazakh documents at 70.0% (95% CI: [0.521, 0.833]). Aggregate accuracy metrics conceal this substantial failure rate for non-English, degraded inputs.
Under Kazakhstan Law No. 94-V on Personal Data, 459,900 pages annually carrying personal names and ИИН (18% of volume) are legally sensitive[cite: 9]. Applying the three regulatory design questions:
1. **Where does the scan go?** Pipeline H transmits raw document images across borders to foreign data centers; Pipeline L processes all scans locally inside Kazakhstan[cite: 9].
2. **Who stores it?** Pipeline H relies on an external third-party foreign vendor; Pipeline L retains storage within the bank's sovereign infrastructure[cite: 9].
3. **For how long?** Foreign vendor retention periods are unverified and subject to foreign jurisdiction; local retention is controlled directly by bank policy.
We must verify vendor logging policies, transport encryption (TLS 1.3), data residency guarantees, and local server capacity limits under load spikes. To enforce compliance, checkable safeguards include an automated local regex filter pre-scrubbing ИИН patterns prior to transmission, and end-to-end audit logging.
**Recommendation:** Select **Pipeline L**. Although Pipeline H offers slightly higher average accuracy (87.8% vs 80.0%), sending 459,900 personal data records abroad violates compliance requirements under Law No. 94-V. Pipeline L keeps all personal data within Kazakhstan, eliminates legal exposure, and saves $6,241.95 annually ($10,800 vs $17,041.95). Residual risk includes a 30.0% extraction failure rate on degraded Kazakh scans, requiring a human-in-the-loop review workflow for failed invoices.


## Part 3: Explain your own numbers

### Q1
* **Number:** 2.82
* **Why:** Kazakh uses 2.64× more total tokens than English due to tokenizer vocabulary fragmentation. Because output tokens cost 3.33× input tokens ($5 vs $1.50 per million)[cite: 10], the higher output token ratio increases the bill ratio beyond the raw token count ratio.

### Q2
* **Number:** 22.2%
* **Why:** 640 cl100k tokens expand to $640 \times (263/256) = 657.5$ e5 tokens for English. Since e5-small truncates context at 512 tokens[cite: 10], $657.5 - 512 = 145.5$ tokens are dropped ($145.5 / 657.5 = 22.2\%$). Russian and Kazakh are also truncated, losing ending context.

### Q3
* **Number:** 51.6%
* **Why:** Dividing logits by $T=0.7$ increases their differences before Softmax, steepening the probability distribution. This concentrates probability mass onto the top token ("Monday"), raising its probability from 41.5% to 51.6% while preserving original rank order.

### Q4
* **Number:** +22
* **Why:** Public leaderboards reward raw accuracy without penalizing errors, encouraging models to guess. Model Y's cautious strategy gets penalized less (-2 per wrong vs +1 correct), yielding a penalized score of +29 vs Model X's +7. Leaderboards teach models to hallucinate guesses.

### Q5
* **Number:** 12.65
* **Why:** 17.71 trillion tokens / 70B parameters = 253 tokens/parameter. Chinchilla's optimal ratio is 1.4T / 70B = 20 tokens/parameter ($253 / 20 = 12.65$). The model is heavily over-trained relative to Chinchilla compute-optimality, matching LLaMA-style inference optimization.
