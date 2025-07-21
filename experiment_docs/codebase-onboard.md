# LADy-kap Codebase Onboarding Guide

Welcome to LADy-kap! This guide will help you get up to speed with the **LLM Dataset Benchmarking** research codebase quickly.

## 🎯 What is LADy-kap?

LADy-kap is a research framework for **benchmarking LLM-generated datasets** for implicit aspect detection. It builds on the LADy framework to evaluate how different Large Language Models perform when generating training datasets for aspect detection models.

### Key Features:
- **8 LLM models benchmarked**: Comprehensive evaluation across multiple LLM providers
- **3 architecture models**: BERT (transformer), CTM (neural topic), LDA (traditional topic)
- **Multiple dataset sizes**: 700, 1300, 2000 sentences for scaling analysis
- **Automated experiment scripts**: End-to-end experiment automation with background execution
- **Baseline comparison**: LLM-generated vs SemEval explicit datasets
- **Complete evaluation pipeline**: From data loading to statistical analysis

## 🚀 Quick Start

### 1. Environment Setup

**Prerequisites:**
- Python 3.8+
- CUDA-capable GPU (recommended for BERT/CTM models)
- Git with submodules support

**Setup Steps:**
```bash
# Clone the repository with submodules
git clone --recursive [repository-url]
cd LADy-kap

# Option A: Poetry (Recommended)
poetry install
poe post_install

# Option B: Conda 
conda env create -f environment.yml
conda activate lady

# Option C: pip
pip install -r requirements.txt
python download_nltk_data.py
cd src/octis && python setup.py install
```

### 2. Run Your First Experiment

**Quick Test:**
```bash
cd src
python main.py -naspects 5 -am lda -data ../experiment_datasets/semeval_implitcits/anthropic-haiku3.5-700.xml -output ../experiment_output/quick_test/
```

**Full Experiment (Automated):**
```bash
# Activate environment
conda activate lady

# Run LLM ranking experiment (24 experiments)
./experiment_scripts/exp1_llm_model_ranking.sh
```

### 3. Monitor Progress
```bash
# Check running processes
ps aux | grep exp1_llm_model_ranking.sh

# Monitor real-time logs
tail -f experiment_output/exp1_llm_model_ranking_run*/experiments.log

# View completed experiments
ls -la experiment_output/
```

## 📁 Project Structure

```
LADy-kap/
├── experiment_datasets/           # LLM-generated and baseline datasets
│   ├── semeval_implitcits/       # 8 LLM models × 3 sizes each
│   └── semeval_baselines/        # SemEval explicit baselines
├── experiment_scripts/           # Automated experiment runners
│   ├── exp1_llm_model_ranking.sh # Phase 1: LLM ranking (24 experiments)
│   ├── exp2_scaling_analysis.sh  # Phase 2: Scaling analysis (27 experiments)
│   └── exp3_baseline_comparison.sh # Phase 3: Baseline comparison (24 experiments)
├── experiment_output/            # All experiment results and logs
├── experiment_docs/              # Research documentation
│   ├── experiments_planning.md   # 3-phase experimental design
│   ├── experiment_settings.md    # Hyperparameters and settings
│   ├── llm_model_selection.md    # LLM model selection criteria
│   └── codebase-onboard.md      # This file
├── datasets-generator/           # Submodule for LLM dataset generation
├── src/                          # LADy framework source code
│   ├── cmn/                     # Common layer - Review classes
│   ├── aml/                     # Aspect models (BERT, CTM, LDA)
│   ├── web/                     # Web interface
│   ├── main.py                  # Main pipeline driver
│   └── params.py                # Configuration settings
└── data/                        # Original LADy datasets and cache
```

## 🔧 Key Concepts

### 1. LLM Dataset Benchmarking
- **8 LLM Models**: Anthropic (Haiku, Sonnet), Google (Gemini), OpenAI (GPT), xAI (Grok)
- **3 Dataset Sizes**: 700, 1300, 2000 sentences for scaling analysis
- **Implicit vs Explicit**: LLM-generated implicit datasets vs SemEval explicit baselines

### 2. Architecture Models
- **BERT**: Transformer-based supervised model (GPU recommended)
- **CTM**: Contextualized Topic Model using neural networks (GPU recommended)  
- **LDA**: Traditional Latent Dirichlet Allocation (CPU-only)

### 3. Experimental Pipeline
1. **prep**: Load XML datasets and preprocess reviews
2. **train**: 5-fold cross-validation training
3. **test**: Inference on held-out test sets
4. **eval**: Calculate ranking metrics (P@k, Recall, NDCG, MAP)
5. **agg**: Aggregate results across folds

### 4. Experiment Scripts
- **Background execution**: All scripts run in background with nohup
- **Auto-versioning**: Creates numbered run directories (run2, run3, etc.)
- **Environment validation**: Checks conda environment and directory structure
- **Comprehensive logging**: Real-time progress and error tracking

## 💻 Common Tasks

### Run Individual Experiments
```bash
cd src

# Test single LLM dataset with BERT
python main.py -naspects 5 -am bert -data ../experiment_datasets/semeval_implitcits/openai-gpt4o-700.xml -output ../experiment_output/test_bert/ -gpu 1

# Test with CTM
python main.py -naspects 5 -am ctm -data ../experiment_datasets/semeval_implitcits/anthropic-sonnet4-1300.xml -output ../experiment_output/test_ctm/ -gpu 1

# Test with LDA (CPU only)
python main.py -naspects 5 -am lda -data ../experiment_datasets/semeval_implitcits/google-gemini2.5pro-2000.xml -output ../experiment_output/test_lda/
```

