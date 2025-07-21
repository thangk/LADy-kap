# COMP8730 Project Implementation Guide

## Overview
This guide provides step-by-step instructions for implementing your implicit aspect detection project using the LADy framework.

## Prerequisites

### System Requirements
- Python 3.8+
- CUDA-compatible GPU (recommended for BERT and CTM)
- 8GB+ RAM
- Linux/macOS environment

### Installation Setup
```bash
# Navigate to project root
cd /home/thangk/msc/LADy-kap

# Install dependencies (choose one method)
# Option 1: Using Poetry (recommended)
poetry install
poe post_install

# Option 2: Using Conda
conda env create -f environment.yml
conda activate lady

# Option 3: Using pip with virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Option 3a: Try original requirements (may fail with newer Python)
pip install -r requirements.txt

# Option 3b: If requirements.txt fails, install compatible versions
pip install --upgrade pip
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
pip install transformers>=4.26.1
pip install pandas>=1.4.4
pip install seaborn tqdm natsort scikit-learn nltk spacy
pip install sentence-transformers contextualized-topic-models bitermplus
pip install gensim matplotlib requests scipy simalign
pip install pytrec-eval-terrier

# Download NLTK data and install OCTIS
python download_nltk_data.py  # Downloads NLTK stopwords and wordnet data
cd src/octis && python setup.py install
```

### What is `download_nltk_data.py`?
This script downloads essential NLTK data required for text processing:
- **stopwords**: Common words (like "the", "is", "at") that are filtered out during analysis
- **wordnet**: Lexical database for word relationships and lemmatization

These are required for the LADy framework's text preprocessing pipeline.

## Phase 1: Testing LADy Pipeline with Existing Datasets

### Available Test Datasets

1. **Toy Dataset** (fastest for testing):
   - `data/raw/semeval/toy.2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml`
   - ~50 reviews, ideal for quick testing

2. **SemEval 2016 Restaurant Dataset**:
   - `data/raw/semeval/2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml`
   - Full dataset for comprehensive testing

3. **SemEval 2014 Restaurant Dataset**:
   - `data/raw/semeval/SemEval-14/Restaurants_Train_v2.xml`
   - Alternative baseline dataset

4. **MAMS Dataset**:
   - `data/raw/mams/train.xml`
   - Multi-Aspect Multi-Sentiment dataset

### Quick Pipeline Test

#### Step 1: Basic Pipeline Test
```bash
# Navigate to src directory
cd src

# Test with toy dataset using LDA (fastest)
python main.py -naspects 5 -am lda -data ../data/raw/semeval/toy.2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/test_toy/
```

#### Step 2: Test Different Models
```bash
# Test LDA (2-5 minutes, CPU sufficient)
python main.py -naspects 5 -am lda -data ../data/raw/semeval/toy.2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/test_lda/

# Test CTM (10-15 minutes, GPU recommended)
python main.py -naspects 5 -am ctm -data ../data/raw/semeval/toy.2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/test_ctm/

# Test BERT (30-60 minutes, GPU required)
python main.py -naspects 5 -am bert -data ../data/raw/semeval/toy.2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/test_bert/
```

#### Step 3: Test with Full Dataset
```bash
# Test with full SemEval 2016 dataset
python main.py -naspects 5 -am lda -data ../data/raw/semeval/2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/test_semeval2016/
```

### Expected Output Structure
After running, you should see:
```
output/test_toy/
├── reviews.pkl                    # Preprocessed review objects
├── 5/                            # Number of aspects (configurable)
│   └── lda/                      # Model type
│       ├── f1.model              # Fold 1 trained model
│       ├── f1.model.dict         # Fold 1 vocabulary
│       ├── f1.model.pred.eval    # Fold 1 evaluation results
│       ├── f2.model              # Fold 2 trained model
│       ├── ...                   # Folds 3-5
│       └── model.pred.eval.csv   # Mean results across folds
└── agg.pred.eval.mean.csv        # Final aggregated results
```

### Understanding Results
Key metrics in `agg.pred.eval.mean.csv`:
- **P@k**: Precision at rank k (k=1,5,10,100)
- **Recall@k**: Recall at rank k
- **NDCG@k**: Normalized Discounted Cumulative Gain
- **MAP@k**: Mean Average Precision
- **Success@k**: Success rate at rank k

## Phase 2: Generate Your Custom Dataset

### Step 1: Use LLM Prompt
```bash
# Copy the prompt from project root
cat llm_dataset_generation_prompt.md
```

### Step 2: Generate Dataset
1. Copy everything between `<llm_prompt_template>` tags
2. Paste into GPT-4, Claude, or similar LLM
3. Save the XML output as `your_dataset.xml`

### Step 3: Validate Dataset Format
Your generated dataset should have this structure:
```xml
<?xml version="1.0" ?>
<sentences>
    <sentence id="1">
        <text>Review text here</text>
        <aspectTerms>
            <aspectTerm term="food" polarity="positive" from="10" to="14"/>
        </aspectTerms>
        <aspectCategories>
            <aspectCategory category="food" polarity="positive"/>
        </aspectCategories>
    </sentence>
    <!-- More sentences... -->
</sentences>
```

## Phase 3: Run Your Experiments

### Recommended Experimental Setup

