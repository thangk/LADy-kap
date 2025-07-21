# COMP8730 Project Requirements

## Project Overview
**Course**: COMP8730 - Natural Language Processing and Understanding  
**Project**: A Benchmark Dataset for Implicit Aspect Detection  
**Student**: Kap Thang (thangk@uwindsor.ca)  
**Institution**: University of Windsor  

## Project Objectives

### Primary Goal
Develop the first dedicated benchmark dataset for implicit aspect detection in aspect-based sentiment analysis (ABSA), addressing the significant gap in available datasets for this specific task.

### Secondary Goals
1. Implement an LLM-enhanced dataset generation pipeline
2. Create expert validation workflows for annotation quality
3. Integrate seamlessly with existing LADy framework
4. Provide comprehensive evaluation of aspect detection models

## Required Implementation Components

### 1. Dataset Generation Pipeline
**Status**: ✅ Implemented  
**Files**: `llm_dataset_generation_prompt.md` (project root)

**Requirements**:
- LLM-based dataset generation using structured prompts
- Manual generation workflow for quality control
- XML output in SemEval format for LADy compatibility
- Natural language restaurant review generation

**Key Features**:
- Simple copy-paste prompt template for LLM use
- SemEval XML format output
- Both explicit and implicit aspect generation
- Restaurant-specific vocabulary and expressions

### 2. LLM Prompt Templates
**Status**: ✅ Implemented  
**Files**: `llm_dataset_generation_prompt.md` (project root)

**Requirements**:
- Specific prompts for each aspect-sentiment combination
- Guidelines for natural language generation
- Quality validation criteria
- Batch generation capabilities
- Research-grade documentation for reproducibility

**Aspect Categories**:
- FOOD: taste, quality, menu items, ingredients
- SERVICE: staff behavior, attentiveness, speed
- AMBIENCE: atmosphere, noise, decor, comfort
- PRICE: value for money, cost, affordability
- LOCATION: accessibility, parking, convenience

### 3. Enhanced Review Processing
**Status**: 🔄 Required (Extension of existing)  
**Files**: `src/cmn/review.py`, `src/cmn/semeval.py`

**Requirements**:
- Extend existing Review class for implicit aspect annotations
- Support for sentence-level (s, a, o, p) tuple storage
- Opinion word extraction and storage capabilities
- Enhanced metadata tracking for annotation quality
- Backward compatibility with existing LADy pipeline

### 4. Dataset Quality Assurance
**Status**: ✅ Integrated in prompt design  
**Files**: `llm_dataset_generation_prompt.md` (quality guidelines)

**Requirements**:
- Automated quality validation for generated sentences
- Explicit aspect term detection and filtering
- Sentiment-aspect alignment verification
- Diversity metrics for vocabulary and patterns
- Expert annotation interface for manual validation

### 5. Evaluation Framework Integration
**Status**: ✅ Existing (LADy Framework)  
**Files**: Existing LADy evaluation pipeline

**Requirements**:
- Seamless integration with existing LADy evaluation metrics
- Support for P@k, Recall@k, NDCG@k, MAP@k, Success@k
- Proper handling of implicit aspect evaluation
- Cross-validation support (5-fold)
- Model comparison capabilities (BERT, LDA, CTM)

## Technical Specifications

### Data Format Requirements
```xml
<sentences>
  <sentence id="1">
    <text>Review sentence with implicit aspect</text>
    <aspectCategories>
      <aspectCategory category="food" polarity="positive" type="implicit"/>
    </aspectCategories>
    <metadata sentence_count="1" review_type="implicit"/>
  </sentence>
</sentences>
```

### Dataset Specifications
- **Size**: Focused on 3 key dataset sizes for efficient analysis:
  - 700 sentences (~127 reviews) - Smaller baseline for rapid iteration
  - 1300 sentences (~236 reviews) - Large baseline (matches SemEval-15: 1315 sentences)
  - 2000 sentences (~364 reviews) - Maximum baseline (matches SemEval-16: 2000 sentences)
