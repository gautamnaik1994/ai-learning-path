# AI/ML Glossary

A quick reference for common terminology across the AI/ML learning path.

---

## Core Concepts

**Activation Function** - Non-linear function applied to neuron outputs (e.g., ReLU, sigmoid, tanh). Enables neural networks to learn non-linear patterns.

**Backpropagation** - Algorithm to compute gradients for training neural networks. Propagates error backwards through layers to update weights.

**Batch** - Subset of training data processed together. Helps with computational efficiency and gradient stability.

**Bias** - Offset parameter in neural networks. Allows models to fit data not passing through origin.

**Classifier** - Model that predicts discrete categories (e.g., spam/not spam, cat/dog/bird).

**Clustering** - Unsupervised learning task of grouping similar data points together.

**Cross-Validation** - Technique to evaluate model on different data splits to assess generalization.

**Dataset** - Collection of labeled or unlabeled examples used for training/testing.

**Deep Learning** - Machine learning using neural networks with multiple hidden layers.

**Dimensionality Reduction** - Technique to reduce number of features while preserving information (e.g., PCA).

**Embedding** - Dense vector representation of data that captures semantic meaning (e.g., word embeddings).

**Epoch** - One complete pass through entire training dataset.

**Evaluation Metrics** - Measures of model performance (e.g., accuracy, precision, recall, F1-score).

**Feature** - Input variable used by model. Also called "attribute" or "predictor".

**Feature Engineering** - Process of creating new features from raw data to improve model performance.

**Fine-tuning** - Transfer learning technique where pre-trained model is further trained on new task.

**Generalization** - Model's ability to perform well on unseen data.

**Gradient** - Vector of partial derivatives; points in direction of steepest increase.

**Gradient Descent** - Optimization algorithm that iteratively updates parameters to minimize loss.

**Ground Truth** - Actual correct labels in dataset.

**Hyperparameter** - Parameter set before training (e.g., learning rate, batch size). Not learned from data.

**Inference** - Using trained model to make predictions on new data.

**Label** - Target output variable we're trying to predict.

**Learning Rate** - Hyperparameter controlling step size in gradient descent.

**Loss Function** - Function measuring prediction error. We minimize this during training.

**Machine Learning** - Creating models that learn patterns from data without explicit programming.

**Model** - Mathematical representation learned from data that makes predictions.

**Neural Network** - Computational model inspired by biological neurons, composed of interconnected nodes.

**Optimization** - Process of finding best model parameters by minimizing loss.

**Overfitting** - When model memorizes training data but fails on new data.

**Parameter** - Learnable weight or coefficient in model. Updated during training.

**Preprocessing** - Data cleaning and transformation before training (e.g., normalization, encoding).

**Regression** - Supervised learning task of predicting continuous numerical values.

**Regularization** - Technique to prevent overfitting (e.g., L1, L2, dropout).

**Residual** - Difference between predicted and actual value.

**Supervised Learning** - Learning from labeled examples (input-output pairs).

**Test Set** - Data used to evaluate final model performance. Must be unseen during training.

**Training** - Process of adjusting model parameters to minimize loss on training data.

**Training Set** - Data used to train model.

**Transfer Learning** - Using knowledge from one task to improve learning on another task.

**Underfitting** - When model is too simple and fails to capture patterns in data.

**Unsupervised Learning** - Learning from unlabeled data to find patterns or structure.

**Validation Set** - Data used during training to tune hyperparameters and monitor generalization.

**Weight** - Parameter in neural network connecting neurons. Learned during training.

---

## Deep Learning Specific

**CNN (Convolutional Neural Network)** - Neural network designed for image data using convolutional layers.

**Convolution** - Operation that slides a filter over input to extract local features.

**Dropout** - Regularization technique randomly disabling neurons during training to prevent overfitting.

**LSTM (Long Short-Term Memory)** - Recurrent neural network variant that can learn long-term dependencies.

**Pooling** - Operation that downsamples feature maps by taking max or average of local regions.

