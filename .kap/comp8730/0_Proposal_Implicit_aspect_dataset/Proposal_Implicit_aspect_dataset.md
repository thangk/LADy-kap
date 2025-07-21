# A Benchmark Dataset for Implicit Aspect Detection

Sepideh Ahmadian University of Windsor ahmadia3@uwindsor.ca

## Abstract

Customer reviews on e-commerce platforms provide valuable insights but are challenging to analyze due to their volume and unstructured nature. Aspect-based sentiment analysis (ABSA) identifies opinions on product features, yet detecting implicit aspects remains difficult due to limited specialized datasets. This study develops a framework for implicit aspect detection, structuring reviews into inferred aspects, opinion words, and sentiment. It ensures diversity and strong opinion-aspect connections, enhancing review analysis tools and uncovering useful insights.

## Keywords

implicit aspect detection, dataset curation, aspect-based sentiment analysis

# 1 INTRODUCTION

Unsolicited online reviews provide valuable customer insights but are challenging to analyze manually due to their volume and unstructured nature. Traditional sentiment analysis's single-sentiment approach is limiting. ABSA improves this by linking opinions to specific aspects [\[3\]](#page-0-0). For example, "The pizza is delicious, but the waiter was rude." expresses distinct sentiments for food and service. Reviews often contain implicit aspects, where aspects must be inferred from context, like "Mine was a little burnt!" implying poor quality. By addressing both explicit and implicit aspects, ABSA enhances sentiment analysis accuracy and practical utility.

# 2 MOTIVATION

Significant progress has been made in explicit aspect detection, supported by numerous datasets. However, challenges persist in handling implicit aspects where meanings must be inferred from context. This is particularly challenging in brief, informal online reviews that businesses commonly analyze. Creating quality training data that captures these nuances remains a major challenge. Previous attempts, like Hemmatizadeh et al. [\[1\]](#page-0-1)'s approach of masking explicit aspects, had limitations as they altered the grammatical structure of sentences and potentially affected the performance of models sensitive to grammar.

To the best of our knowledge, no such comprehensive dataset currently exists, underscoring the need for its creation to facilitate high-quality research and robust model development in this field.

## 3 PROBLEM STATEMENT

To understand implicit aspect detection, it is helpful to first introduce ABSA through an example. Consider the review: "The pizza is delicious, but the waiter was rude." This provides feedback on two aspects: food ("pizza") and service ("waiter"), each with a sentiment. ABSA identifies these as targets and sentiments [\[2\]](#page-0-2). A target can be an aspect term (e.g., "pizza") or an aspect category (e.g., "food").

Kap Thang University of Windsor thangk@uwindsor.ca

Some targets are implicit, as in "It is overpriced!" where "price" is inferred. When no explicit aspect is named, the target may be marked as "null." The sentiment component includes the opinion term (e.g., "delicious") and its polarity (e.g., negative). In the earlier example, ABSA identifies "pizza" and "waiter" as aspect terms, "food" and "service" as aspect categories, and assigns positive and negative polarities. This demonstrates ABSAś fine-grained sentiment analysis.

Implicit aspect detection extends this by uncovering unstated aspects. For instance, in "It is overpriced," the aspect "price" must be inferred. This task requires advanced computational techniques to identify and analyze implicit targets and sentiments.

# 3.1 Definition

Let = {1, 2, . . . , } represent a dataset of reviews ( is the number of reviews), where each review is a sequence of sentences = {1, 2, . . . , }. A dataset suitable for implicit aspect detection can be defined as a collection of tuples:

$$
D = \{ (r_i, A_i, O_i, P_i) \}, \quad \text{for } i = 1, ..., m
$$

Where:

- : A review text where aspects are implied and not explicitly stated.
- = {1, 2, . . . , }: The set of implicit aspect categories inferred from . For example, in "This is overpriced" = {price}.
- = {1, 2, . . . , }: The corresponding opinion terms or phrases that indicate the implied aspects (e.g., "overpriced").
- = {1, 2, . . . , }: The sentiment polarities (e.g., positive, negative, neutral) associated with each implicit aspect ∈ .

## 4 TEAM JUSTIFICATION

Sepideh Ahmadian contributes her expertise in natural language processing (NLP), particularly in ABSA, leveraging her knowledge of the literature to guide effective project design, evaluate the dataset.

Kap Thang rings his experience in Python and neural networks, along with a strong research background, to support the design and analysis of methods for dataset curation.

## References

- <span id="page-0-1"></span>[1] Farinam Hemmatizadeh, Christine Wong, Alice Yu, and Hossein Fani. 2023. Latent Aspect Detection via Backtranslation Augmentation. In Proceedings of the 32nd ACM International Conference on Information and Knowledge Management, CIKM 2023, Birmingham, United Kingdom, October 21-25, 2023, Ingo Frommholz, Frank Hopfgartner, Mark Lee, Michael Oakes, Mounia Lalmas, Min Zhang, and Rodrygo L. T. Santos (Eds.). ACM, United Kingdom, 3943–3947. [https://doi.org/10.1145/](https://doi.org/10.1145/3583780.3615205) [3583780.3615205](https://doi.org/10.1145/3583780.3615205)
- <span id="page-0-2"></span>[2] Bing Liu. 2012. Sentiment Analysis and Opinion Mining. Morgan & Claypool Publishers.<https://doi.org/10.2200/S00416ED1V01Y201204HLT016>
- <span id="page-0-0"></span>[3] Wenxuan Zhang, Xin Li, Yang Deng, Lidong Bing, and Wai Lam. 2023. A Survey on Aspect-Based Sentiment Analysis: Tasks, Methods, and Challenges. IEEE Trans. Knowl. Data Eng. 35, 11 (2023), 11019–11038. [https://doi.org/10.1109/TKDE.2022.](https://doi.org/10.1109/TKDE.2022.3230975) [3230975](https://doi.org/10.1109/TKDE.2022.3230975)