# DSGram: Dynamic Weighting Sub-Metrics for Grammatical Error Correction in the Era of Large Language Models  

Anonymous submission  

# Abstract  

Evaluating the performance of Grammatical Error Correction (GEC) models has become increasingly challenging, as large language model (LLM)-based GEC systems often produce corrections that diverge from provided gold references. This discrepancy undermines the reliability of traditional reference-based evaluation metrics. In this study, we propose a novel evaluation framework for GEC models, DSGram, integrating Semantic Coherence, Edit Level, and Fluency, and utilizing a dynamic weighting mechanism. Our framework employs the Analytic Hierarchy Process (AHP) in conjunction with large language models to ascertain the relative importance of various evaluation criteria. Additionally, we develop a dataset incorporating human annotations and LLM-simulated sentences to validate our algorithms and fine-tune more cost-effective models. Experimental results indicate that our proposed approach enhances the effectiveness of GEC model evaluations.  

# Introduction  

Grammatical Error Correction (GEC) models aims to automatically correct grammatical errors in natural language texts, enhancing the quality and accuracy of written content. Traditionally, the evaluation of GEC models has employed a variety of metrics, categorized into those requiring a reference (reference-based evaluation) and those that do not (reference-free evaluation).  

Reference-based metrics such as BLEU (Papineni et al. 2002), ERRANT (Bryant, Felice, and Briscoe 2017), and $\mathbf{M}^{2}$ (Dahlmeier and $\mathrm{Ng}\,2012,$ ) compare the model-generated text with a correct reference text to evaluate the accuracy of grammatical corrections, and they are widely used in this area. Despite the usefulness of these metrics, they possess inherent limitations. For example, the golden reference may not encompass all potential corrections (Choshen and Abend 2018), and the alignment of existing automatic evaluation metrics with human judgment is often weak (Coyne et al. 2023). Additionally, LLMs-based GEC models may excessively correct sentences, resulting in unnecessary editing not captured by traditional metrics (Fang et al. 2023b).  

Conversely, reference-free metrics like Perplexity, GLEU (Napoles et al. 2015), and SOME (Yoshimura et al. 2020) assess the quality of generated text directly, proving beneficial when a correct reference text is unavailable, or when a single (or several) reference is insufficient for GEC evaluation Input sentence: Though it is said that genetic testing involves emotional and social risks due to the test results, while the potential negative impacts of the risk still exist, the consequence will be significant if other members of his or her family do not know.  

Reference: Though it is said that genetic testing involves emotional and social risks due to the test results, while the potential negative impacts of the risk still exist, the consequences will be significant if other members of his or her family do not know.  

BLEU: 1.00       F0.5 Score: 1           SOME: 0.83ChatGPT Output Sentence: Though it is said that genetic testing involves emotional and social risks due to the test results, and the potential negative impacts of the risk still exist, the consequences will be significant if other members of his or her family do not know.  

Grammarly Output Sentence: Though it is said that genetic testing involves emotional and social risks due to the test results, while the potential negative impacts of the risk still exist, the consequence will be significant if other members of his or her family do not know.  

BLEU: 0.94        F0.5 Score: 0          SOME: 0.81 Figure 1: Running examples and evaluation results of several existing metrics. The sentence is from Fang et al. (2023b). This figure illustration presents the outcomes of an input sentence processed through two representative GEC models and also the corresponding reference. The metrics’ scores are placed under the output. The highlighted in blue represents the over-correction. The highlighted in red indicates poor fluency. Notably, BLEU fails to differentiate between over- and under-correction, whereas SOME cannot capture over-correction.  

in the era of LLMs. While existing reference-free evaluation metrics like SOME offer an analytical foundation, they no longer fully encompass the scope of GEC evaluation. It is necessary to design new sub-metrics based on the original sub-metrics and emerging requirements. Figure 1 shows running examples of representative GEC systems and the limitations of current evaluation metrics.  

This study seeks to overcome these limitations by introducing a novel evaluation framework, DSGram, for GEC models. DSGram integrates Semantic Coherence, Edit Level, and Fluency, and determines the appropriate weights  

![](images/be33ac2d757c61bdcedf2ef722c48ce251ed9a923e78de268180d6059813b972.jpg)  

Figure 2: Architecture of the DSGram method. It begins with the input of sentence pairs (original and corrected). Large language models (such as GPT-4 and LLaMA3) are employed to generate dynamic weights, which are further refined through a judgment matrix and a consistency check. These dynamic weights are normalized and then used to score sub-metrics, which include semantic coherence, edit level, and fluency. The final output consists of both the calculated weights and the overall score for the evaluated correction.  

for these criteria. By initially training a weighting system using human-annotated datasets and later adjusting these weights based on the evaluation scenario’s context, a more nuanced and context-sensitive evaluation approach can be developed.  