### Run Full Experiment Phases
```bash
# Phase 1: Rank all 8 LLMs across 3 architectures (24 experiments)
./experiment_scripts/exp1_llm_model_ranking.sh

# Phase 2: Scaling analysis with top 3 LLMs (27 experiments) 
./experiment_scripts/exp2_scaling_analysis.sh

# Phase 3: Compare top LLMs vs SemEval baselines (24 experiments)
./experiment_scripts/exp3_baseline_comparison.sh
```

### Configure GPU Usage
Edit `experiment_scripts/exp*.sh`:
```bash
# Set GPU index (0, 1, 2, 3) or leave empty for CPU
export GPU_ID="1"
```

### Monitor Experiment Progress
```bash
# View experiment logs
tail -f experiment_output/exp1_llm_model_ranking_run*/experiments.log

# Check individual model logs
ls -la experiment_output/exp1_llm_model_ranking_run*/logs/

# View completed results
find experiment_output -name "agg.ad.pred.eval.mean.csv"
```

## 🔍 Debugging Tips

1. **Check experiment logs**: Each script creates detailed logs in `experiment_output/*/logs/`
2. **GPU memory issues**: Reduce batch size in `src/params.py` or use different GPU
3. **Script failures**: Run individual experiments manually to isolate issues
4. **Path issues**: Ensure you're in the project root directory when running scripts
5. **Environment issues**: Verify conda environment activation with `conda info --envs`

## 📊 Understanding Results

### Output Structure
```
experiment_output/exp1_llm_model_ranking_run1/
├── experiments.log                    # Main experiment log
├── experiment_summary.txt             # High-level results summary  
├── logs/                             # Individual experiment logs
│   ├── bert_anthropic-haiku3.5_700.log
│   └── ...
└── bert/                             # Results by architecture
    ├── anthropic-haiku3.5-700/
    │   ├── agg.ad.pred.eval.mean.csv # Final aggregated metrics
    │   ├── reviews.pkl               # Preprocessed data
    │   └── splits.json              # Train/test splits
    └── ...
```

### Key Metrics
- **P@5**: Precision at 5 (primary ranking metric)
- **Recall@5**: Coverage of correct aspects in top 5
- **NDCG@5**: Normalized Discounted Cumulative Gain  
- **MAP@5**: Mean Average Precision
- **Success@5**: Binary success metric

### Evaluation Files
- `agg.ad.pred.eval.mean.csv`: Final results averaged across 5 folds
- `experiment_summary.txt`: Human-readable summary with rankings
- Individual fold results: `f{0-4}.model.ad.pred.eval.mean.csv`

## 🤝 Contributing

### Add New LLM Datasets
1. **Generate dataset**: Use `datasets-generator/` submodule
2. **Add to collection**: Place XML file in `experiment_datasets/semeval_implitcits/`
3. **Update scripts**: Add LLM model to arrays in experiment scripts
4. **Test**: Run single experiment to validate

### Add New Architecture Model
1. **Implement model**: Create class in `src/aml/` inheriting from `AbstractAspectModel`
2. **Add configuration**: Update `src/params.py` with model settings
3. **Update scripts**: Add to `ARCH_MODELS` arrays in experiment scripts
4. **Test**: Validate with small dataset first

### Modify Experiment Configuration
- **Hyperparameters**: Edit `experiment_docs/experiment_settings.md` and `src/params.py`
- **Dataset sizes**: Update `DATASET_SIZES` in experiment scripts
- **Evaluation metrics**: Modify evaluation settings in `src/params.py`

## 📚 Resources

### Documentation
- **Experiment Planning**: `experiment_docs/experiments_planning.md`
- **Hyperparameters**: `experiment_docs/experiment_settings.md`
- **LLM Selection**: `experiment_docs/llm_model_selection.md`
- **Dataset Generation**: `experiment_docs/llm_dataset_generation_prompt.md`

### Research Context
- **Original LADy Paper**: [CIKM'23 - Latent Aspect Detection via Backtranslation Augmentation](https://hosseinfani.github.io/res/papers/2023_CIKM_Latent_Aspect_Detection_via_Backtranslation_Augmentation.pdf)
- **LADy Framework**: https://github.com/fani-lab/LADy

## ⚡ Quick Commands Reference

```bash
# Environment setup
conda activate lady

# Quick test
cd src && python main.py -naspects 5 -am lda -data ../experiment_datasets/semeval_implitcits/anthropic-haiku3.5-700.xml -output ../test/

# Full experiments
./experiment_scripts/exp1_llm_model_ranking.sh    # Phase 1: LLM ranking
./experiment_scripts/exp2_scaling_analysis.sh     # Phase 2: Scaling analysis  
./experiment_scripts/exp3_baseline_comparison.sh # Phase 3: Baseline comparison

# Monitor progress
tail -f experiment_output/exp*/experiments.log
ps aux | grep exp1_llm_model_ranking.sh

# Code quality
ruff check src/ --fix

# Web interface (optional)
poe start_web_back   # Backend: http://localhost:5000
poe start_web_front  # Frontend: http://localhost:3000
```

## 🆘 Getting Help

### Troubleshooting
1. **Check experiment logs**: `experiment_output/*/logs/` and `experiments.log`
2. **Verify environment**: `conda info --envs` and `which python`
3. **Test individual components**: Run single experiments manually
4. **Review settings**: Check `experiment_docs/experiment_settings.md`

### Common Issues
- **GPU out of memory**: Reduce batch size in `src/params.py`
- **Script permissions**: Run `chmod +x experiment_scripts/*.sh`
- **Missing datasets**: Verify files exist in `experiment_datasets/`
- **Path errors**: Always run scripts from project root directory

Happy researching! 🎉