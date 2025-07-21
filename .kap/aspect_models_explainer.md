# Aspect-Based Sentiment Analysis Models Explainer

This document provides a brief overview of the four aspect detection models used in the LADy experiments: BERT, BTM, CTM, and RND.

## BERT (Bidirectional Encoder Representations from Transformers)

### How it Works
- **Architecture**: Transformer-based neural network with bidirectional attention
- **Approach**: Fine-tuned pre-trained language model for aspect extraction
- **Process**: 
  1. Takes entire review as input
  2. Uses attention mechanisms to identify aspect-relevant tokens
  3. Classifies text spans as aspects using learned representations

### Key Characteristics
- **Supervised**: Requires labeled training data
- **Contextual**: Understands words based on surrounding context
- **Deep Learning**: Uses multiple transformer layers (typically 12-24)
- **Strengths**: Captures complex linguistic patterns, handles context well
- **Limitations**: Computationally expensive, needs substantial training data

## BTM (Biterm Topic Model)

### How it Works
- **Architecture**: Probabilistic graphical model based on word co-occurrences
- **Approach**: Models topics through word pairs (biterms) rather than individual words
- **Process**:
  1. Extracts all word pairs (biterms) from documents
  2. Models topics as distributions over biterms
  3. Infers aspects as topic clusters

### Key Characteristics
- **Unsupervised**: No labeled data required
- **Biterm-based**: Better for short texts than traditional LDA
- **Statistical**: Uses Gibbs sampling for inference
- **Strengths**: Works well with short texts, captures word associations
- **Limitations**: Ignores word order, assumes bag-of-words

## CTM (Contextualized Topic Model)

### How it Works
- **Architecture**: Neural topic model combining BERT embeddings with variational autoencoders
- **Approach**: Uses pre-trained contextual embeddings to inform topic modeling
- **Process**:
  1. Encodes documents using BERT/Sentence-BERT
  2. Passes embeddings through neural topic model
  3. Learns topics that respect contextual semantics

### Key Characteristics
- **Hybrid**: Combines neural embeddings with topic modeling
- **Semi-supervised**: Leverages pre-trained models but doesn't need labels
- **Neural**: Uses variational inference with neural networks
- **Strengths**: Contextual understanding + topic interpretability
- **Limitations**: More complex than traditional topic models

## RND (Random Baseline)

### How it Works
- **Architecture**: No architecture - purely random selection
- **Approach**: Randomly selects aspects without any learning
- **Process**:
  1. Randomly samples from possible aspects
  2. May use simple heuristics (e.g., noun phrases)
  3. Serves as lower bound for performance

### Key Characteristics
- **Baseline**: Establishes minimum expected performance
- **No Learning**: Doesn't adapt to data patterns
- **Simple**: Minimal computational requirements
- **Purpose**: Validates that other models actually learn
- **Note**: In experiments, "RND" may use LLMs with minimal guidance

## Key Differences

### Learning Paradigm
- **BERT**: Supervised learning with labeled examples
- **BTM/CTM**: Unsupervised topic discovery
- **RND**: No learning

### Input Processing
- **BERT**: Processes full sequences with positional information
- **BTM**: Treats text as bag of word pairs
- **CTM**: Combines sequence understanding with topic modeling
- **RND**: Minimal or no processing

### Computational Requirements
- **BERT**: High (GPU recommended)
- **CTM**: Medium-High (needs BERT encoding)
- **BTM**: Low-Medium (statistical sampling)
- **RND**: Minimal

### When to Use Each
- **BERT**: When you have labeled training data and need high accuracy
- **BTM**: For exploratory analysis of short texts without labels
- **CTM**: When you want interpretable topics with contextual understanding
- **RND**: As a baseline to ensure other models provide value

## In the Context of LLM Experiments

When these models are augmented with LLMs:
- **BERT + LLM**: LLM generates training data or enhances representations
- **BTM + LLM**: LLM helps interpret or refine discovered topics
- **CTM + LLM**: LLM provides better contextual embeddings
- **RND + LLM**: Tests LLM's zero-shot aspect extraction ability

The experiments show that LLM integration dramatically improves all methods, with thinking models (Sonnet-4, Grok-4) showing particular advantages for topic-based approaches (BTM/CTM).