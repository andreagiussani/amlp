# Lecture 01 — Dataset creation matters more than the model

## TL;DR
Most “model wins” come from **better data representation**:
- text features (TF vs TF-IDF)
- scaling/normalization
- imputation + leakage-safe pipelines

A stronger model on weak features just overfits faster.

---

## Goal
By the end of this lecture you can explain (and justify) why:
1) preprocessing choices often beat model swaps  
2) the same model can jump in performance with better features  
3) pipelines prevent accidental leakage

---

## The core idea
A model can only learn patterns that are **available in the dataset**.

So the practical question isn’t:
> “Which model should I use?”

It’s:
> “What information did I make available to the model, and how clean/consistent is it?”

---

## Example 1 — TF vs TF-IDF (Text)

### Setting
Binary text classification (spam/ham, ticket routing, sentiment, etc.)

### TF (Term Frequency)
Counts tokens in each document.

**Typical failure mode**
Very frequent terms dominate (generic words, boilerplate, signatures).  
The model spends capacity on words that don’t discriminate.

### TF-IDF
Downweights terms frequent across the corpus and upweights terms that are **specific**.

**Intuition**
- TF: frequent *here*
- IDF: rare *overall*
- TF-IDF: “frequent here AND rare overall” → often more informative

**Practical impact**
Keeping the model fixed (e.g., Logistic Regression), TF-IDF can outperform TF by a lot.

**Takeaway**
Good features make “simple models” look smart.

---

## Example 2 — Normalization / Scaling (Numeric)

### Setting
Features have different scales:
- `age` (0–100)
- `income` (0–300000)
- `distance_km` (0–3000)

### Why it matters
Scale-sensitive models include:
- k-NN
- SVM
- regularized linear models
- neural nets

If you don’t scale, big-magnitude features dominate distance/loss and the model behaves oddly.

### Common options
- **Standardization (z-score)**: mean 0, std 1  
  Good default for many models.
- **Normalization (L2)**: vectors with norm 1  
  Common in NLP (cosine similarity setups).

**Takeaway**
Scaling is often the difference between “model unstable” and “model works”.

---

## Example 3 — Imputers (Missing values)

### Setting
You have missing values in important columns.

### Bad choices
- Dropping rows → less data + possible bias (missing might not be random)
- Imputing using full dataset (train+test) → leakage

### Solid baseline strategies
1) **SimpleImputer** (median / most_frequent)  
   Strong baseline and cheap.
2) **Missing indicator**  
   Sometimes “missing” is information (sensor offline, user skipped a field).
3) **More advanced** (KNN / Iterative imputation)  
   Useful, but higher leakage risk if not done correctly.

**Takeaway**
Imputation is part of dataset design, not a “cleanup detail”.

---

## Golden rule — Put preprocessing inside a pipeline
All transforms must be:
- **fit on training only**
- applied to validation/test

Otherwise you get optimistic metrics and a model that fails in production.

---

## Checklist
- [ ] I have a clean baseline (simple model, correct split).
- [ ] Text features: TF-IDF considered (not only raw counts).
- [ ] Numeric features: scaling strategy chosen.
- [ ] Missing values: imputation + (optional) indicator.
- [ ] Everything is leakage-safe (pipeline, fit on train only).

---

## Next
Lecture 02 — Train/Validation/Test: leakage, target leakage, and why your score lies