The main contributions of this paper are summarized as follows:  

• We introduce new sub-metrics for GEC evaluation, diverging from previous categorical approaches. Our metrics optimize past sub-metrics by adding an evaluation of over-editing.   
• We propose a novel dynamic weighting-based GEC evaluation method, DSGram, which integrates the Analytic Hierarchy Process (Ana 1987) with large language models to ascertain the relative importance of different evaluation criteria.   
• We present two datasets: DSGram-Eval, created through human scoring, and DSGram-LLMs, a larger dataset designed to simulate human scoring for fine-tuning. Both datasets utilize sentences from the CoNLL-2014 and BEA-2019 test sets to facilitate rigorous evaluation.  

# Related Work  

Numerous studies have focused on evaluating GEC models. This section provides an overview of key research utilizing LLMs for GEC assessment.  

# Model-Based Evaluation Metrics  

Model-based evaluation metrics have garnered significant attention, especially in the domain of GEC. BLEURT (Sellam, Das, and Parikh 2020) is a versatile metric that evaluates based on (prediction, reference) pairs. It employs a scalable pretraining phase where it learns to predict automatically generated signals of supervision from semantically comparable synthetic pairs.  

A burgeoning trend in automatic evaluation involves the direct application of LLMs for assessment purposes. Liu et al. (2023) have LLMs generate numerical ratings by interpreting descriptions of evaluation criteria through a chainof-thought method (Wei et al. 2023). Sottana et al. (2023) demonstrate the viability of GPT-4 in GEC assessment, highlighting an approach that uses natural language instructions to define evaluation criteria.  

# Reference-Free Evaluation  

Reference-free evaluation is a method of assessing the performance of models without relying on reference. Asano, Mizumoto, and Inui (2017) integrate three sub-metrics, which are Grammaticality, Fluency, and Meaning Preservation, to surpass reference-based metrics. They employ a language model and edit-level metrics as Fluency and Meaning Preservation sub-metrics, respectively, although these sub-metrics are not tailored for manual evaluation. The final score is determined through a weighted linear summation of each individual evaluation score.  

SOME is a reference-free GEC metric that follows the approach of Asano, Mizumoto, and Inui (2017). It utilizes three distinct BERT models, each dedicates to one scoring aspect. The researchers construct a novel dataset for training these BERT models by annotating the outputs from various GEC systems on the CoNLL-2013 test set across the three scoring dimensions.  

However, in real-world applications, the use of just three evaluation metrics presents a limitation, as it hinders the generation of a holistic score in an inherently intuitive way. Consequently, this constraint diminishes its effectiveness in directing the training of GEC models. In the sphere of GEC evaluation, Wu et al. (2023) assessed ChatGPT and observed a tendency towards over-correction, which might be attributed to its extensive generative capacities as a large language model. Similarly, Fang et al. (2023b) noted that ChatGPT makes over-corrections, leading to revised sentences with high fluency. These findings align with the outcomes of our own experiments, which indicate that existing referencefree metrics do not adequately reflect these issues.  

# GEC Dataset with Human Scoring  

There are very few GEC datasets with human evaluation scores. The dataset annotated by Yoshimura et al. (2020) includes individual scores for Grammaticality, Fluency, and Meaning Preservation, but lacks an overall score. GJG15 (Grundkiewicz, Junczys-Dowmunt, and Gillian 2015) and SEEDA (Kobayashi, Mita, and Komachi 2024) are manually annotated but provide ranking information rather than scores. Sottana et al. (2023) conducted ratings based on the gold standard, which presents certain limitations.  

Sottana et al. (2023) suggested the potential of GPT-4 as a reviewer, prompting us to consider using GPT-4 to annotate a dataset that simulates human scoring for GEC model evaluation.  

# DSGram: Dynamic Weighting Sub-Metrics for GEC  

DSGram comprises two main components: score generation and weight generation. By applying specific weights to the generated scores, an overall score is obtained. Figure 2 illustrates the method’s flowchart.  

# Generating Scores  

Sub-Metrics Definition Upon our analysis, the three submetrics introduced by Asano, Mizumoto, and Inui (2017), exhibit redundancy and insufficiency. We compute the correlation of these metrics using the SOME’s dataset, and the results are depicted in Figure 3.  

The heatmap reveals a high correlation between Grammaticality and Fluency. Hence, we have combined these two metrics into a single ”Fluency” measure. Furthermore, to address the issue of over-corrections present in LLM-based GEC models, we have incorporated a new sub-metric called ”Edit Level” to evaluate concerns associated with excessive corrections. Based on the computation of 200 humanannotated scores, The correlation of these novel sub-metrics is shown in Figure 4. It can be observed that our classification criteria have improved the sub-metrics’ distribution.  

In our definition, the meanings of the three sub-metrics are as follows:  

