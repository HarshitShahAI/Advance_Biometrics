# Advanced Biometric Systems and Security (AI461)
## Assignment 1 – User Authentication using Biometric Features

**Student:** U23AI075
**Course:** Advanced Biometric Systems and Security (AI461)
**Assignment:** Assignment 1
**Dataset:** `biomet_data.csv`

---

## 1. Objective

The objective of this assignment is to evaluate the performance of a biometric authentication system using biometric feature vectors.

The dataset contains:

- 100 users
- 10 samples per user
- 144 features per sample

For every user:

- First 5 samples are used for enrollment.
- Remaining 5 samples are used for testing.

A representative biometric template is generated for every user by taking the mean of their 5 enrollment feature vectors.

Each test sample is compared with all 100 user templates using:

1. Euclidean Distance
2. Cosine Similarity

The resulting genuine and impostor scores are used to evaluate the biometric authentication system.

---

## 2. Experimental Setup

### Dataset

| Parameter | Value |
|---|---:|
| Number of users | 100 |
| Samples per user | 10 |
| Enrollment samples per user | 5 |
| Test samples per user | 5 |
| Features per sample | 144 |
| Total samples | 1000 |
| Genuine comparisons | 500 |
| Impostor comparisons | 49,500 |

The program produced the following data shapes:

```
Raw data shape: (144, 1000)
Final data shape: (1000, 144)

Enrollment shape: (100, 5, 144)
Test shape: (100, 5, 144)

Templates shape: (100, 144)
```

For every test sample:

- 1 comparison is made with the correct user's template.
- 99 comparisons are made with other users' templates.

Therefore:

```
Genuine comparisons  = 100 × 5
                     = 500

Impostor comparisons = 100 × 5 × 99
                     = 49,500
```

---

## 3. Evaluation Metrics

The biometric system is evaluated using the following:

**A. Genuine Distribution**
The distribution of matching scores obtained when a test sample is compared with the template belonging to the same user.

**B. Impostor Distribution**
The distribution of matching scores obtained when a test sample is compared with templates belonging to other users.

**C. FAR Plot**
False Acceptance Rate (FAR) measures the percentage of impostor attempts that are incorrectly accepted.

**D. FRR Plot**
False Rejection Rate (FRR) measures the percentage of genuine attempts that are incorrectly rejected.

**E. Receiver Operating Characteristic (ROC)**
The ROC curve shows the relationship between:
- False Acceptance Rate (FAR)
- True Acceptance Rate (TAR)

The Area Under the ROC Curve (AUC) is also calculated.

