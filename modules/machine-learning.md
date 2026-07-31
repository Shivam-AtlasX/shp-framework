# Machine Learning Module

## Purpose

Provides guidance on ML problem framing, model selection, pipeline design, evaluation, and production deployment. Bridges statistical rigour with engineering practice.

---

## Responsibilities

- Frame problems correctly (supervised/unsupervised/reinforcement)
- Advise on model and algorithm selection
- Design ML pipelines from data to serving
- Define evaluation metrics and methodologies
- Identify and diagnose common ML failure modes
- Advise on feature engineering and data quality
- Guide productionisation and monitoring

---

## Activation Criteria

Activate when:
- ML models, training, or prediction systems are discussed
- Classification, regression, clustering, or anomaly detection tasks arise
- Model evaluation, metrics, or performance is discussed
- "Train", "predict", "feature", "label", "model" are used in ML context
- Data science or analytics engineering is discussed

---

## Inputs

- Problem description and success criteria
- Data characteristics (volume, features, label availability, distribution)
- Performance requirements (accuracy, latency, throughput)
- Deployment environment
- Existing ML infrastructure and tooling

---

## Outputs

- Problem framing and model class recommendation
- Pipeline design
- Evaluation strategy and metric selection
- Identified risks and mitigation approaches

---

## Dependencies

- `modules/ai.md` — LLM and deep learning patterns
- `modules/performance.md` — computational efficiency
- `modules/system-design.md` — ML system architecture
- `modules/testing.md` — ML evaluation and testing

---

## Problem Framing

The first question is always: **is this actually an ML problem?**

```
Is there a clear objective function?              If no → define one first
Is there sufficient labelled data?                If no → consider alternatives
Is the pattern learnable from the data?           If no → ML won't help
Would a simple rule-based system suffice?         If yes → start simpler
Is ML the right tool, or the fashionable one?     Be honest
```

### Problem types

| Type | Description | Data requirement |
|---|---|---|
| Binary classification | Two-class prediction (spam/not spam, churn/no churn) | Labelled examples of both classes |
| Multi-class classification | N-class prediction (category, intent, language) | Labelled examples of all classes |
| Regression | Continuous value prediction (price, demand, duration) | Labelled continuous outputs |
| Ranking | Order items by relevance (search, recommendations) | Preference or click data |
| Clustering | Group similar items without labels | Unlabelled data |
| Anomaly detection | Identify unusual examples | Normal data (anomalies rare by definition) |
| Sequence labelling | Label each element in a sequence (NER, POS tagging) | Labelled sequences |
| Generation | Produce new content | Large unlabelled corpora (LLMs) or paired data |

---

## Algorithm Selection

### Classical ML (structured/tabular data)

| Task | First choice | When to try alternatives |
|---|---|---|
| Binary/multi-class | Gradient Boosting (XGBoost, LightGBM) | Neural net if > 1M rows with high-dimensional features |
| Regression | Gradient Boosting | Linear regression first as baseline |
| Ranking | LambdaMART / LightGBM with ranking objective | Neural ranking for very large corpora |
| Clustering | K-means first | DBSCAN for non-spherical clusters; hierarchical for small datasets |
| Anomaly detection | Isolation Forest | Autoencoder if data is high-dimensional |
| Time series | ARIMA (univariate, stationary) | LightGBM with lag features; Transformer for complex patterns |

**Rule:** Always train a simple baseline first (logistic regression, linear regression, majority class). If gradient boosting doesn't beat the baseline meaningfully, reconsider whether ML is the right approach.

### Deep Learning (unstructured data)

| Data type | Architecture |
|---|---|
| Text (classification, NER, QA) | Fine-tuned BERT-family or LLM |
| Text (generation) | Fine-tuned LLM or RAG |
| Images (classification) | Fine-tuned CNN (ResNet, EfficientNet) or ViT |
| Images (detection) | YOLO, Faster R-CNN |
| Audio | Whisper (speech), wav2vec (embeddings) |
| Tabular (with many features) | Usually gradient boosting wins; try TabNet or FT-Transformer |

---

## ML Pipeline

### Full pipeline stages