Semantic Coherence The degree to which the meaning of the original sentence is preserved in the corrected sentence. It evaluates whether the corrected sentence conveys the same intended meaning as the original, without introducing semantic errors or altering the core message.  

![](images/e83dc7dbee880e0bbcf2cef82017873cc870fe8bb7a9fc7c50935b5361893cb2.jpg)  
Figure 3: Heat map of the correlations among the three sub-metrics of SOME. avg g, avg f, and avg m denote the average scores for Grammaticality, Fluency, and Meaning Preservation, respectively. The correlation between avg g and avg f is notably strong at 0.89.  

![](images/d56797fb20e86ffe9c741621521c09f5ee127dab2984cc2992481b08e24d73bc.jpg)  
Figure 4: Heat map of our three sub-metrics correlations. avg s, avg e, and avg f denote the average scores for Semantic Coherence, Edit Level and Fluency, respectively. The correlation has become more evenly distributed.  

Edit Level The extent to which the GEC model has modified the sentence. It assesses whether the corrections made are necessary and appropriate, or if the sentence has been unnecessarily or excessively altered, deviating from the original prose more than required.  

Fluency The grammatical correctness and the natural flow of the corrected sentence. It evaluates whether the sentence adheres to proper grammar rules, has a coherent structure, and reads smoothly without awkward phrasing or unnatural constructions.  

These three sub-metrics comprehensively cover the key aspects that GEC models need to consider in the era of large language models.  

![](images/d6e29677637f173882fb7ce45e52d0ec8c8b9593194fb8a3b96c846705b7ce09.jpg)  
Figure 5: The DSGram score computation processes for two different sentences. Sentence (a) represents a casual daily dialogue where the emphasis is on Fluency. Sentence (b) is a more formal expression, thus placing a greater emphasis on the Edit Level.  

Sub-Metrics Score Generation We employ prompt engineering techniques such as chain-of-thought (Wei et al. 2023), few-shot (Brown et al. 2020) and output reason before scoring (Chu, Chen, and Nakayama 2024) to craft the prompts, enabling LLMs to generate scores based on the aforementioned three sub-metrics. The specific prompts can be found in Appendix.  

The method is applicable to a variety of different LLMs. Our experiments reveal that the GPT-4 and GPT-4o exhibit greater scoring consistency and alignment with human judgments. Models such as LLaMA2 and LLaMA3 struggle to adhere to structured prompts and do not effectively grasp the scoring tasks. To reduce evaluation costs and enhance model usability, we annotate a dataset simulating human scoring with GPT-4 to fine-tune open-source LLMs, exploring their feasibility for scoring. We utilize GPT-4 to annotate DSGram-LLMs and subsequently fine-tune the LLaMA2- 13B and LLaMA3-8B models on this dataset, thereby confirming their consistency with human scores.  

In summary, we achieve the automatic generation of evaluation scores with prompting GPT-4 and open-source LLMs like LLaMA.  

# Generating Dynamic Weights  

The prevalent approach using sub-metrics methods typically involves assigning specific weights to compute an overall score. However, we contend that in the context of GEC evaluation tasks, human judges may have varying considerations for different sentences. It is essential to adjust the weights according to the evaluation scenario; for formal documents such as legal texts and medical instructions, a stricter standard is required for Semantic Coherence and Edit Level. In contrast, for more relaxed contexts like dialogues, Fluency should be given priority in the assessment.  

We employ LLMs in conjunction with the Analytic Hierarchy Process (AHP) (Ana 1987) to generate varying weights for different sentences, thereby making the weighted scores more aligned with human scoring. AHP is a decision-making framework that decomposes complex decisions into a hierarchy of simpler, more manageable elements through pairwise comparisons. It synthesizes subjective judgments into objective priorities that are appropriately weighted based on a structured evaluation of criteria and objectives.  

The weight generation algorithm involves the following key steps:  

Constructing the Judgement Matrix : For each criterion, utilize LLMs to construct a pairwise comparison matrix $A=(a_{i j})$ , where $a_{i j}$ represents the importance of criterion $i$ relative to criterion $j$ . The specific prompt for this process is documented in the Appendix.  

Consistency Check : Compute the consistency index (CI) and consistency ratio (CR) to check the consistency of the pairwise comparison matrix.  

$$
C I=\frac{\lambda_{\operatorname*{max}}-n}{n-1},
$$  

$$
C R={\frac{C I}{R I}},
$$  

where RI is the Random Index for a matrix of order $n$ . For instance, an RI value of 0.58 is used for a $3\mathrm{x}3$ matrix, while for a $4\mathrm{x4}$ matrix, RI is 0.90, and this pattern continues. A CR below 0.1 indicates that the pairwise comparison matrix possesses sufficient consistency.  