**RNN (Recurrent Neural Network)** - Neural network with connections between time steps, good for sequences.

**Transformer** - Architecture using self-attention mechanism, foundation for modern language models.

**Attention Mechanism** - Technique allowing model to focus on relevant parts of input.

**Self-Attention** - Attention mechanism where sequence attends to itself.

---

## Generative AI Specific

**Agent** - System that can perceive environment, decide actions, and execute them to achieve goals.

**Chain-of-Thought** - Prompting technique where model explains reasoning step-by-step.

**Context Window** - Maximum number of tokens a model can process (e.g., GPT-4 has 8K or 32K tokens).

**Fine-tuning** - Further training of pre-trained LLM on specific task or domain data.

**Hallucination** - When LLM generates plausible but false information.

**In-Context Learning** - Learning from examples provided in prompt without fine-tuning.

**LLM (Large Language Model)** - Pre-trained neural network with billions of parameters, trained on large text corpora.

**Prompt Engineering** - Art of designing prompts to guide LLM toward desired outputs.

**RAG (Retrieval-Augmented Generation)** - Technique combining retrieval system with LLM to provide grounded answers.

**Token** - Basic unit text is split into (roughly 4 characters per token in English).

**Temperature** - Hyperparameter controlling randomness of LLM outputs (0 = deterministic, 1+ = more random).

**Vector Database** - Database storing dense embeddings for semantic search and retrieval.

**Embedding Model** - Model converting text/images to dense vectors capturing semantic meaning.

**MCP (Model Context Protocol)** - Protocol enabling AI models and tools to communicate standardized interfaces.

---

## Data Science Terms

**Confusion Matrix** - Table showing true positives, true negatives, false positives, false negatives.

**Precision** - Of predicted positives, how many were correct? (TP / (TP + FP))

**Recall** - Of actual positives, how many did we find? (TP / (TP + FN))

**F1-Score** - Harmonic mean of precision and recall; balanced metric for imbalanced datasets.

**ROC Curve** - Plot of true positive rate vs. false positive rate at different classification thresholds.

**AUC (Area Under Curve)** - Area under ROC curve; probability model ranks random positive higher than random negative.

**Accuracy** - Proportion of correct predictions out of all predictions.

**MAE (Mean Absolute Error)** - Average absolute difference between predictions and actual values.

**RMSE (Root Mean Squared Error)** - Square root of average squared errors; penalizes large errors more.

**Baseline** - Simple model used as reference for comparing more complex models.

**Confusion** - Model incorrectly predicting one class as another.

---

## Optimization & Training

**Batch Gradient Descent** - Updates parameters using gradient computed on entire dataset.

**Stochastic Gradient Descent (SGD)** - Updates parameters using gradient computed on single example.

**Mini-Batch Gradient Descent** - Updates parameters using gradient computed on small batch.

**Adam** - Adaptive optimization algorithm combining momentum and adaptive learning rates.

**Momentum** - Optimization technique accumulating gradients over time for faster convergence.

**Learning Rate Decay** - Gradually reducing learning rate during training for convergence.

**Convergence** - Point where loss stops improving significantly, training can stop.

**Vanishing Gradient** - When gradients become too small during backpropagation, slowing learning.

**Exploding Gradient** - When gradients become too large, destabilizing training.

---

## Quick Reference by Category

### Unsure about performance?

See: Evaluation Metrics, Confusion Matrix, Precision, Recall, F1-Score, ROC Curve, Baseline

### Training not working?

See: Learning Rate, Vanishing Gradient, Exploding Gradient, Convergence, Overfitting, Underfitting, Regularization

### Working with sequences/text?

See: RNN, LSTM, Transformer, Token, Context Window, Embedding

### Building with LLMs?

See: LLM, Prompt Engineering, In-Context Learning, RAG, Temperature, Hallucination, MCP

### Need to reduce data dimensions?

See: Dimensionality Reduction, PCA, Embedding, Feature Engineering