#### Dataset Generation Plan
Generate multiple datasets for robustness:
```bash
# Generate datasets of different sizes
# 1. Small dataset (500 reviews) - for quick testing
# 2. Medium dataset (1000 reviews) - for validation
# 3. Large dataset (2000 reviews) - for main experiments
```

#### Model Comparison Experiments
```bash
# Test your generated dataset with all three models
cd src

# Your implicit dataset with LDA
python main.py -naspects 5 -am lda -data ../data/your_implicit_dataset.xml -output ../output/implicit_lda/

# Your implicit dataset with CTM
python main.py -naspects 5 -am ctm -data ../data/your_implicit_dataset.xml -output ../output/implicit_ctm/

# Your implicit dataset with BERT
python main.py -naspects 5 -am bert -data ../data/your_implicit_dataset.xml -output ../output/implicit_bert/
```

#### Baseline Comparison
```bash
# Compare with existing explicit datasets
python main.py -naspects 5 -am lda -data ../data/raw/semeval/2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/explicit_lda/

python main.py -naspects 5 -am ctm -data ../data/raw/semeval/2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/explicit_ctm/

python main.py -naspects 5 -am bert -data ../data/raw/semeval/2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/explicit_bert/
```

### Research Questions to Address

1. **RQ1**: How do neural models (BERT) compare to topic models (LDA/CTM) for implicit aspect detection?
2. **RQ2**: What's the performance degradation when moving from explicit to implicit aspects?
3. **RQ3**: How does dataset size affect model performance for implicit aspects?
4. **RQ4**: Which approach offers the best accuracy-efficiency trade-off?

### Expected Results Analysis
Based on evaluation PDF, expect:
- **BERT**: Highest performance (~38% P@5), most computational cost
- **LDA**: Good baseline (~8% P@5), very efficient
- **CTM**: Middle ground (~2% P@5), moderate efficiency
- **Performance Drop**: ~20-25% degradation from explicit to implicit

## Phase 4: Results Analysis

### Compare Results Across Models
```bash
# Compare aggregated results
compare_results.py output/implicit_lda/agg.pred.eval.mean.csv \
                   output/implicit_ctm/agg.pred.eval.mean.csv \
                   output/implicit_bert/agg.pred.eval.mean.csv
```

### Generate Visualization
```bash
# Create performance comparison charts
python analyze_results.py --input output/ --output results_analysis/
```

## Troubleshooting

### Common Issues and Solutions

#### Installation Issues
```bash
# Requirements.txt version conflicts (Python 3.12+)
# Solution: Use Option 3b with compatible versions instead

# NLTK data missing
python download_nltk_data.py

# OCTIS installation failed
cd src/octis && python setup.py install

# PyTorch CUDA issues
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118

# Specific fix for torch==1.13.1 error
pip install torch>=2.0.0 torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
```

#### Runtime Issues
```bash
# Memory issues with large datasets
# Solution: Start with toy dataset, increase gradually

# CUDA out of memory
# Solution: Reduce batch size in params.py or use CPU

# Import errors
# Solution: Ensure all dependencies installed correctly
```

#### Model-Specific Issues
- **BERT**: Requires GPU, high memory usage
- **LDA**: CPU sufficient, may need more iterations for convergence
- **CTM**: GPU recommended, sensitive to hyperparameters

### Performance Expectations

#### Computational Time (per experiment)
- **LDA**: 2-5 minutes (toy), 30-60 minutes (full dataset)
- **CTM**: 10-15 minutes (toy), 1-2 hours (full dataset)
- **BERT**: 30-60 minutes (toy), 2-4 hours (full dataset)

#### Total Project Timeline
- **Testing Phase**: 1-2 days
- **Dataset Generation**: 1-2 days
- **Full Experiments**: 1-2 weeks
- **Analysis & Documentation**: 1 week

## Configuration Options

### Key Parameters in `src/params.py`
```python
# Pipeline steps to run
settings['cmd'] = ['prep', 'train', 'test', 'eval', 'agg']

# Cross-validation folds
settings['train']['nfolds'] = 5

# Train/test split ratio
settings['train']['ratio'] = 0.8

# GPU configuration
os.environ['CUDA_VISIBLE_DEVICES'] = '0'
```

### Model-Specific Configuration
- **BERT**: Batch size, learning rate, epochs
- **LDA**: Number of topics, alpha, beta parameters
- **CTM**: Hidden dimensions, dropout, learning rate

## Success Criteria

### Technical Success
- [ ] Pipeline runs successfully with existing datasets
- [ ] Generated dataset works with LADy framework
- [ ] All three models (BERT, LDA, CTM) complete experiments
- [ ] Results show meaningful performance differences

### Research Success
- [ ] Clear performance comparison between models
- [ ] Quantified degradation from explicit to implicit aspects
- [ ] Scalability analysis across dataset sizes
- [ ] Publication-ready results and documentation

## Next Steps

1. **Start with Phase 1**: Test existing datasets to understand pipeline
2. **Generate your dataset**: Use the LLM prompt to create implicit aspect data
3. **Run experiments**: Compare BERT vs LDA vs CTM performance
4. **Analyze results**: Address the research questions systematically
5. **Document findings**: Prepare final report and presentation

---

**Note**: This guide provides the complete workflow for your COMP8730 project. Follow each phase sequentially for best results.