Calculating the Eigenvector and Eigenvalue : Solve the eigenvalue problem $A\mathbf{w}~=~\lambda_{\operatorname*{max}}\mathbf{w}$ to obtain the maximum eigenvalue $\lambda_{\mathrm{max}}$ and the corresponding eigenvector w,which is then normalized.  

Calculating the Composite Weight : Compute the composite weights by multiplying the weights at each level.  

Initialize: Define criteria set $C=C_{1},C_{2},...,C_{n}$ (e.g., Semantic Coherence, Edit Level, Fluency)  

# Construct Judgment Matrices:  

each pair of criteria $(C_{i},C_{j})$ , each sentence $S_{k}$ Construct judgment matrix $A=[a_{i j}]$ utilizing LLMs for $S_{k}$ where $a_{i j}$ represents the relative importance of $C_{i}$ to $C_{j}$ in the context of this sentence  

# Consistency Check:  

each judgment matrix $A$ Calculate Consistency Index (CI) and Consistency Ratio (CR) $\operatorname{CR}\geq\theta$ Adjust ratings in $A$ until $\mathbf{CR}<\theta$  

# Calculate Weights:  

each judgment matrix $A$ Compute the principal eigenvector $\mathbf{w}=(w_{1},w_{2},\ldots,w_{n})$ of $A$ Normalize the principal eigenvector to obtain weights $w_{i}$ for each criterion $C_{i}$ :  

# Compute Aggregated Scores:  

each s where $s_{k,i}$ is the score of nce $S_{k}$ Calcula $S_{k}$ weighted on criterion o$C_{i}$ $\begin{array}{r}{W_{k}=\sum_{i=1}^{n}w_{i}\cdot s_{k,i}}\end{array}$ ·  

The procedure for this approach is outlined in Algorithm 1. Figure 5 depicts an illustration of this method for two distinct sentence pairs. Sentence (a) represents a casual daily dialogue where the emphasis is on Fluency. On the other hand, sentence (b) is a more formal expression with a higher level of professionalism, thus placing a greater emphasis on the Edit Level.  

# Experiments and Analysis  

# Datasets Preparation  

We mainly evaluate our metrics using the SEEDA dataset (Kobayashi, Mita, and Komachi 2024), and compare them with existing metrics. The SEEDA corpus comprises human-rated annotations at two distinct evaluation levels: edit-based (SEEDA-E) and sentence-based (SEEDA-S), and encompasses corrections from 12 cutting-edge neural systems, including large language models, as well as corrections made by humans.  

In addition to SEEDA, we also manually annotate an evaluation dataset with human scores for validation purposes. We name this human-annotated dataset DSGram-Eval. For details on annotating this dataset, please refer to the Appendix.  

Furthermore, we construct an additional training set by utilizing system outputs from the BEA-2019 shared task official website 2. After removing biased output groups, a sample of approximately 2500 entries is randomly selected. Then GPT-4 is employed to generate the sub-metrics scores, which we refer to as DSGram-LLMs. This set is subsequently used for model training.  

# Meta-Evaluation of Our Metrics  

We evaluate the performance of various GEC models using the DSGram metric on the SEEDA dataset. GPT-3.5 secures the highest score in our DSGram metric, aligning with the findings in Fang et al. (2023b), which report ChatGPT as having the highest human score in fluency. Despite it exhibiting substandard scores in metrics like $M^{2}$ , we still regard it as an outstanding GEC system. Our metric demonstrates equally outstanding performance on models like T5 (Rothe et al. 2021) and TransGEC (Fang et al. 2023a) that excel in GLEU and ERRANT. However, the REF-F model, despite boasting the highest SOME score, underperforms significantly when assessed using our metric. The results are shown in Table 1.  

We calculate the correlation between the ranking according to DSGram and human ranking on the SEEDA dataset and compare it with existing metrics. Specifically, we convert the concrete data obtained in Table 1 into human rankings and compare them with the human rankings in the dataset, obtaining the corresponding correlation. The comparison is presented in Table 2 and the obtained correlation is presented in Table 3.  

The results indicate that the correlation of our metric with human feedback surpasses that of all conventional referencebased metrics, as well as reference-free metrics like GLEU, Scribendi Score and DSGram weighted by 0.33.  

# Validation of LLMs Scoring  

To verify the broad applicability of our metrics, we conduct tests on DSGram-Eval dataset by using various LLMs (including few-shot and finetuned LLaMA models) to generate the sub-metric scores and then calculate the correlation between the sub-metric/overall scores with human scores. The correlation results in Table 4 indicate that GPT-4 consistently shows a high correlation with human scores across three sub-metrics. In contrast, LLaMA3-70B is found to be highly correlated in Semantic Coherence and Fluency but less so in Edit Level. Since the few-shot LLaMA performs poorly, we fine-tune LLaMA on the DSGram-LLMs dataset. The fine-tuned LLaMA3-8B and LLaMA2-13B models outperform their original few-shot outcomes, demonstrating that LLMs can achieve higher human-aligned scores for GEC tasks, while fine-tuning smaller LLMs serves to reduce evaluation costs and enhance model usability.  

