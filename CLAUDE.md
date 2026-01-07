# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Environment Setup

**IMPORTANT**: Always activate the project conda environment before running any commands, testing, or development work:

```bash
conda activate lady-comp8730
```

All development, testing, and execution should be done within this environment to ensure proper dependencies are available.

## Common Development Commands

### Setup and Installation
```bash
# Using Poetry (recommended)
poetry install
poe post_install

# Using Conda
conda env create -f environment.yml
conda activate lady

# Using pip
pip install -r requirements.txt
python download_nltk_data.py
cd src/octis && python setup.py install
```

### Running Experiments
```bash
# Run a basic experiment
cd src
python main.py -naspects 5 -am rnd -data ../data/raw/semeval/toy.2016SB5/ABSA16_Restaurants_Train_SB1_v2.xml -output ../output/toy.2016SB5/

# Run slim experiment
poe dummy

# Run experiment scripts (from project root)
./experiment_scripts/run_exp1.sh    # Experiment 1: LLM model evaluation
./experiment_scripts/run_exp2.sh    # Experiment 2: LADy performance analysis
./experiment_scripts/run_exp3.sh    # Experiment 3: Comprehensive comparison

# Stop running experiments
./experiment_scripts/stop_exp.sh

# Rerun specific experiments
./experiment_scripts/rerun_exp.sh

# Run baseline models
./experiment_scripts/run_baselines.sh
```

### Web Interface
```bash
# Start backend server
poe start_web_back
# Or manually:
python src/web/backend/app.py

# Start frontend (in another terminal)
poe start_web_front
# Or manually:
cd src/web/frontend && npm run dev
```

### Code Quality
```bash
# Lint code with ruff
ruff check src/

# Fix linting issues
ruff check src/ --fix

# Frontend linting
cd src/web/frontend && npm run lint
```

### Testing
```bash
# Run tests
python tests/test_implicit_reviews.py
python tests/ev_implicit_reviews.py
```

## High-Level Architecture

### Core Components

1. **Common Layer (`src/cmn/`)**: Base abstractions for review processing
   - `Review`: Abstract base class for all review types
   - Dataset-specific implementations: `SemEvalReview`, `TwitterReview`, `MAMSReview`
   - Reviews support augmentation via backtranslation stored in `self.augs`

2. **Aspect Model Layer (`src/aml/`)**: Various aspect detection methods
   - `AbstractAspectModel`: Base class defining the interface for all aspect models
   - Unsupervised methods: LDA, BTM, CTM, BERTopic, Neural LDA
   - Supervised methods: BERT-E2E-ABSA, FastText
   - Each model implements `train()`, `infer()`, and `load()` methods

3. **Pipeline Architecture**: The system follows a modular pipeline approach
   - **prep**: Loads raw reviews and creates pickled review objects
   - **train**: Splits data, trains models with cross-validation
   - **test**: Performs inference with configurable latent aspect ratios
   - **eval**: Evaluates using metrics like P@k, Recall, NDCG, MAP
   - **agg**: Aggregates results across folds and experiments

### Key Design Patterns

1. **Object-Oriented Hierarchy**: All review types and aspect models inherit from abstract base classes, enabling easy extension with new methods or datasets.

2. **Augmentation Integration**: Backtranslation augmentation is built into the review objects and can be seamlessly enabled during training by configuring languages in `params.py`.

3. **Experiment Management**: Output structure maintains clear separation between experiments, models, and evaluation results under organized directory hierarchies.

4. **Stateful Configuration**: All hyperparameters and settings are centralized in `params.py`, including model-specific configurations, GPU settings, and pipeline steps.

### Important Configuration

Key settings in `src/params.py`:
- `settings['cmd']`: Pipeline steps to execute
- `settings['prep']['langaug']`: Languages for backtranslation augmentation
- `settings['train']['ratio']`: Train/test split ratio
- `settings['train']['nfolds']`: Number of cross-validation folds
- GPU configuration via `CUDA_VISIBLE_DEVICES` environment variable

### Extension Points

To add new aspect detection methods:
1. Create a new class inheriting from `AbstractAspectModel` in `src/aml/`
2. Implement required methods: `train()`, `infer()`, `load()`
3. Add configuration parameters to `settings['train']` in `params.py`
4. Import and register the new model in `main.py`

## Experiment Output Structure

Experiment results are organized under `experiment_output/`:
- `exp1_llm_model_ranking/`: LLM model evaluation results
- `exp2_top_models/`: LADy performance analysis with top models
- `exp3_baseline_comparison/`: Comprehensive baseline comparisons

Each experiment directory contains:
- Model outputs and predictions
- Evaluation metrics (P@k, Recall, NDCG, MAP)
- Aggregated results across folds
- Summary reports and visualizations