**F. Equal Error Rate and Decidability Index**
The following summary measures are calculated:
- Equal Error Rate (EER)
- EER score/threshold
- Decidability Index (d')

A lower EER indicates better authentication performance, while a higher decidability index indicates better separation between genuine and impostor distributions.

---

## 4. Genuine Distribution

The genuine distribution is built from the 500 genuine comparisons — each test sample matched against its own user's template. A tightly clustered genuine distribution, sitting clearly apart from the imposter scores, is a sign of a well-behaved system.

**Euclidean Distance — Genuine Scores**

| Metric | Value |
|---|---:|
| Mean | 291.616047 |
| Std | 135.595397 |

Genuine distances average around 291.6, which is exactly what's expected — a test sample should sit fairly close to its own template. The spread (std ≈ 135.6) shows there's still some natural variation across users and samples.

**Cosine Similarity — Genuine Scores**

| Metric | Value |
|---|---:|
| Mean | 0.989501 |
| Std | 0.010968 |

Genuine cosine scores cluster tightly near 1 (mean ≈ 0.9895), with a very small standard deviation (≈ 0.011) — meaning genuine matches are consistently high-confidence across nearly all users.
---

## 5. Imposter Distribution

The imposter distribution is built from all 49,500 imposter comparisons — every test sample matched against every other user's template. Ideally, this distribution sits far away from the genuine one, with minimal overlap.

**Euclidean Distance — Imposter Scores**

| Metric | Value |
|---|---:|
| Mean | 703.822776 |
| Std | 301.794848 |

Imposter distances average around 703.8 — well above the genuine mean of 291.6 — confirming there's clear separation. The wider spread (std ≈ 301.8) reflects the huge variety of user-pairings being compared.

**Cosine Similarity — Imposter Scores**

| Metric | Value |
|---|---:|
| Mean | 0.960376 |
| Std | 0.018367 |

Imposter similarity averages around 0.960, noticeably lower than the genuine mean of 0.9895. The gap looks small in absolute terms, but that's simply because cosine similarity operates on a compressed 0–1 scale — the relative separation (captured properly by EER and d′ later) is what actually matters.

## 6. FAR — False Accept Rate

FAR is how often an imposter accidentally gets accepted as genuine.

```
Euclidean: FAR = P(Imposter Distance ≤ Threshold)
Cosine:    FAR = P(Imposter Similarity ≥ Threshold)
```

Lower FAR means better security — fewer intruders sneaking through.

---

## 7. FRR — False Reject Rate

FRR is how often a genuine user gets wrongly turned away.

```
Euclidean: FRR = P(Genuine Distance > Threshold)
Cosine:    FRR = P(Genuine Similarity < Threshold)
```

Lower FRR means a smoother experience for legitimate users — fewer unnecessary rejections.

---

## 8. ROC Curve

The ROC curve plots:

```
X-axis → False Accept Rate (FAR)
Y-axis → Genuine Accept Rate (GAR), where GAR = 1 − FRR
```

The closer the curve sits to the top-left corner, the better the system is at telling genuine and imposter attempts apart. Comparing the Euclidean and cosine ROC curves side by side gives a visual sense of which one discriminates better overall.

---

## 9. Equal Error Rate (EER)

EER is the point where FAR and FRR are roughly equal — a single number that sums up how balanced (and how accurate) the system is. Lower is better.

```
Euclidean EER = 11.5556%
Cosine EER    = 8.4616%
```

Cosine similarity comes out ahead by about 3.09 percentage points, meaning it hits a better balance between letting genuine users in and keeping imposters out.

---

## 10. Decidability Index

This measures how cleanly the genuine and imposter distributions are separated from each other:

```
        |μg − μi|
d' = ─────────────────
     √[ (σg² + σi²) / 2 ]
```

where μg and μi are the genuine and imposter means, and σg and σi are their standard deviations. Basically — a bigger gap between the two means, combined with tighter (less spread-out) distributions, pushes d′ higher.

```
Euclidean d' = 1.761935
Cosine d'    = 1.925414
```

Higher is better here too, and cosine similarity wins again — its genuine and imposter scores are more distinctly separated.

---

## 11. Final Comparison

| Metric | Euclidean Distance | Cosine Similarity | Better |
|---|---:|---:|---|
| Genuine Mean | 291.616047 | 0.989501 | — |
| Imposter Mean | 703.822776 | 0.960376 | — |
| EER | **11.5556%** | **8.4616%** | **Cosine** |
| Decidability (d′) | **1.761935** | **1.925414** | **Cosine** |
| ROC-AUC | 0.949556 | 0.968943 | **Cosine** |

The raw means can't really be compared directly since Euclidean distance and cosine similarity sit on completely different scales. What matters is the pattern below:

```
                    Euclidean       Cosine
EER                  11.56%          8.46%
d'                    1.762          1.925
ROC-AUC               0.9496         0.9689
```

---

## 12. Which Matching Criterion Performs Best?

Cosine similarity comes out ahead, and by a fair margin. Here's why:

**1. Lower EER** — 11.56% for Euclidean vs. 8.46% for cosine. Fewer overall errors at the point where the system is most balanced.

**2. Higher decidability** — 1.76 vs. 1.93. Genuine and imposter scores are more clearly separated, leaving less room for confusion.

**3. Better discrimination by design** — cosine similarity cares about the direction of a feature vector rather than its raw magnitude, which tends to suit biometric data well, since a lot of the meaningful signal lives in the pattern/direction of the features rather than their scale.

---

## Conclusion

Between the two, cosine similarity is the better matching criterion for this biometric recognition system. It posted a lower EER (8.4616% vs. 11.5556%), a higher decidability index (1.925414 vs. 1.761935), and a higher ROC-AUC (0.968943 vs. 0.949556) — all three pointing the same direction.

Since a good biometric system is one with a low EER and a high decidability index, the results here make a pretty clear case:

> **Cosine similarity is the stronger matching criterion for this dataset.**