# Validation of Dynamic Weights  

To ascertain the efficacy of the dynamic weights in DSGram, we conduct a comparison between the average overall score assigned by human annotators and the score weighted by generated dynamic weights derived from human-labeled sub-metrics.  

![](images/56e5772d708bafaf9f393ff46c922fdabf59eebde3c63b6b1a09f2518a653845.jpg)  

Table 1: Scores of common GEC models w.r.t various existing metrics and DSGram on SEEDA dataset. The bold part indicates the model with the highest score under each metric. Certain GEC models with low scores have been omitted from this table.   

![](images/da8b2b8647207b5762c4c55d4dcee038baf198a10f9927c4a9ee429fe9397f5c.jpg)  
Table 2: A comparative analysis of the DSGram Score and the Human Score in SEEDA, as ranked by the DSGram Score, indicates a favorable correlation, with our scores exhibiting a close alignment to those assigned by humans.  

We calculate the AHP Human Score by applying dynamic weights to the human-annotated Semantic Coherence, Edit Level, and Fluency scores. The Pearson correlation coefficient between the AHP Human Score and the humanannotated overall score is 0.8764 . Subsequently, we compute the overall score derived from the average weighting method and correlated it with the human scoring overall score. By assigning an equal weight of 0.33 to each metric, we obtain the AVG Human Score. The calculation reveal a Pearson correlation coefficient of 0.8544 between the AVG Human Score and the human scoring overall score.  

Our experimental findings indicate that when the overall score is adjusted using dynamic weights, it significantly corresponds with the evaluations provided by human annotators, and the dynamic weighting approach outperforms the average weighting method at a relaxed significance level.  

It is suggested that, although human annotators do not consciously consider the specific weights of each metric while annotating, they implicitly gauge their relative importance. This intuitive evaluation process resembles the creation of a judgment matrix, and our method effectively mimics the human scoring procedure.  

To further verify the effectiveness, we select three distinctly different text datasets: OpenSubtitles 3, Justia (CaseLaw) 4, and Wikipedia Corpus, to represent everyday conversations, legal documents, and technical explanations, respectively. For each dataset, we generate weights for three sub-metrics and evaluate the internal consistency of these weights using Cronbach’s Alpha coefficient (Cronbach 1951). Specifically, we preprocess each dataset to extract sub-metric scores and then calculate Cronbach’s Alpha based on the generated weights to assess the consistency and reliability of the sub-metrics across different datasets.  

The experimental results demonstrate that the Cronbach’s Alpha coefficients for the OpenSubtitles, Justia (CaseLaw), and Wikipedia Corpus datasets are 0.76 ,0.82 , and 0.79 , respectively, all exceeding the acceptable threshold of 0.7.  

This indicates that the weights generated in different scenarios exhibit high consistency and reliability. The weights in the everyday conversation scenario show good consistency, while the weights in the legal and technical documentation scenarios perform even better. Overall, this experiment validates the effectiveness of the weight generation algorithm across different text types, demonstrating its robustness and reliability in diverse applications.  

![](images/ed458749b4b1704e7b88a9414de454bd81b3c160977c2c6e42d1a41dd9d9f804.jpg)  

Table 3: System-level and sentence-level meta-evaluation results of common GEC models. We follow the Kobayashi, Mita, and Komachi (2024),use Pearson (r) and Spearman $(\rho)$ for system-level and Accuracy (Acc) and Kendall $(\tau)$ for sentencelevel meta-evaluations. The sentence-based human evaluation dataset is denoted SEEDA-S and the edit-based one is denoted SEEDA-E. The score in bold represents the metrics with the highest correlation at each granularity.   
Table 4: Pearson correlation with human scoring on DSGram-Eval across various LLMs   

![](images/3a189833633e7117b2fccc5279091d886fc60809eef40163b117ba6d86318318.jpg)  

# Discussion  

Human Feedback For reference-free evaluation metrics, their effectiveness is often gauged by how closely they mirror human judgments. Although ChatGPT is rated as the best model in terms of grammar by human reviewers, it is rated as the worst or second-worst model in terms of semantics and over-correction (Sottana et al. 2023). This suggests that human feedback also seems to have its limitations.  

LLMs as Reviewers The utilization of large language models to construct evaluation metrics has been found to closely approximate human feedback, thereby enhancing the accuracy of assessment. Additionally, the dynamic evaluation approach demonstrates a degree of rationality, which can potentially be transferred to other forms of assessment.  