```
1. Data Collection
   └── Define what data is needed; identify sources

2. Data Validation
   └── Schema checks, data quality, drift detection

3. Exploratory Data Analysis (EDA)
   └── Distribution, correlation, class balance, missing values

4. Feature Engineering
   └── Transform raw data into model-ready features

5. Data Splitting
   └── Train / Validation / Test — strictly hold out test set

6. Model Training
   └── Start simple; iterate toward complexity

7. Evaluation (offline)
   └── Measure on held-out test set using correct metrics

8. Error Analysis
   └── Understand where and why the model fails

9. Deployment
   └── Batch inference or real-time serving

10. Monitoring
    └── Feature drift, prediction drift, ground truth labels
```

---

## Data Splitting

**Golden rule: the test set is sacred. It is touched exactly once.**

```
All data
├── Training set     (60–70%) — model sees this during training
├── Validation set   (15–20%) — hyperparameter tuning, model selection
└── Test set         (15–20%) — final evaluation; never used during development

For time-series data:
├── Training: data up to time T
├── Validation: data from T to T+1
└── Test: data from T+1 onwards
Never shuffle time-series data randomly.
```

**Data leakage** occurs when test set information influences training. Common causes:
- Normalising/scaling with statistics from the full dataset (including test)
- Temporal leakage: using future information as a feature
- Duplicate rows spanning train and test

---

## Evaluation Metrics

### Classification

| Metric | Formula | Use When |
|---|---|---|
| Accuracy | TP+TN / all | Balanced classes |
| Precision | TP / (TP+FP) | False positives are costly (spam filter) |
| Recall | TP / (TP+FN) | False negatives are costly (cancer detection) |
| F1 | 2×P×R / (P+R) | Balanced precision/recall, imbalanced classes |
| ROC-AUC | Area under ROC curve | General ranking quality |
| PR-AUC | Area under P-R curve | Imbalanced classes |

**Never report only accuracy on an imbalanced dataset.** A model predicting the majority class always achieves high accuracy while being useless.

### Regression

| Metric | Use When |
|---|---|
| MAE (Mean Absolute Error) | Easy to interpret; robust to outliers |
| RMSE (Root Mean Squared Error) | Penalises large errors more; sensitive to outliers |
| MAPE (Mean Absolute Percentage Error) | When relative error matters; fails if targets near zero |
| R² | Proportion of variance explained |

### Ranking

- NDCG (Normalised Discounted Cumulative Gain): position-weighted relevance
- MAP (Mean Average Precision): precision across recall levels
- MRR (Mean Reciprocal Rank): position of first relevant result

---

## Common Failure Modes

### Overfitting

**Signal:** Low training loss, high validation/test loss. Good training accuracy, poor generalisation.

**Causes:** Model too complex for the data volume; insufficient regularisation; training too long.

**Fixes:**
- More training data
- Regularisation (L1/L2, dropout, early stopping)
- Simpler model (fewer parameters, lower tree depth)
- Data augmentation

### Underfitting

**Signal:** High training loss. Model is too simple to capture the pattern.

**Fixes:**
- More complex model
- Better feature engineering
- Less regularisation

### Data Leakage

**Signal:** Suspiciously high performance during development that drops dramatically in production.

**Diagnosis:** Check for features derived from the label; check for temporal leakage; check for duplicate rows spanning splits.

### Class Imbalance

**Signal:** Model predicts majority class almost always; recall on minority class near zero.

**Fixes:**
- Oversampling minority class (SMOTE)
- Undersampling majority class
- Class weight adjustment in loss function
- Threshold optimisation (default 0.5 may not be optimal)

### Distribution Shift

**Signal:** Model performance degrades after deployment, gradually or suddenly.

**Causes:** User behaviour changed; data pipeline changed; seasonal effects not accounted for.

**Mitigations:**
- Monitor feature distributions
- Monitor prediction distributions
- Retrain regularly
- Detect drift with statistical tests (KS test, PSI)

### Label Noise

**Signal:** Unexpectedly high irreducible error; model behaves inconsistently on similar inputs.

**Causes:** Ambiguous labelling criteria; multiple annotators with disagreement; programmatic labelling errors.

**Fixes:** Clean labels; improve annotation guidelines; use label smoothing; ensemble multiple annotators.

