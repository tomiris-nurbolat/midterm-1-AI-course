# Midterm 1: answers

GitHub username: tomiris-nurbolat 
Session code: practice 
Fingerprint: bfd9a65e

Fill in the three lines above from my_data/DATA.md. Do not rename headings or table columns: a script reads the numbers. Write only in the empty cells and lines. Percentages as 0.78 or 78 %, money as plain numbers.

AI-use declaration
I used Claude (claude.ai) as an AI assistant throughout this exam. Specifically:

Part 1: Claude computed recall@k and MRR@10 from retrieval_ranks.csv, calculated Wilson confidence intervals using the formula from the README, and computed annual costs for each candidate using the cost formula. I used the results to construct the decision.
Part 2: Claude read doc_results.csv, computed overall and per-cell accuracy, Wilson intervals, and annual costs for both pipelines. The memo text was drafted with AI assistance.
Part 3 (Q1–Q5): Claude performed all arithmetic (bill ratio, e5 token counts, softmax with temperature, penalty scores, conversation token sums). I verified each step and explained the reasoning.
I reviewed all numbers and explanations to ensure I can explain them in the oral check.

Part 1: Retrieval and cost decision
1a. Metrics (all 60 questions)
config	recall@1	recall@3	recall@5	MRR@10
A	0.5833	0.6833	0.7667	0.6425
B	0.5667	0.6833	0.7333	0.6371
1b. Recall and 95 % Wilson interval of each candidate (recall at its own k)
candidate	recall@k	lower	upper
A, k=3	0.6833	0.5577	0.7869
A, k=5	0.7667	0.6456	0.8556
B, k=3	0.6833	0.5577	0.7869
B, k=5	0.7333	0.6099	0.8287
1c. Annual model cost in USD
candidate	annual cost
A, k=3	19084
A, k=5	24764
B, k=3	13115
B, k=5	14815
1d. Decision (at most 150 words)
Chosen candidate: B, k=3

(A, k=5) exceeds the $21,900 budget and is eliminated. The three remaining candidates — (A, k=3), (B, k=3), (B, k=5) — are statistically indistinguishable: all Wilson intervals overlap with the best-recall candidate (B, k=5). Because no candidate is shown to outperform the others, I choose the cheapest in the tied pool: (B, k=3) at $13,115/year, saving $5,969 over (A, k=3) and $1,700 over (B, k=5) with no measurable recall penalty.

Per-language recall@3 for (B, k=3): EN 80 %, RU 65 %, KK 60 %. Kazakh-speaking users are served worst: only 6 of every 10 questions retrieve the correct chunk.

1e. Next measurement (at most 60 words)
Have human judges rate final answer quality for (B, k=3) versus (B, k=5) on a stratified sample of KK questions. Retrieval recall shows which chunk was found, not whether the answer improved. A quality evaluation would reveal whether the extra chunk in k=5 earns its cost for the weakest language.

Part 2: A document pipeline you can defend
2a. Numbers
pipeline	overall accuracy	weakest cell (language, condition)	accuracy there	lower	upper	annual cost
H	0.9000	KK, degraded	0.8333	0.6644	0.9266	12266
L	0.7778	RU, degraded	0.7000	0.5212	0.8334	10800
Personal-data pages per year sent abroad under H: 180675

2b. Memo (at most 300 words)
To: Head of Operations Re: Invoice pipeline selection

Accuracy. Pipeline H achieves 90.0 % overall accuracy; Pipeline L achieves 77.8 %. H's weakest group is (KK, degraded) at 83.3 % [0.664, 0.927]. L's weakest group is (RU, degraded) at 70.0 % [0.521, 0.833]. These intervals do not overlap, so H demonstrably serves its weakest group better than L serves its weakest group.

Cost. Annual cost: H $12,266; L $10,800. The $1,466 saving from L does not justify a gap of more than 13 percentage points on the weakest group.

Personal-data constraint (three questions from L5.1 applied to H). Where does the scan go? Each page is sent as an image to a data centre abroad, outside Kazakhstan. Who stores it and for how long? The vendor's retention policy is unknown; we have a pricing agreement, not a data-processing agreement — this must be verified. What data are at risk? 9 % of pages (≈180,675 pages/year) carry an individual's name and ИИН. Kazakhstan's Law No. 94-V classifies the ИИН as personal data, so cross-border transfer requires legal review. Pipeline L keeps all pages on the bank's own server in Kazakhstan; all three questions are answered satisfactorily by design.

Safeguards (verifiable). If H were selected: (1) sign a Data Processing Agreement specifying retention ≤30 days and no secondary use — verifiable by legal audit; (2) automatically mask the ИИН before transmission for pages containing personal data — verifiable by a pre-processing log review.

Recommendation: Pipeline L. The accuracy difference is real, and H's cross-border data flow introduces legal risk that is not yet resolved. The residual risk: L's 70 % accuracy on degraded RU/KK invoices means approximately 30 % of those documents will require manual review — staffing must account for this workload.

Part 3: Explain your own numbers
Q1
Number: 2.54

Why (at most 40 words): Input tokens cost $1/M and output tokens $5/M. KK has proportionally fewer expensive output tokens (4.3 % of its total) than EN (5.8 %), so KK's average token is cheaper, making the bill ratio lower than the raw token ratio.

Q2
Number: 2.7 %

Why (at most 40 words): Only EN is cut: 512 cl100k tokens → 526 e5 tokens, exceeding the 512-token limit by 14 (2.7 % of 526). RU produces 256 e5 tokens and KK 152 — both fit within 512. The cut tail is simply never encoded into the embedding.

Q3
Number: 91.9 %

Why (at most 40 words): Temperature T = 0.5 sharpens the distribution by dividing scores before softmax, amplifying differences between logits. The ranking of tokens is unchanged (Monday remains top). All five tokens can still be sampled, but low-scoring tokens become extremely unlikely (< 0.3 %).

Q4
Number: 0

Why (at most 40 words): The public leaderboard (no penalty) prefers Model X (70 vs 64 correct). This teaches a model to always guess rather than abstain — guessing costs nothing on the leaderboard but earns points, even if wrong answers are penalised equally under a fairer scoring rule.

Q5
Number: 26220

Why (at most 40 words): Turn i bills 95 × i tokens, so total = 95 × (1 + 2 + … + 23) = 95 × 276 = 26,220. The first message is billed 23 times. Prompt caching charges repeated prefix tokens at a reduced rate, so long histories become much cheaper.