Applications The metrics in question may prove applicable in guiding the training of GEC models. For largescale models, the rate of gradient descent during the training process may vary across different tasks. For instance, the model’s ability to correct semantic errors may have already saturated, whereas there is still room for improvement in terms of fluency correction. Employing this metric to construct the loss function could potentially enhance the training of GEC models.  

# Conclusions  

This study presents an evaluation framework for Grammatical Error Correction models that integrates Semantic Coherence, Edit Level, and Fluency through a dynamic weighting system. By leveraging the AHP in conjunction with LLMs, we have developed a method that dynamically adjusts the importance of different evaluation criteria based on the context, leading to a more nuanced and accurate assessment.  

Through extensive experiments, our methodology has demonstrated strong alignment with human judgments, particularly when utilizing advanced LLMs like GPT-4. The dynamic weighting system has shown considerable promise in mirroring the intuitive scoring processes of human annotators, thereby validating its application in various contexts.  

Looking forward, several avenues for future research are evident. First, it is imperative to conduct more extensive tests of this method across a broader range of LLMs, including Claude, GLM and Qwen. Moreover, a more comprehensive validation of the dynamic evaluation approach is required, to explore its applicability in diverse evaluation contexts.  

However, GPT-4, utilized in creating judgment matrices, is not freely available to the public and might require specific authorization or payment for its use. This limitation could hinder the broad adoption of our evaluation method. Moreover, the performance of the fine-tuned LLaMA model is suboptimal and might necessitate additional high-quality data for effective fine-tuning.  

# References  

1987. The Analytic Hierarchy Process—What It Is and How It Is Used. Mathematical Modelling , 9(3-5): 161–176.  

Asano, H.; Mizumoto, T.; and Inui, K. 2017. ReferenceBased Metrics Can Be Replaced with Reference-less Metrics in Evaluating Grammatical Error Correction Systems. In Kondrak, G.; and Watanabe, T., eds., Proceedings of the Eighth International Joint Conference on Natural Language Processing (Volume 2: Short Papers) , 343–348. Taipei, Taiwan: Asian Federation of Natural Language Processing.  

Awasthi, A.; Sarawagi, S.; Goyal, R.; Ghosh, S.; and Piratla, V. 2019. Parallel Iterative Edit Models for Local Sequence Transduction. In Inui, K.; Jiang, J.; Ng, V.; and Wan, X., eds., Proceedings of the 2019 Conference on Empirical Methods in Natural Language Processing and the 9th International Joint Conference on Natural Language Processing (EMNLP-IJCNLP) , 4260–4270. Hong Kong, China: Association for Computational Linguistics.  

Brown, T. B.; Mann, B.; Ryder, N.; Subbiah, M.; Kaplan, J.; Dhariwal, P.; Neelakantan, A.; Shyam, P.; Sastry, G.; Askell, A.; Agarwal, S.; Herbert-Voss, A.; Krueger, G.; Henighan, T.; Child, R.; Ramesh, A.; Ziegler, D. M.; Wu, J.; Winter, C.; Hesse, C.; Chen, M.; Sigler, E.; Litwin, M.; Gray, S.; Chess, B.; Clark, J.; Berner, C.; McCandlish, S.; Radford, A.; Sutskever, I.; and Amodei, D. 2020. Language Models Are Few-Shot Learners. https://arxiv.org/abs/2005.14165v4. Bryant, C.; Felice, M.; and Briscoe, T. 2017. Automatic Annotation and Evaluation of Error Types for Grammatical Error Correction. In Barzilay, R.; and Kan, M.-Y., eds., Proceedings of the 55th Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers) , 793–805. Vancouver, Canada: Association for Computational Linguistics.  

Choshen, L.; and Abend, O. 2018. Inherent Biases in Reference-based Evaluation for Grammatical Error Correction. In Gurevych, I.; and Miyao, Y., eds., Proceedings of the 56th Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers) , 632–642. Melbourne, Australia: Association for Computational Linguistics.  

Chu, K.; Chen, Y.-P.; and Nakayama, H. 2024. A Better LLM Evaluator for Text Generation: The Impact of Prompt Output Sequencing and Optimization. arXiv:2406.09972.  

Coyne, S.; Sakaguchi, K.; Galvan-Sosa, D.; Zock, M.; and Inui, K. 2023. Analyzing the Performance of GPT-3.5 and GPT-4 in Grammatical Error Correction. arXiv:2303.14342. Cronbach, L. J. 1951. Coefficient Alpha and the Internal Structure of Tests. Psychometrika , 16(3): 297–334.  

Dahlmeier, D.; and Ng, H. T. 2012. Better Evaluation for Grammatical Error Correction. In Fosler-Lussier, E.; Riloff, E.; and Bangalore, S., eds., Proceedings of the 2012 Conference of the North American Chapter of the Association for Computational Linguistics: Human Language Technologies ,568–572. Montr´eal, Canada: Association for Computational Linguistics.  

