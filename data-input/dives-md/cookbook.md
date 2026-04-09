# Cookbook: Google's ML Training and Configuration System

## Key Insight
Cookbook is the system that controls how ranking models learn. Every ML-based ranking signal (NavBoost models, BERT fine-tuning, quality classifiers, spam detectors) has a configuration managed by Cookbook: training data selection, hyperparameters, model architecture, evaluation metrics, and deployment schedules. This means ranking behavior changes not only when Google updates algorithms but also when Cookbook configurations are adjusted, which can happen without any public announcement. A Cookbook configuration change to the NavBoost model's training data window, for example, could shift how user engagement signals are weighted across the entire search pipeline.

### 1 Leak Signal Confirmed (leak)
- Cookbook: ML training/configuration system referenced in the API leak. Manages model parameters and training configurations for ranking models. Connected to Mustang, NavBoost, and Glue systems.
- The leak references suggest Cookbook serves as a centralized configuration management system, similar to how ML platforms like TFX or Kubeflow manage model training pipelines.
- Configuration parameters referenced include training data windows, model architecture selection, hyperparameter tuning ranges, and evaluation metric thresholds.

### 10 Patents (patent)
- ML model parameter management: Patent describing systems for managing and versioning ML model configurations across large-scale search infrastructure. Covers hyperparameter optimization, training data selection, and model deployment scheduling.
- Experiment configuration framework: Patent for A/B testing infrastructure that allows simultaneous deployment of multiple model configurations. Describes how different user populations receive results from different model versions.
- Automated model retraining: Patent covering systems that automatically retrain ranking models when performance metrics degrade or new training data becomes available. Includes drift detection and rollback mechanisms.
- US7836214: Managing configuration of computer systems on a computer network; US7379985: Apparatus and method for managing configuration of computer systems on a computer network; US7398332: Apparatus and method for managing configuration of computer systems on a computer network
- US7487231: Managing configuration of computer systems on a computer network; US7441051: Apparatus and method for managing configuration of computer systems on a computer network; US7171458: Apparatus and method for managing configuration of computer systems on a computer network
- US7231393: Method and apparatus for learning a probabilistic generative model for text; US7743050: Model generation for ranking documents based on large data sets; US11295171: Framework for training machine-learned models on extremely large datasets
- US11256866: Natural language processing with an N-gram machine

### Related DOJ Context (doj)
- No direct Cookbook references in DOJ testimony, but witnesses described the infrastructure it supports:
- Pandu Nayak described Google's ranking system as using machine learning models that are continuously retrained on user interaction data, a process Cookbook would manage.
- HJ Kim testified about NavBoost's 13-month data window and Instant Glue's 24-hour window, configuration parameters that would be defined in Cookbook.
- The DOJ trial revealed that Google runs thousands of search experiments annually, each requiring configuration management that Cookbook provides.

### How ML Configuration Affects Your Rankings (exploit)
- Model retraining explains ranking shifts: Unexplained ranking changes without announced updates may result from Cookbook-driven model retraining. If your rankings shift without a known algorithm update, a model configuration change may have occurred.
- Training data windows matter: Models trained on 13-month windows (NavBoost) vs. 24-hour windows (Instant Glue) respond differently to behavioral changes. Sustained engagement improvements over months have more impact than short-term spikes.
- A/B testing means inconsistent results: Cookbook manages experiment configurations, meaning different users may see different rankings for the same query during testing periods. Rank tracking tools may show volatility during these experiments.
- Seasonal model adjustments: ML models can be retrained with seasonal data, changing how signals are weighted during holidays, events, or trending periods. Content strategies should account for seasonal ranking behavior shifts.
- New model architectures roll out gradually: When Google deploys a new model version (like transitioning from BERT to MUM), Cookbook manages the gradual rollout. Early adopters of the content patterns the new model favors gain a temporary advantage.

### Model Configuration Management (Leak + Patents)
Cookbook manages the full lifecycle of ML ranking models: from training data selection and hyperparameter tuning through evaluation and deployment. Each ranking model (NavBoost, quality classifiers, BERT/MUM, spam detectors) has a Cookbook configuration that defines how it learns and what it optimizes for.
Formula: CookbookConfig(model) = {
  trainingData: { window, sources, filters },
  hyperparams: { lr, batchSize, epochs },
  architecture: { layers, attention, dims },
  evaluation: { metrics, thresholds },
  deployment: { rollout%, schedule }
}
// Each ranking model has its own Cookbook config
// Config changes trigger model retraining
// Versioned for rollback capability
Affects: Affected by: model performance metrics, new training data availability, experiment results, search quality evaluations

### Experiment Configuration (Patents)
Cookbook manages A/B experiment configurations that test new model versions or parameter changes against production baselines. Google runs thousands of experiments annually, each comparing a Cookbook-configured variant against the current production model. Successful experiments are gradually rolled out to all users.
Formula: Experiment(variant) = {
  model: CookbookConfig(variant),
  baseline: CookbookConfig(production),
  traffic: X% of queries,
  metrics: [satisfaction, engagement, accuracy]
}
// Thousands of experiments run annually
if variant > baseline: promote(variant)
// Gradual rollout managed by Cookbook
Affects: Affected by: experiment duration, traffic allocation, metric significance thresholds, rollback conditions

## Pipeline
- **Configuration Definition**: Engineers define model parameters in Cookbook: training data sources, window sizes, hyperparameters, architecture choices, and evaluation metrics.
- **Training Data Preparation**: Cookbook selects and preprocesses training data based on configuration. For NavBoost models, this includes 13 months of click/engagement data. For quality models, human evaluator ratings.
- **Model Training**: ML models trained with Cookbook-specified parameters. Multiple model variants may train simultaneously for A/B comparison.
- **Evaluation**: Trained models evaluated against Cookbook-defined metrics and thresholds. Models that fail evaluation are rejected or flagged for parameter adjustment.
- **Experiment Deployment**: Successful models deployed to a percentage of live traffic as A/B experiments. Performance monitored against production baseline.
- **Production Rollout**: Experiments that show improvement are gradually rolled out to 100% of traffic. Cookbook manages the rollout schedule and maintains rollback capability.

## Timeline
- ~2012: Early ML Infrastructure - Google begins formalizing ML model management for search ranking. Initial systems manage model training for quality classifiers and spam detection.
- ~2015: RankBrain Integration - RankBrain introduces large-scale ML into query understanding. Cookbook-type systems needed to manage model training, versioning, and deployment at search scale.
- ~2019: BERT Model Management - BERT deployment to search requires sophisticated model configuration management. Multiple BERT variants trained and tested for different query types.
- 2024: API Leak: Cookbook Confirmed - Cookbook referenced in API leak as ML training/configuration system. Connected to Mustang, NavBoost, Glue, SnippetBrain, and SuperRoot systems.
