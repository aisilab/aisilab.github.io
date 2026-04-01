---
layout: page
title: Research
permalink: /research/
---

We make advanced AI systems safer and more transparent by developing methods to
understand model behavior and internals, AI agent interaction, and collective dynamics in agent populations.

To this end, we build interpretability methods that reveal how AI systems work internally, and leverage these insights to ensure safe behavior when AI agents interact with the world and each other. Our work is organized around four themes:

**Model Behavior**. How do AI systems behave, and what do they know? We probe and evaluate model capabilities through benchmarks, behavioral experiments, and analysis of emergent structure in learned representations.

**Model Internals**. How do models represent what they know? We develop mechanistic interpretability methods to localize, isolate, and edit internal representations — from culture-specific neurons to tokenization effects on encoded knowledge.

**Agentic & Multi-Agent Safety**. What happens when AI agents interact with the world and each other? We investigate guardrails, steering, cooperation, and failure modes in multi-agent systems.

**Efficient & Sustainable AI**. AI safety research requires models we can actually inspect, and the planet requires that we train them responsibly. We develop methods to reduce the computational and environmental cost of AI systems, democratizing access to models that can be opened up for transparency and safety research.

A selection of our recent publications is listed below, organized by theme.

## Model behavior

{% include publication.html
    title="Learning and communication pressures in neural networks: Lessons from emergent communication"
    authors="Lukas Galke, Limor Raviv"
    venue="Language Development Research 5(1)"
    year="2025"
    paper_url="https://doi.org/10.34842/3vr5-5r49"
    preprint_url="https://arxiv.org/abs/2403.14427"
%}

{% include publication.html
    title="Deep neural networks and humans both benefit from compositional language structure"
    authors="Lukas Galke, Yoav Ram, Limor Raviv"
    venue="Nature Communications 15:10816"
    year="2024"
    paper_url="https://rdcu.be/d5f2e"
    code_url="https://github.com/lgalke/easy2deeplearn"
    data_url="https://doi.org/10.5281/zenodo.14205452"
%}

{% include publication.html
    title="Morphology Matters: Probing the Cross-linguistic Morphological Generalization Abilities of Large Language Models through a Wug Test"
    authors="Anh Dang, Limor Raviv, Lukas Galke"
    venue="Cognitive Modeling and Computational Linguistics Workshop at ACL 2024"
    paper_url="https://aclanthology.org/2024.cmcl-1.15/"
%}

## Model internals

{% include publication.html
    title="Isolating Culture Neurons in Multilingual Large Language Models"
    authors="Danial Namazifard, Lukas Galke Poech"
    venue="IJCNLP-AACL Findings"
    abstract="Language and culture are deeply intertwined, yet it is so far unclear how and where multilingual large language models encode culture. Here, we extend upon an established methodology for identifying language-specific neurons and extend it to localize and isolate culture-specific neurons, carefully disentangling their overlap and interaction with language-specific neurons. To facilitate our experiments, we introduce MUREL, a curated dataset of 85.2 million tokens spanning six different cultures. Our localization and intervention experiments show that LLMs encode different cultures in distinct neuron populations, predominantly in upper layers, and that these culture neurons can be modulated independently from language-specific neurons or those specific to other cultures. These findings suggest that cultural knowledge and propensities in multilingual language models can be selectively isolated and edited - promoting fairness, inclusivity, and alignment."
    code_url="https://github.com/namazifard/Culture_Neurons"
    year="2025"
    paper_url="https://aclanthology.org/2025.findings-ijcnlp.45/"
    preprint_url="https://arxiv.org/abs/2508.02241"
%}