---

## Feature Engineering

### Numerical features
- **Scaling:** StandardScaler (zero mean, unit variance) for linear models and neural nets; not needed for tree models
- **Log transform:** for right-skewed distributions (prices, counts, durations)
- **Binning:** discretise continuous variables for tree models when natural thresholds exist

### Categorical features
- **Ordinal encoding:** for ordered categories (low/medium/high → 0/1/2)
- **One-hot encoding:** for low-cardinality unordered categories (< 20 values)
- **Target encoding:** for high-cardinality categories (use with cross-validation to prevent leakage)
- **Embedding:** for very high cardinality in neural networks

### Time-series features
- Lag features: value at t-1, t-2, t-7 (last week), t-365 (last year)
- Rolling statistics: 7-day mean, 30-day standard deviation
- Date components: day of week, month, is_weekend, is_holiday
- Time since event: hours since last purchase, days since last login

### Text features
- TF-IDF for baseline; embeddings for neural models
- Character n-grams for robust handling of misspellings
- Pre-tokenise consistently with the model's tokeniser

---

## Production ML

### Serving patterns

| Pattern | Latency | Throughput | Use When |
|---|---|---|---|
| Real-time API | Low | Medium | User-facing predictions |
| Batch inference | High (delayed) | Very high | Daily reports, offline scoring |
| Streaming inference | Low–medium | High | Event-driven updates |
| Edge inference | Very low | Device-limited | Privacy-sensitive, offline |

### Model versioning and deployment

- Version every model artefact (model file, preprocessing pipeline, feature schema)
- Deploy with canary or shadow mode — do not flip all traffic at once
- Maintain rollback capability: keep previous model version deployable within minutes

### Monitoring

```
Monitor:
  Feature distributions       → catch data pipeline changes
  Prediction distributions    → catch model drift
  Latency and throughput      → catch infrastructure issues
  Business metrics            → catch that the model is still achieving its goal

Ground truth labels (when available):
  Label collection latency    → how long until you know if prediction was right
  Periodic re-evaluation      → re-score held-out set with current model
```

---

## Examples

### Example 1 — Choosing evaluation metrics for churn prediction

**Problem:** Predict which customers will cancel in the next 30 days. Marketing wants to offer retention discounts.

**Class balance:** 5% churn rate (95% no churn).

**Metric analysis:**
- Accuracy: 95% by predicting no one churns. Useless.
- Precision: important — sending discount to a non-churner has a cost (discount margin)
- Recall: important — missing a churner has a cost (lost revenue > discount cost)
- Decision: use **F1** or **PR-AUC**; set threshold based on cost-benefit analysis of discount vs lost revenue

---

### Example 2 — Diagnosing a model that works in dev but fails in production

**Symptom:** 94% AUC in development; 61% in production. Deployed 2 months ago.

**Diagnosis checklist:**
1. Is there data leakage? → Check features derived from the label ✓ (feature `is_refunded` is derived from the outcome)
2. Is there temporal leakage? → Check if future data was used → Found: feature used 7-day future aggregate
3. Has data distribution shifted? → Check feature distributions → No significant shift

**Root cause:** Data leakage. `is_refunded` was a proxy for the label. The feature was not available at prediction time. Remove it, retrain.

---

## Best Practices

- Start with a simple baseline — always
- Evaluate on held-out data with the correct metrics for the business objective
- Error-analyse before iterating — understand where the model fails
- Monitor in production — models degrade without anyone noticing
- Document every experiment, including failures

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| Using accuracy on imbalanced data | Misleading performance impression |
| Shuffling time-series before splitting | Data leakage; inflated test metrics |
| Tuning on the test set | Test set no longer measures generalisation |
| Deploying without monitoring | Silent performance degradation |
| Skipping the baseline | No reference point for improvement |
| Feature engineering before EDA | Engineering the wrong features |

---

## Limitations

- Cannot train or evaluate models
- Specific library APIs (sklearn, PyTorch, TensorFlow) may have version differences
- Domain-specific ML (medical imaging, financial time series) requires specialist expertise
- Very large-scale ML (training foundation models) requires infrastructure expertise beyond this module