Fang, T.; Liu, X.; Wong, D. F.; Zhan, R.; Ding, L.; Chao, L. S.; Tao, D.; and Zhang, M. 2023a. TransGEC: Improving Grammatical Error Correction with Translationese. In Rogers, A.; Boyd-Graber, J.; and Okazaki, N., eds., Findings of the Association for Computational Linguistics: ACL 2023 , 3614–3633. Toronto, Canada: Association for Computational Linguistics.  

Fang, T.; Yang, S.; Lan, K.; Wong, D. F.; Hu, J.; Chao, L. S.; and Zhang, Y. 2023b. Is ChatGPT a Highly Fluent Grammatical Error Correction System? A Comprehensive Evaluation. arXiv:2304.01746.  

Grundkiewicz, R.; Junczys-Dowmunt, M.; and Gillian, E. 2015. Human Evaluation of Grammatical Error Correction Systems. In M\`arquez, L.; Callison-Burch, C.; and Su, J., eds., Proceedings of the 2015 Conference on Empirical Methods in Natural Language Processing , 461–470. Lisbon, Portugal: Association for Computational Linguistics.  

Kaneko, M.; Mita, M.; Kiyono, S.; Suzuki, J.; and Inui, K. 2020. Encoder-Decoder Models Can Benefit from Pretrained Masked Language Models in Grammatical Error Correction. In Jurafsky, D.; Chai, J.; Schluter, N.; and Tetreault, J., eds., Proceedings of the 58th Annual Meeting of the Association for Computational Linguistics , 4248–4254. Online: Association for Computational Linguistics.  

Kiyono, S.; Suzuki, J.; Mita, M.; Mizumoto, T.; and Inui, K. 2019. An Empirical Study of Incorporating Pseudo Data into Grammatical Error Correction. In Inui, K.; Jiang, J.; Ng, V.; and Wan, X., eds., Proceedings of the 2019 Conference on Empirical Methods in Natural Language Processing and the 9th International Joint Conference on Natural Language Processing (EMNLP-IJCNLP) , 1236–1242. Hong Kong, China: Association for Computational Linguistics.  

Kobayashi, M.; Mita, M.; and Komachi, M. 2024. Revisiting Meta-evaluation for Grammatical Error Correction. arXiv:2403.02674.  

Liu, Y.; Iter, D.; Xu, Y.; Wang, S.; Xu, R.; and Zhu, C. 2023. G-Eval: NLG Evaluation Using GPT-4 with Better Human Alignment. arXiv:2303.16634.  

Napoles, C.; Sakaguchi, K.; Post, M.; and Tetreault, J. 2015. Ground Truth for Grammatical Error Correction Metrics. In Zong, C.; and Strube, M., eds., Proceedings of the 53rd Annual Meeting of the Association for Computational Linguistics and the 7th International Joint Conference on Natural Language Processing (Volume 2: Short Papers) , 588–593. Beijing, China: Association for Computational Linguistics.  

Omelianchuk, K.; Atrasevych, V.; Chernodub, A.; and Skurzhanskyi, O. 2020. GECToR – Grammatical Error Correction: Tag, Not Rewrite. In Burstein, J.; Kochmar, E.; Leacock, C.; Madnani, N.; Pil´an, I.; Yannakoudakis, H.; andZesch, T., eds., Proceedings of the Fifteenth Workshop on Innovative Use of NLP for Building Educational Applications , 163–170. Seattle, WA, USA $\rightarrow$ Online: Association for Computational Linguistics.  

Papineni, K.; Roukos, S.; Ward, T.; and Zhu, W.-J. 2002. Bleu: A Method for Automatic Evaluation of Machine Translation. In Isabelle, P.; Charniak, E.; and Lin, D., eds., Proceedings of the 40th Annual Meeting of the Association for Computational Linguistics , 311–318. Philadelphia, Pennsylvania, USA: Association for Computational Linguistics.  

Rothe, S.; Mallinson, J.; Malmi, E.; Krause, S.; and Severyn,A. 2021. A Simple Recipe for Multilingual Grammatical Error Correction. In Zong, C.; Xia, F.; Li, W.; and Navigli, R., eds., Proceedings of the 59th Annual Meeting of the Association for Computational Linguistics and the 11th International Joint Conference on Natural Language Processing (Volume 2: Short Papers) , 702–707. Online: Association for Computational Linguistics.  

Sellam, T.; Das, D.; and Parikh, A. 2020. BLEURT: Learning Robust Metrics for Text Generation. In Jurafsky, D.; Chai, J.; Schluter, N.; and Tetreault, J., eds., Proceedings of the 58th Annual Meeting of the Association for Computational Linguistics , 7881–7892. Online: Association for Computational Linguistics.  

Sottana, A.; Liang, B.; Zou, K.; and Yuan, Z. 2023. Evaluation Metrics in the Era of GPT-4: Reliably Evaluating Large Language Models on Sequence to Sequence Tasks. arXiv:2310.13800.  

Wei, J.; Wang, X.; Schuurmans, D.; Bosma, M.; Ichter, B.; Xia, F.; Chi, E.; Le, Q.; and Zhou, D. 2023.Chain-of-Thought Prompting Elicits Reasoning in Large Language Models. arXiv:2201.11903.  

Wu, H.; Zhang, S.; Zhang, Y.; and Zhao, H. 2023. Rethinking Masked Language Modeling for Chinese Spelling Correction. arXiv:2305.17721.  

Yoshimura, R.; Kaneko, M.; Kajiwara, T.; and Komachi, M. 2020. SOME: Reference-less Sub-Metrics Optimized for Manual Evaluations of Grammatical Error Correction. In Scott, D.; Bel, N.; and Zong, C., eds., Proceedings of the 28th International Conference on Computational Linguistics , 6516–6522. Barcelona, Spain (Online): International Committee on Computational Linguistics.  

# Reproducibility Checklist  

This paper:  

Includes a conceptual outline and/or pseudocode description of AI methods introduced (yes/partial/no/NA)  

yes  

Clearly delineates statements that are opinions, hypothesis, and speculation from objective facts and results (yes/no) yes  

Provides well marked pedagogical references for lessfamiliare readers to gain background necessary to replicate the paper (yes/no)  

yes  

Does this paper make theoretical contributions? (yes/no)  

If yes, please complete the list below.  

All assumptions and restrictions are stated clearly and formally. (yes/partial/no)  

All novel claims are stated formally (e.g., in theorem statements). (yes/partial/no)  

yes  

Proofs of all novel claims are included. (yes/partial/no)  

yes  

Proof sketches or intuitions are given for complex and/or novel results. (yes/partial/no)  

yes  

Appropriate citations to theoretical tools used are given. (yes/partial/no)  

yes  

All theoretical claims are demonstrated empirically to hold. (yes/partial/no/NA)  

yes  

All experimental code used to eliminate or disprove claims is included. (yes/no/NA)  

NA  

Does this paper rely on one or more datasets? (yes/no)  

yes  

If yes, please complete the list below. A motivation is given for why the experiments are conducted on the selected datasets (yes/partial/no/NA)  

yes  

All novel datasets introduced in this paper are included in a data appendix. (yes/partial/no/NA)  

NA  

All novel datasets introduced in this paper will be made publicly available upon publication of the paper with a license that allows free usage for research purposes. (yes/partial/no/NA)  

yes  

All datasets drawn from the existing literature (potentially including authors’ own previously published work) are accompanied by appropriate citations. (yes/no/NA)  

yes  

All datasets drawn from the existing literature (potentially including authors’ own previously published work) are publicly available. (yes/partial/no/NA)  

yes  

All datasets that are not publicly available are described in detail, with explanation why publicly available alternatives are not scientifically satisficing. (yes/partial/no/NA)  

yes  

Does this paper include computational experiments? (yes/no)  

yes  

If yes, please complete the list below.  

Any code required for pre-processing data is included in the appendix. (yes/partial/no). All source code required for conducting and analyzing the experiments is included in a code appendix. (yes/partial/no)  

no  

All source code required for conducting and analyzing the experiments will be made publicly available upon publication of the paper with a license that allows free usage for research purposes. (yes/partial/no)  

yes  

All source code implementing new methods have comments detailing the implementation, with references to the paper where each step comes from (yes/partial/no)  

yes  

If an algorithm depends on randomness, then the method used for setting seeds is described in a way sufficient to allow replication of results. (yes/partial/no/NA)  

This paper specifies the computing infrastructure used for running experiments (hardware and software), including GPU/CPU models; amount of memory; operating system; names and versions of relevant software libraries and frameworks. (yes/partial/no)  

yes  

This paper formally describes evaluation metrics used and explains the motivation for choosing these metrics. (yes/partial/no)  

This paper states the number of algorithm runs used to compute each reported result. (yes/no)  

yes  

Analysis of experiments goes beyond single-dimensional summaries of performance (e.g., average; median) to include measures of variation, confidence, or other distributional information. (yes/no)  

yes The significance of any improvement or decrease in performance is judged using appropriate statistical tests (e.g., Wilcoxon signed-rank). (yes/partial/no)  

yes  

This paper lists all final (hyper-)parameters used for each model/algorithm in the paper’s experiments. (yes/partial/no/NA)  

This paper states the number and range of values tried per (hyper-) parameter during development of the paper, along with the criterion used for selecting the final parameter setting. (yes/partial/no/NA)  

yes  