{% include publication.html
    title="Tokenization and Morphology in Multilingual Language Models: A Comparative Analysis of mT5 and ByT5"
    authors="Thao Anh Dang, Limor Raviv, Lukas Galke"
    venue="ICNLSP"
    year="2025"
    abstract="Morphology is a crucial factor for multilingual language modeling as it poses direct challenges for tokenization. Here, we seek to understand how tokenization influences the morphological knowledge encoded in multilingual language models. Specifically, we capture the impact of tokenization by contrasting a minimal pair of multilingual language models: mT5 and ByT5. The two models share the same architecture, training objective, and training data and only differ in their tokenization strategies: subword tokenization vs. character-level tokenization. Probing the morphological knowledge encoded in these models on four tasks and 17 languages, our analyses show that the models learn the morphological systems of some languages better than others and that morphological information is encoded in the middle and late layers. Finally, we show that languages with more irregularities benefit more from having a higher share of the pre-training data."
    paper_url="https://aclanthology.org/2025.icnlsp-1.24/"
%}

## Agentic & Multi-Agent Safety

{% include publication.html
    title="Guarded Query Routing for Large Language Models"
    authors="Richard Šléher, William Brach, Tibor Sloboda, Kristián Košťál, and Lukas Galke"
    venue="ECAI"
    year="2025"
    preprint_url="https://arxiv.org/abs/2505.14524"
    project_url="https://gqr-bench.github.io"
    code_url="https://github.com/williambrach/gqr"
%}

{% include publication.html
    title="Super-additive Cooperation in Language Model Agents"
    authors="Filippo Tonini, Lukas Galke"
    venue="3rd International Conference on Frontiers of Artificial Intelligence, Ethics, and Multidisciplinary Applications (FAIEMA)"
    year="2025"
    preprint_url="https://arxiv.org/abs/2508.15510"
    project_url="https://github.com/pippot/Superadditive-cooperation-LLMs"
%}

## Efficient & Sustainable AI

{% include publication.html
    title="DeToNATION: Decoupled Torch Network-Aware Training on Interlinked Online Nodes"
    authors="Mogens From, Jacob Nielsen, Lukas Galke, and Peter Schneider-Kamp"
    venue="AAAI"
    year="2026"
    preprint_url="https://arxiv.org/abs/2502.06728"
%}

{% include publication.html
    title="Continual Quantization-Aware Pre-Training: When to transition from 16-bit to 1.58-bit pre-training for BitNet language models?"
    authors="Jacob Nielsen, Peter Schneider-Kamp, and Lukas Galke"
    venue="ACL Findings"
    year="2025"
    abstract="Large language models (LLMs) require immense resources for training and inference. Quantization, a technique that reduces the precision of model parameters, offers a promising solution for improving LLM efficiency and sustainability. While post-training quantization methods typically achieve 4-8 bits per parameter, recent research suggests that training LLMs with 1.58 bits per weight parameter from scratch can maintain model accuracy while greatly reducing memory requirements and energy consumption at inference time. Here, we investigate a training strategy for quantization-aware pre-training, where the models are first trained with 16-bit precision and then transition into 1.58-bit quantization-aware training. Our results on 11 downstream tasks, show that this 16-to-1.58-bit training strategy is preferable over full 1.58-bit training and leaves models closer to those which have undergone 16-bit training. We further investigate the effects of retaining the optimizer state at the transition point and gradually phasing in quantization strength - finding that both techniques alleviate the magnitude of loss spikes, but also that these effects can be compensated through further training."
    paper_url="https://aclanthology.org/2025.findings-acl.694/"
%}

{% include publication.html
    title="When are 1.58 bits enough? A Bottom-up Exploration of Quantization-aware Training with Ternary Weights"
    authors="Jacob Nielsen, Lukas Galke, and Peter Schneider-Kamp"
    venue="18th International Conference on Agents and Artificial Intelligence (ICAART)"
    year="2025"
    abstract="Contemporary machine learning models, such as language models, are powerful, but come with immense resource requirements both at training and inference time. It has been shown that decoder-only language models can be trained to a competitive state with ternary weights (1.58 bits per weight), facilitating efficient inference. Here, we start our exploration with non-transformer model architectures, investigating 1.58-bit training for multi-layer perceptrons and graph neural networks. Then, we explore 1.58-bit training in other transformer-based language models, namely encoder-only and encoder-decoder models. Our results show that in all of these settings, 1.58-bit training is on par with or sometimes even better than the standard 32/16-bit models."
    preprint_url="https://arxiv.org/abs/2411.05882"
%}
