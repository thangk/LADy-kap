# Course Project Summary: Implicit Aspect Detection Dataset

## Project Overview

This course project aims to develop **the first dedicated benchmark dataset for implicit aspect detection** in aspect-based sentiment analysis (ABSA). The project is conducted by Kap Thang at the University of Windsor, leveraging the LADy (Latent Aspect Detection) framework to create a comprehensive dataset for training and evaluating implicit aspect detection models.

## Problem Statement & Motivation

### Current Challenges
- **Limited Datasets**: To our knowledge, no comprehensive dataset currently exists specifically for implicit aspect detection
- **Implicit vs Explicit Aspects**: While significant progress has been made in explicit aspect detection, implicit aspects remain challenging as they must be inferred from context
- **Previous Limitations**: Earlier attempts like masking explicit aspects altered grammatical structure and affected model performance
- **Real-world Relevance**: Brief, informal online reviews commonly contain implicit aspects that businesses need to analyze

### Formal Definition

**Dataset Structure**: 
```
D = {(r_i, A_i, O_i, P_i)}, for i = 1, ..., m
```

Where:
- **r_i**: A review text where aspects are implied and not explicitly stated
- **A_i = {a_1, a_2, ..., a_k}**: Set of implicit aspect categories inferred from r_i
- **O_i = {o_1, o_2, ..., o_k}**: Corresponding opinion terms indicating the implied aspects
- **P_i = {p_1, p_2, ..., p_k}**: Sentiment polarities (positive, negative, neutral) for each aspect

**Example**: 
- Review: "It is overpriced!"
- Implicit Aspect: {price}
- Opinion: {overpriced}
- Polarity: {negative}

### Refined Formal Definition (Sentence-Level)

**Implicit Aspect Dataset**:
```
D = {(s, a, o, p) | s ∈ S, a ∈ A, o ∈ O, p ∈ P}
```

Where:
- **s**: Sentence containing an implicit aspect
- **a**: Implicit aspect category inferred for the sentence
- **o**: Sentiment expression extracted from the sentence
- **p**: Sentiment polarity associated with the opinion

## Literature Review Summary

### Historical Context
- **Ancient Foundations**: Systematic review analysis traces back to classical philosophers like Aristotle
- **Modern Evolution**: Digital era brought unprecedented user-generated content requiring new NLP approaches
- **ABSA Development**: Traditional sentiment analysis evolved to fine-grained aspect-based analysis

### Current ABSA Landscape
- **Categories**: Single tasks (aspect term extraction) vs compound tasks (aspect-opinion pair extraction)
- **Limitations**: Most datasets focus on explicit aspects, overlooking implicit ones
- **Methods**: Unsupervised, supervised, and hybrid approaches for implicit aspect detection

### Implicit Aspect Detection Methods
1. **Unsupervised Methods**:
   - Co-occurrence-based approaches (Zhang & Zhu)
   - Graph-based representations (Bagheri et al.)
   - LDA with knowledge constraints (Zhang et al.)

2. **Supervised Methods**:
   - SVM with contextual n-grams (Hu et al.)
   - Deep learning models with annotated data

3. **Hybrid Methods**:
   - Clustering + supervised learning (Hai et al.)
   - Balancing accuracy and generalizability

### Role of LLMs in Dataset Curation
- **Capabilities**: Strong reasoning and contextual understanding for data generation
- **Advantages**: Scale, semantic understanding, domain knowledge preservation
- **Considerations**: Prompt quality, data filtering, bias mitigation

## Proposed Method & Technical Implementation

### Dataset Curation Pipeline

**Algorithm 1: ImplicitAspectPreprocessing**
```
1. Initialize ImplicitAspectDataset ← []
2. For each Review in RawReviews:
   a. Segment Review into sentences
   b. For each Sentence:
      - Extract sentiment expressions using SentimentLexicon
      - Check for absence of explicit aspect terms
      - If sentiment exists AND no explicit aspects:
        * Apply implicit aspect identification
        * Create annotation: [Sentence, ImplicitAspect, SentimentWord, Polarity]
        * Add to ImplicitAspectDataset
3. Expert verification and refinement
4. Format for LADy framework compatibility
```

### Key Process Steps

1. **Preprocessing**: 
   - Clean raw reviews (remove HTML tags, special characters)
   - Sentence segmentation
   - Tokenization and sentiment extraction

2. **Annotation Process**:
   - Extract sentiment expressions using SentimentLexicon
   - Identify sentences without explicit aspect terms
   - Apply implicit aspect identification
   - Create structured annotations

3. **LLM Data Augmentation**:
   - Generate additional candidate annotations
   - Improve coverage through contextual understanding
   - Validate and refine with domain experts

4. **Expert Verification**:
   - Manual review of annotations
   - Inter-annotator agreement calculation
   - Refinement of ambiguous cases

