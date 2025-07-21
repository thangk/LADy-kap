# Understanding Information Retrieval Metrics for Aspect Detection

## Important Note
**The examples below are simplified teaching analogies to explain how metrics work. Your actual experiment data has different values.**

## The Restaurant Review Analogy

Imagine you're a restaurant reviewer who needs to identify what aspects customers are talking about. You have 5 main categories:
- 🍕 **Food**
- 👨‍🍳 **Service** 
- 💰 **Price**
- 🪑 **Ambiance**
- 📍 **Location**

Your AI assistant reads reviews and predicts which categories are mentioned. Let's see how we measure its performance!

## Core Metrics Explained

### 1. Precision@k (P@k)
**"How accurate are your top k predictions?"**

Think of it as: If you only look at the first k predictions, what percentage are correct?

**Teaching Example (NOT your actual data):**
```
Ground Truth: Review mentions [Food, Service]
Model Predicts: [Food✓, Price✗, Service✓, Ambiance✗, Location✗]

P@1 = 1/1 = 100% (first prediction is correct)
P@3 = 2/3 = 67%  (2 correct out of first 3)
P@5 = 2/5 = 40%  (2 correct out of all 5)
```

**Your ACTUAL Data (BERT anthropic-haiku3.5-1300):**
```
P@1 = 0.0000 (0%)     - Never got the first prediction right
P@5 = 0.1398 (14%)    - About 0.7 correct predictions out of 5
P@10 = 0.0699 (7%)    - Same 0.7 correct, but now divided by 10
P@100 = 0.0070 (0.7%) - Same 0.7 correct, but now divided by 100
```

Notice how P@10 = P@5/2 and P@100 = P@10/10? This confirms BERT only makes 5 predictions.

### 2. Recall@k
**"How many of the relevant items did you find in your top k?"**

**Teaching Example:**
```
Ground Truth: [Food, Service, Price]
Model found in top 5: [Food✓, Service✓]
Recall@5 = 2/3 = 67% (found 2 out of 3 ground truth items)
```

**Your ACTUAL Data shows saturation:**
```
Recall@5 = 0.6344 (63.4%)
Recall@10 = 0.6344 (63.4%)  - Same! No new items found after position 5
Recall@100 = 0.6344 (63.4%) - Still same!
```

### 3. NDCG@k (Normalized Discounted Cumulative Gain)
**"How good is your ranking quality?"**

NDCG rewards putting important items first and penalizes important items appearing later.

**Your ACTUAL Data shows saturation:**
```
NDCG@5 = 0.4028
NDCG@10 = 0.4028  - Same! Ranking quality doesn't change after position 5
NDCG@100 = 0.4028 - Still same!
```

### 4. MAP@k (Mean Average Precision)
**"On average, how precise are you at each relevant position?"**

**Your ACTUAL Data shows saturation:**
```
MAP@5 = 0.3145
MAP@10 = 0.3145  - Same! No new relevant items after position 5
MAP@100 = 0.3145 - Still same!
```

### 5. Success@k
**"Did you find at least ONE correct answer in your top k?"**

**Your ACTUAL Data:**
```
Success@1 = 0.0000 (0%)    - Never found a correct answer at position 1
Success@5 = 0.6684 (66.8%)  - Found at least one correct in 66.8% of reviews
Success@10 = 0.6684 (66.8%) - Same! If not found by position 5, won't find later
Success@100 = 0.6684 (66.8%) - Still same!
```

## Why Your Metrics Saturate at k=5

With only 5 possible categories and models predicting at most 5:

```
Position:  1    2    3    4    5    6    7    8    9    10
Predicted: Cat1 Cat2 Cat3 Cat4 Cat5 --   --   --   --   --
                                    ^
                                    |
                          No predictions after position 5!
```

## The Key Insight

Your BERT model appears to predict exactly 5 categories for each review:
- Makes predictions for positions 1-5
- No predictions for positions 6-100
- This is why all metrics except precision saturate at k=5

## Recommendation for Your Paper

### Use k=1 and k=5 only because:

1. **k=1**: Shows "first-guess accuracy" 
   - Your BERT: 0% (never gets first guess right)
   - Important finding: BERT struggles with top prediction

2. **k=5**: Shows complete model performance
   - Your BERT: 14% precision, 63% recall
   - Captures all model predictions

3. **k=10, k=100**: Redundant
   - Just arithmetic divisions of k=5 results
   - No new information

### How to present this:

*"Given our experimental setup with 5 aspect categories and models configured to predict up to 5 categories, we report metrics at k=1 and k=5. The k=5 metrics represent complete model performance as no additional predictions exist beyond position 5."*