- **Structure**: ~5.5 sentences per review (derived from SemEval-15: 5.18, SemEval-16: 5.71)
- **Format**: XML following SemEval standards with nested Review->sentences->sentence structure
- **Aspect Distribution**: Configurable weights (FOOD#QUALITY: 30%, SERVICE#GENERAL: 25%, AMBIENCE#GENERAL: 20%, PRICE#GENERAL: 15%, LOCATION#GENERAL: 10%)
- **Sentiment Distribution**: Positive: 60%, Negative: 30%, Neutral: 10%
- **Quality Thresholds**: 8-25 words per sentence, 3-8 sentences per review

### Model Evaluation Requirements
- **Models**: BERT, LDA, CTM (existing LADy implementations)
- **Metrics**: Precision@k, Recall@k, NDCG@k, MAP@k, Success@k (k=1,5,10,100)
- **Validation**: 5-fold cross-validation
- **Comparison**: Explicit vs. implicit aspect detection performance

## Implementation Status

### ✅ Completed Components
1. **LLM Prompt Templates** - Simple copy-paste template for dataset generation
2. **SemEval XML Format** - Compatible output format for LADy framework
3. **Quality Guidelines** - Built into prompt requirements
4. **Restaurant Domain Focus** - Specialized for restaurant reviews
5. **Mixed Aspect Support** - Both explicit and implicit aspects

### 🔄 In Progress
1. **LADy Integration** - Ensuring seamless compatibility with existing pipeline
2. **Evaluation Framework** - Adapting existing metrics for implicit aspects

### 📋 Pending Implementation
1. **Expert Annotation Interface** - Manual validation workflow
2. **Enhanced Review Class Extensions** - Sentence-level annotation storage
3. **Batch Processing Scripts** - Automated generation pipeline orchestration

## Research Methodology

### Data Generation Process
1. **LLM Prompting**: Use provided prompts to generate natural implicit aspect sentences
2. **Quality Validation**: Automated filtering for sentence quality and implicit nature
3. **Manual Review**: Expert validation of generated content subset
4. **Dataset Assembly**: XML format generation compatible with LADy framework
5. **Evaluation**: Model performance comparison using LADy pipeline

### Recommended Experimental Strategy

#### Types of Comparison

1. **Model Benchmarking**
    - Compare all models on the 700-sentence dataset (~127 reviews) to identify top performers.

2. **Scaling Analysis**
    - Evaluate the top 3 models across all dataset sizes (700, 1300, 2000 sentences) to analyze how performance scales with data volume.

3. **Baseline Comparison**
    - Compare the top 3 models on your 1300-sentence dataset against the SemEval-15 baseline (1315 sentences) for fair, direct benchmarking.
    - Compare the top 3 models on your 2000-sentence dataset against the SemEval-16 baseline (2000 sentences) for fair, direct benchmarking.

#### Dataset Generation Plan
Generate focused datasets for efficient scaling analysis:
- **700 sentences** (~127 reviews) - Smaller baseline for quick iteration and model benchmarking
- **1300 sentences** (~236 reviews) - Large baseline (matches SemEval-15 size: 1315 sentences)
- **2000 sentences** (~364 reviews) - Maximum baseline (matches SemEval-16 size: 2000 sentences)

**Baseline Comparison Datasets**:
- **SemEval-15**: 1315 sentences, 254 reviews (5.18 sentences/review)
- **SemEval-16**: 2000 sentences, 350 reviews (5.71 sentences/review)

#### Model Comparison Strategy
**Core Models** (all ready-to-use in LADy):
1. **BERT-E2E-ABSA** (`src/aml/bert.py`)
   - Supervised, state-of-the-art
   - Expected: Highest performance, most computational cost

2. **LDA** (`src/aml/lda.py`)
   - Classical unsupervised topic modeling
   - Expected: Good baseline, very efficient (~30 min/experiment)

3. **CTM** (`src/aml/ctm.py`)
   - Modern contextualized topic modeling
   - Expected: Middle ground between BERT and LDA (~1-2 hours/experiment)

#### Research Questions to Address
1. **RQ1**: How do neural models (BERT) compare to topic models (LDA/CTM) for implicit aspect detection?
2. **RQ2**: What's the performance degradation when moving from explicit to implicit aspects?
3. **RQ3**: How does dataset size affect model performance for implicit aspects?
4. **RQ4**: Which approach offers the best accuracy-efficiency trade-off?

#### Evaluation Framework
- **Metrics**: P@k, Recall@k, NDCG@k, MAP@k, Success@k (k=1,5,10)
- **Validation**: 5-fold cross-validation (configured in LADy)
- **Comparison**: Explicit vs implicit aspect detection performance
- **Expected Results**: ~20-25% performance degradation from explicit to implicit across all models

#### Computational Timeline
- **LDA**: ~30 minutes per dataset (CPU sufficient)
- **CTM**: ~1-2 hours per dataset (GPU recommended)
- **BERT**: ~2-4 hours per dataset (GPU required)
- **Total Project**: ~1-2 weeks for all experiments

### Evaluation Approach
- **Baseline Models**: BERT, LDA, CTM on explicit aspects
- **Target Models**: Same models on implicit aspects
- **Performance Metrics**: Standard ABSA evaluation metrics
- **Analysis**: Performance degradation from explicit to implicit aspects

## Expected Outcomes

### Academic Contributions
1. **First Implicit Aspect Dataset**: Dedicated benchmark for implicit aspect detection
2. **Methodology Documentation**: Reproducible LLM-enhanced dataset generation
3. **Performance Baselines**: Established benchmarks for future research
4. **Framework Extension**: Enhanced LADy capabilities for implicit aspects

### Technical Deliverables
1. **High-Quality Dataset**: 2000 implicit aspect reviews with expert validation
2. **Generation Pipeline**: Reusable toolkit for similar dataset creation
3. **Evaluation Results**: Comprehensive model performance analysis
4. **Documentation**: Complete methodology and prompt documentation

## Project Timeline

### Phase 1: Core Implementation ✅
- LLM prompt development
- Dataset generation pipeline
- Quality validation framework

### Phase 2: Integration & Testing 🔄
- LADy framework integration
- Evaluation pipeline adaptation
- Initial dataset generation

## Testing LADy Pipeline with Existing Datasets

### Available Sample Datasets
The repository includes several datasets for testing:

1. **SemEval Restaurant Datasets**:
   - `data/raw/semeval/toy.2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml` (small test dataset)
   - `data/raw/semeval/2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml` (full dataset)
   - `data/raw/semeval/SemEval-14/Restaurants_Train_v2.xml` (SemEval 2014)

2. **SemEval Laptop Datasets**:
   - `data/raw/semeval/2016SB5/ABSA16_Laptops_Train_SB1_v2.xml`
   - `data/raw/semeval/SemEval-14/Laptop_Train_v2.xml`

3. **MAMS Dataset**:
   - `data/raw/mams/train.xml` (Multi-Aspect Multi-Sentiment)

### Quick Pipeline Test
To test the LADy pipeline with existing data:

```bash
# Navigate to src directory
cd src

# Test with toy restaurant dataset (fastest)
python main.py -naspects 5 -am lda -data ../data/raw/semeval/toy.2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/test_toy/

# Test with full SemEval 2016 restaurant dataset
python main.py -naspects 5 -am lda -data ../data/raw/semeval/2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/test_semeval2016/

# Test with BERT model (requires GPU)
python main.py -naspects 5 -am bert -data ../data/raw/semeval/toy.2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/test_bert/
```

### Testing Different Models
```bash
# Test LDA (fastest, CPU)
python main.py -naspects 5 -am lda -data ../data/raw/semeval/toy.2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/test_lda/

# Test CTM (medium speed, GPU recommended)
python main.py -naspects 5 -am ctm -data ../data/raw/semeval/toy.2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/test_ctm/

# Test BERT (slowest, GPU required)
python main.py -naspects 5 -am bert -data ../data/raw/semeval/toy.2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/test_bert/
```

### Expected Output Structure
After running, you should see:
```
output/test_toy/
├── reviews.pkl                    # Preprocessed review objects
├── 5/                            # Number of aspects
│   └── lda/                      # Model type
│       ├── f1.model              # Fold 1 model
│       ├── f1.model.pred.eval    # Fold 1 evaluation
│       └── ...                   # Other folds
└── agg.pred.eval.mean.csv        # Aggregated results
```

### Troubleshooting
- **CUDA errors**: Install PyTorch with CUDA support for GPU models
- **Memory issues**: Start with toy dataset first
- **Import errors**: Run `python download_nltk_data.py` and `cd src/octis && python setup.py install`

### Phase 3: Validation & Refinement 📋
- Expert annotation workflow
- Quality assessment and refinement
- Final dataset assembly

### Phase 4: Evaluation & Documentation 📋
- Model performance evaluation
- Results analysis and documentation
- Final deliverables preparation

## Success Criteria

### Technical Success
- [x] Functional dataset generation pipeline
- [x] Quality validation mechanisms
- [ ] Seamless LADy integration
- [ ] Comprehensive evaluation results

### Research Success
- [ ] Demonstrable improvement in dataset quality over template-based generation
- [ ] Meaningful performance analysis between explicit and implicit aspects
- [ ] Reproducible methodology for future research
- [ ] Publication-ready results and documentation

## Repository Structure

```
LADy-kap/
├── src/
│   ├── cmn/                            # Extended: Review processing
│   └── aml/                            # Existing: Model implementations
├── .kap/
│   ├── comp8730_roadmap.md                     # New: This document
│   ├── course_project_summary.md              # New: Project overview
│   └── changes_summary.md                      # New: Change documentation
├── llm_dataset_generation_prompt.md   # New: LLM generation prompt
├── data/raw/                           # Existing: Sample datasets for testing
└── experiments/                        # Existing: Evaluation results
```

---

**Note**: This project builds upon the existing LADy framework while introducing novel LLM-enhanced dataset generation capabilities specifically for implicit aspect detection research.