5. **Dataset Finalization**:
   - Convert to LADy-compatible structure
   - Enrich with metadata
   - Split into training and evaluation sets

### LADy Framework Integration

**Architecture Components**:
- **Modular Design**: Organized into data preprocessing, aspect modeling, sentiment analysis, and evaluation
- **Data Processing Pipeline**: Multiple dataset format support, augmentation capabilities, configurable splitting
- **Diverse Model Support**: LDA, BTM, CTM, BERTopic, BERT-E2E-ABSA, FastText integration
- **Implementation**: Python-based using PyTorch, NLTK, pandas, numpy

**Extension Points**:
- **Review Objects**: Extend existing Review class for implicit aspect annotations
- **Aspect Models**: Integrate implicit aspect detection into AbstractAspectModel hierarchy
- **Evaluation Metrics**: Adapt existing metrics for implicit aspect evaluation
- **Pipeline Integration**: Seamless integration with existing prep→train→test→eval→agg workflow

## Expected Integration with LADy Framework

### Data Structure Integration
- **Review Objects**: Enhance existing Review class to support implicit aspect annotations
- **Pickle Serialization**: Maintain compatibility with existing `.pkl` file format
- **Augmentation Support**: Leverage existing backtranslation capabilities for implicit aspect data

### Model Integration
- **AbstractAspectModel**: Extend base class for implicit aspect detection methods
- **Training Pipeline**: Integrate with existing cross-validation and model tuning workflows
- **Inference Interface**: Maintain compatibility with existing `infer()` method signatures

### Evaluation Framework
- **Metrics**: Adapt P@k, Recall, NDCG, MAP for implicit aspect evaluation
- **Output Structure**: Maintain existing output directory structure and file formats
- **Aggregation**: Integrate with existing result aggregation pipeline

## Project Structure & Responsibilities

### Kap Thang
- **Expertise**: Python development, Neural Networks, Research methodology, Natural Language Processing
- **Responsibilities**:
  - Literature review and methodology design
  - Technical implementation and framework development
  - Dataset curation method design and analysis
  - LADy framework integration and extension
  - Dataset evaluation and validation
  - ABSA domain expertise application

## Research Contributions

### Novel Contributions
1. **First Dedicated Dataset**: Creating the first comprehensive dataset specifically for implicit aspect detection
2. **LLM-Enhanced Curation**: Innovative use of large language models for dataset generation and validation
3. **Framework Extension**: Extending LADy framework to support implicit aspect detection research
4. **Formal Definition**: Providing clear mathematical formalization of implicit aspect detection tasks

### Expected Impact
- **Research Community**: Enable robust model development and fair comparison
- **Industry Applications**: Improve business review analysis capabilities
- **Methodological**: Advance understanding of implicit aspect detection challenges
- **Technical**: Provide reusable framework for future research

## Technical Specifications

### Data Format
```
Annotation Structure: [Sentence, ImplicitAspect, SentimentWord, Polarity]
- Sentence: Original text with implicit aspects
- ImplicitAspect: Categorized aspect (e.g., SERVICE, PRICE, QUALITY)
- SentimentWord: Opinion expression from sentence
- Polarity: Sentiment classification (positive, negative, neutral)
```

### LADy Framework Requirements
- **Python 3.8+**: Base runtime environment
- **Dependencies**: transformers, sentence_transformers, gensim, nltk, pandas
- **Models**: Support for LDA, BTM, CTM, BERT-E2E-ABSA, FastText
- **Pipeline**: Compatible with existing prep→train→test→eval→agg workflow

### Quality Assurance
- **Expert Validation**: Manual review and inter-annotator agreement
- **Bias Mitigation**: Systematic filtering and validation processes
- **Reproducibility**: Documented methodology and code availability

## Repository Structure

The project utilizes a fork of the LADy framework available at:
- **Repository**: https://github.com/thangk/LADy
- **Documentation**: Comprehensive README with installation and usage instructions
- **Quickstart**: Rapid experiment launching capabilities
- **Reproducible Pipeline**: Streamlined workflow for experiment replication

## Conclusion

This course project addresses a significant gap in ABSA research by creating the first dedicated dataset for implicit aspect detection. By leveraging the LADy framework's modular architecture and incorporating LLM-enhanced dataset curation, the project aims to advance the state-of-the-art in implicit aspect detection while providing a valuable resource for the research community.

The combination of formal problem definition, innovative methodology, and practical implementation through an established framework positions this work to make meaningful contributions to both academic research and practical applications in sentiment analysis and opinion mining.

---

*Generated from project documentation: Proposal, Literature Review, and Proposed Method PDFs*
*Project Lead: Kap Thang (thangk@uwindsor.ca)*
*University of Windsor - Course Project 2024*