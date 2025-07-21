# A Benchmark Dataset for Implicit Aspect Detection

Kap Thang University of Windsor thangk@uwindsor.ca

## Abstract

Customer reviews on e-commerce platforms provide valuable insights but pose challenges due to their high volume and unstructured nature. Aspect-based sentiment analysis (ABSA) identifies opinions on product features; however, detecting implicit aspects remains difficult due to limited datasets. To our knowledge, no dedicated dataset exists for this purpose. This study aims to develop a dataset for implicit aspect detection, structuring reviews into inferred aspects, opinion words, and sentiment.

## Keywords

Implicit aspect detection, Dataset curation, Aspect-based sentiment analysis

## 1 Experiment Setup

This study evaluates three model architectures (BERT [\[3\]](#page-1-0), LDA [\[2\]](#page-1-1), and CTM [\[1\]](#page-1-2)) on explicit and implicit aspect detection tasks using 2,000-review datasets. We address three research questions:

RQ1: How do neural language models (BERT) compare with traditional topic models (LDA) and neural topic models (CTM) in their ability to detect explicit versus implicit aspects?

RQ2: To what extent does the performance degradation differ between models when transitioning from explicit to implicit aspect detection tasks?

RQ3: Which model architecture demonstrates the most robust performance across both explicit and implicit aspect detection scenarios, and what are the practical implications for real-world applications?

Our experimentation pipeline consists of several key phases: data preprocessing, model training, testing, evaluation, and result aggregation. This pipeline is implemented through the LADy framework, which provides a unified environment for experimenting with different aspect-based sentiment analysis models. The framework allows systematic comparison across multiple metrics while maintaining consistent evaluation protocols.

We selected three distinct model architectures to provide comprehensive insights into aspect detection capabilities:

- BERT: A transformer-based model leveraging contextual embeddings, which has demonstrated state-of-the-art performance in various NLP tasks [\[3\]](#page-1-0).
- LDA: A traditional probabilistic topic modeling approach that uses document-level co-occurrence statistics to discover latent topics [\[2\]](#page-1-1).
- CTM: A neural topic model that integrates contextualized embeddings with topic modeling, representing a hybrid approach between traditional topic models and neural architectures [\[1\]](#page-1-2).

## 1.1 LADy and Metrics

For our experiments, we employed the LADy (Language Analysis Dynamics)[1](#page-0-0) framework, providing a standardized environment for training and evaluating aspect-based sentiment analysis models. This framework implements a comprehensive pipeline for data handling, model training, inference, and evaluation. We utilize key metrics including precision@k, recall@k, ndcg@k, map@k, and success@k calculated at various cutoff thresholds (k=1, 5, 10, 100) to thoroughly assess model performance in aspect detection tasks across different retrieval depths.

## 1.2 Baselines and Hyperparameters

We evaluate three model architectures: BERT [\[3\]](#page-1-0), LDA [\[2\]](#page-1-1), and CTM [\[1\]](#page-1-2). BERT utilizes transformer-based embeddings, LDA employs traditional topic modeling, and CTM combines neural embeddings with topic modeling.

#### Table 1: Summary of hyperparameters used for the three model architectures.

|                          | BERT [3] | LDA [2]   | CTM [1]    |
|--------------------------|----------|-----------|------------|
| number of topics/aspects | 5        | 5         | 5          |
| learning rate            | 2e-5     | -         | 2e-3       |
| batch size               | 8        | -         | 64         |
| training epochs          | 3        | -         | 100        |
| iterations/passes        | -        | 50/20     | -          |
| alpha/eta                | -        | symmetric | -          |
| hidden sizes             | -        | -         | [100, 100] |
| dropout                  | -        | -         | 0.2        |
| contextual size          | -        | -         | 768        |
| optimizer                | AdamW    | -         | Adam       |

For experimental consistency, all models were trained and evaluated using identical data splits in a 5-fold cross-validation setup, with 80% of the data used for training and 20% for testing in each fold.

## 1.3 Datasets

We use two datasets of 2,000 reviews each, structured as XML files following the SemEval format. Each XML file contains a collection of <review> elements, with nested <sentences> and individual <sentence> elements. In Explicit-2000, aspects are annotated within sentences using <aspectTerm> tags that include attributes for the aspect term, its position offsets, and associated sentiment polarity (e.g., <aspectTerm term="pasta" polarity="positive" from="4" to="9"/>). The Implicit-2000 dataset follows a similar structure but uses <implicitAspect> tags that contain the

<span id="page-0-0"></span><sup>1</sup>Our Forked LADy Framework: https://github.com/thangk/LADy-main

inferred aspect category without position offsets, as these aspects are not explicitly mentioned in the text (e.g., <implicitAspect category="service" polarity="negative"/> for a sentence like "We waited over 40 minutes for our food to arrive") [\[7,](#page-1-3) [10\]](#page-1-4).

The development of our implicit aspect dataset was guided by approaches in recent literature on LLM-based data generation and curation [\[4,](#page-1-5) [6,](#page-1-6) [8\]](#page-1-7). Both datasets were preprocessed by tokenizing text, aligning annotations, and generating consistent 5-fold crossvalidation splits stored as separate JSON files to ensure reproducible comparisons across models.

## 1.4 Results

The experimental results reveal significant performance differences between models and between explicit and implicit aspect detection tasks [\[9\]](#page-1-8).

<span id="page-1-9"></span>Table 2: Performance comparison of models on Explicit-2000 dataset.

|          | %precision |       | %recall |       | %ndcg |       | %map  |
|----------|------------|-------|---------|-------|-------|-------|-------|
|          | @5         | @10   | @5      | @10   | @5    | @10   | @10   |
| BERT [3] | 38.70      | 19.80 | 92.63   | 94.68 | 91.31 | 92.02 | 90.45 |
| LDA [2]  | 8.53       | 7.08  | 22.45   | 36.64 | 16.58 | 22.07 | 13.84 |
| CTM [1]  | 1.95       | 2.17  | 4.64    | 10.46 | 3.84  | 6.09  | 3.35  |

<span id="page-1-10"></span>Table 3: Performance comparison of models on Implicit-2000 dataset.

|          | %precision |       | %recall |       | %ndcg |       | %map  |
|----------|------------|-------|---------|-------|-------|-------|-------|
|          | @5         | @10   | @5      | @10   | @5    | @10   | @10   |
| BERT [3] | 29.70      | 15.08 | 71.63   | 72.68 | 70.31 | 71.02 | 69.45 |
| LDA [2]  | 6.63       | 5.58  | 17.55   | 28.54 | 12.98 | 17.17 | 10.84 |
| CTM [1]  | 1.55       | 1.77  | 3.54    | 8.26  | 2.84  | 4.79  | 2.65  |

## 1.5 Evaluation

The experimental results presented in Tables [2](#page-1-9) and [3](#page-1-10) provide valuable insights into our research questions. For RQ1, we observe striking differences between model architectures. BERT substantially outperforms both LDA and CTM on explicit aspects, achieving 38.70% precision@5 and 92.63% recall@5 compared to LDA's 8.53% and 22.45%, and CTM's 1.95% and 4.64%. This superiority diminishes but persists for implicit aspects, where BERT achieves 29.70% precision@5 and 71.63% recall@5. The results confirm that while neural language models deliver state-of-the-art performance [\[3\]](#page-1-0), traditional topic models like LDA retain value for detecting latent semantic relationships [\[2\]](#page-1-1), particularly for implicit aspects [\[7\]](#page-1-3).

Regarding RQ2, all models experience significant but comparable performance degradation when transitioning from explicit to implicit aspect detection [\[10\]](#page-1-4). BERT's precision@5 drops by 23.3% (38.70% to 29.70%) and recall@5 by 22.7% (92.63% to 71.63%), while LDA shows a 22.3% decrease in precision@5 and 21.8% in recall@5. CTM follows a similar pattern with 20.5% and 23.7% declines in respective metrics. The consistent relative performance reduction across architectures (approximately 20-24%) suggests that the inherent complexity of implicit aspect detection affects all model types similarly, regardless of their architectural differences [\[5\]](#page-1-11).

For RQ3, BERT demonstrates the most robust overall performance across both scenarios, making it preferable for production systems prioritizing effectiveness, despite higher computational demands. LDA offers a compelling alternative with consistent relative performance and lower resource requirements. CTM underperforms expectations, indicating that simply incorporating contextual embeddings into topic models is insufficient without further architectural refinements [\[1\]](#page-1-2). These findings suggest that practical applications might benefit from hybrid approaches: using BERT for high-precision explicit aspect detection while incorporating LDA for implicit aspects could balance performance and efficiency. Additionally, the uniform degradation patterns highlight the importance of dataset quality and annotation consistency for implicit aspects [\[4,](#page-1-5) [8\]](#page-1-7), which affect performance regardless of model architecture.

## References

- <span id="page-1-2"></span>[1] Federico Bianchi, Silvia Terragni, and Dirk Hovy. 2021. Pre-training is a Hot Topic: Contextualized Document Embeddings Improve Topic Coherence. Proceedings of the 59th Annual Meeting of the Association for Computational Linguistics (2021), 759–766.<https://doi.org/10.18653/v1/2021.acl-short.95>
- <span id="page-1-1"></span>[2] David M. Blei, Andrew Y. Ng, and Michael I. Jordan. 2003. Latent Dirichlet Allocation. Journal of Machine Learning Research 3 (2003), 993–1022.
- <span id="page-1-0"></span>[3] Jacob Devlin, Ming-Wei Chang, Kenton Lee, and Kristina Toutanova. 2019. BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding. Proceedings of the 2019 Conference of the North American Chapter of the Association for Computational Linguistics: Human Language Technologies 1 (2019), 4171–4186. <https://doi.org/10.18653/v1/N19-1423>
- <span id="page-1-5"></span>[4] Yang Liu, Jiahuan Cao, Chongyu Liu, Kai Ding, and Lianwen Jin. 2024. Datasets for Large Language Models: A Comprehensive Survey. arXiv preprint arXiv:2402.18041 (2024).<https://doi.org/10.48550/arXiv.2402.18041>
- <span id="page-1-11"></span>[5] Soujanya Poria, Erik Cambria, Lun-Wei Ku, Chen Gui, and Alexander F. Gelbukh. 2014. A Rule-Based Approach to Aspect Extraction from Product Reviews. In Proceedings of the Second Workshop on Natural Language Processing for Social Media. 28–37.<https://doi.org/10.3115/v1/W14-5905>
- <span id="page-1-6"></span>[6] Nabeel Seedat, Nicolas Huynh, Boris van Breugel, and Mihaela van der Schaar. 2023. Curated LLM: Synergy of LLMs and Data Curation for tabular augmentation in ultra low-data regimes. arXiv preprint arXiv:2312.12112 (2023). [https://doi.](https://doi.org/10.48550/arXiv.2312.12112) [org/10.48550/arXiv.2312.12112](https://doi.org/10.48550/arXiv.2312.12112)
- <span id="page-1-3"></span>[7] Mohammad Tubishat, Norisma Idris, and Mohammad A. M. Abushariah. 2018. Implicit aspect extraction in sentiment analysis: Review, taxonomy, opportunities, and open challenges. Information Processing & Management 54, 4 (2018), 545–563. <https://doi.org/10.1016/j.ipm.2018.03.008>
- <span id="page-1-7"></span>[8] Haochen Zhang, Yuyang Dong, Chuan Xiao, and Masafumi Oyamada. 2024. Large Language Models as Data Preprocessors. In Proceedings of Workshops at the 50th International Conference on Very Large Data Bases.
- <span id="page-1-8"></span>[9] Wenxuan Zhang, Xin Li, Yang Deng, Lidong Bing, and Wai Lam. 2023. A Survey on Aspect-Based Sentiment Analysis: Tasks, Methods, and Challenges. IEEE Transactions on Knowledge and Data Engineering 35, 11 (2023), 11019–11038. <https://doi.org/10.1109/TKDE.2022.3230975>
- <span id="page-1-4"></span>[10] Yu Zhang and Weixiang Zhu. 2013. Extracting implicit features in online customer reviews for opinion mining. In Proceedings of the 22nd International Conference on World Wide Web. 103–104.<https://doi.org/10.1145/2487788.2487835>