# Deep Learning

Deep learning uses neural networks with multiple layers to learn hierarchical representations of data. This section builds your understanding from foundational concepts through advanced applications.

## What You'll Learn

By the end of this section, you'll be able to:

- Build and train neural networks from scratch
- Understand architectures for different domains (images, sequences, text)
- Apply transfer learning for faster training
- Debug and optimize deep learning models

## Pre-requisites

Before diving into deep learning, ensure you have:

- **Machine Learning fundamentals** - Understanding of supervised learning, model evaluation
- **[Math Essentials](../foundations/math-essentials.md)** - Linear algebra, calculus, probability
- **Deep Learning Framework** - Either **PyTorch** or **TensorFlow**
    - **PyTorch** (recommended): Better for research, easier debugging, excellent tutorials
    - **TensorFlow**: Better for production, more deployment options
- **Google Colab** or **Jupyter Notebooks** for hands-on coding

!!! note
    If you're new to both frameworks, start with **PyTorch**. It's more intuitive and widely used in research.

---

## Beginner Level: Neural Network Fundamentals

### Learning Outcomes

After this section, you'll understand:

- How neurons, weights, and biases form neural networks
- How data flows through networks (forward pass)
- How networks learn (backpropagation)
- Common activation functions and when to use them

### Core Concepts

#### Neurons and Layers

A neuron is the basic computational unit:

```
output = activation(dot(weights, input) + bias)
```

Multiple neurons form a **layer**. Multiple layers form a **neural network**.

#### Activation Functions

Activation functions add **non-linearity**, allowing networks to learn complex patterns.

| Function | Use Case | Properties |
|----------|----------|-----------|
| **ReLU** (Rectified Linear Unit) | Hidden layers (default choice) | Non-negative, fast, prevents vanishing gradients |
| **Sigmoid** | Binary classification output | Outputs 0-1, smooth gradient |
| **Tanh** | Hidden layers, RNNs | Outputs -1 to 1, centered around 0 |
| **Softmax** | Multi-class classification | Outputs probability distribution |
| **Linear** | Regression output | No transformation (y = x) |

#### Loss Functions

Loss functions measure how wrong predictions are. We minimize loss during training.

| Function | Use Case |
|----------|----------|
| **Mean Squared Error (MSE)** | Regression |
| **Cross-Entropy** | Classification (binary or multi-class) |
| **Binary Cross-Entropy** | Binary classification |

#### Optimization: Gradient Descent

Gradient descent iteratively updates weights to minimize loss:

1. Compute gradients (how much each weight contributes to error)
2. Move weights in opposite direction of gradient
3. Repeat until convergence

Learning rate controls step size. Too high = unstable; too low = slow training.

#### Backpropagation

Algorithm that efficiently computes gradients for all layers using the chain rule. Modern frameworks (PyTorch, TensorFlow) handle this automatically.

#### Overfitting and Regularization

**Overfitting** happens when models memorize training data instead of learning generalizable patterns.

**Solutions:**

- **Regularization (L1/L2)**: Add penalty for large weights
- **Dropout**: Randomly disable neurons during training to prevent co-adaptation
- **Data augmentation**: Artificially increase dataset variety
- **Early stopping**: Stop training when validation loss increases

### Hands-on: Build Your First Neural Network

**Project: MNIST Digit Classification**

Using PyTorch, build a simple feedforward network to classify handwritten digits:

1. Load MNIST dataset
2. Define a network with 2-3 hidden layers
3. Train for 10 epochs, monitor loss
4. Evaluate on test set (aim for >95% accuracy)

**Resources:**

- [PyTorch Getting Started](https://pytorch.org/get-started/locally/)
- [Official PyTorch MNIST Tutorial](https://github.com/pytorch/examples/blob/main/mnist/main.py)
- [FastAI Practical Deep Learning](https://course.fast.ai/) (Excellent, free, beginner-friendly)

---

## Intermediate Level: Deep Architectures

### Learning Outcomes

After this section, you'll understand:

- How CNNs extract spatial features from images
- How RNNs process sequential data
- How transformers use attention mechanisms
- When to use each architecture

### Convolutional Neural Networks (CNNs)

CNNs are specialized for **image data**. They use convolutions to extract local spatial patterns.

**Key components:**

- **Convolution**: Slide filters over image to detect patterns (edges, corners, textures)
- **Pooling**: Downsample while preserving important features
- **Fully connected layers**: Make final predictions

**Common architectures:**

- **LeNet**: Original CNN for digit recognition (small, simple)
- **AlexNet**: Breakthrough model for ImageNet classification (2012)
- **VGG**: Simple stacked architecture, easy to understand
- **ResNet**: Skip connections prevent vanishing gradients (deeper networks)
- **MobileNet**: Lightweight, for mobile/edge devices
- **EfficientNet**: Better accuracy-efficiency trade-off

**When to use:** Image classification, object detection, image segmentation

### Recurrent Neural Networks (RNNs)

RNNs are specialized for **sequential data** (text, time series, audio). They process one element at a time while maintaining hidden state.

**Key idea:** Output depends on current input AND previous hidden state.

**Common architectures:**

- **Vanilla RNN**: Simple but suffers from vanishing gradients
- **LSTM (Long Short-Term Memory)**: Gates control information flow, handles long-term dependencies
- **GRU (Gated Recurrent Unit)**: Simpler than LSTM, similar performance
- **Bidirectional RNN**: Processes sequence in both directions

**When to use:** Text classification, language modeling, time series forecasting, machine translation

### Transformers and Attention

Transformers use **self-attention** to process sequences in parallel (vs. RNNs' sequential processing).

**Key advantages:**

- Faster training (parallel processing)
- Better long-range dependencies
- Foundation for modern LLMs (BERT, GPT, etc.)

**Architecture:**

- **Encoder**: Processes input sequence (used in BERT)
- **Decoder**: Generates output sequence (used in GPT)
- **Encoder-Decoder**: Sequence-to-sequence tasks (translation, summarization)

**Attention intuition:** "Which parts of input are most relevant to this task?"

### Training Practices for Deep Networks

**Data Splitting:**

- Training set: 70-80% of data
- Validation set: 10-15% (tune hyperparameters during training)
- Test set: 10-15% (final evaluation, untouched during training)

**Optimization Techniques:**

- **SGD with momentum**: Smoother convergence
- **Adam**: Adaptive learning rates, often default choice
- **Learning rate scheduling**: Decrease learning rate over time
- **Batch normalization**: Normalize layer inputs, faster training, better generalization

**Monitoring Training:**

- Plot loss (should decrease over time)
- Plot validation accuracy (should improve but may plateau)
- Stop training if validation loss increases (overfitting)

**Early Stopping:**

```
if validation_loss hasn't improved for N epochs:
    stop training
    load best model from checkpoint
```

### Transfer Learning

Pre-trained models learned on large datasets (ImageNet, Common Crawl) capture useful features. Reuse them!

**Two approaches:**

1. **Fine-tuning:** Train all layers on new data (small learning rate to avoid forgetting)
2. **Feature extraction:** Freeze pre-trained layers, only train new head

**Benefits:**

- Faster training (start with learned features)
- Better performance (especially with limited data)
- Reduces computational cost

### Intermediate Projects

**Project 1: Image Classification**

- Use CNN on CIFAR-10 or custom dataset
- Aim for >85% accuracy
- Try transfer learning with ResNet18

**Project 2: Text Classification**

- Build LSTM for sentiment analysis on IMDB or custom reviews
- Aim for >85% accuracy
- Compare with simpler baseline (Naive Bayes)

**Project 3: Time Series Forecasting**

- Predict stock prices, weather, or electricity demand using LSTM
- Evaluate on future data (temporal split, not random)

**Resources:**

- [FastAI Part 1: Practical Deep Learning](https://course.fast.ai/) (Highly recommended)
- [PyTorch tutorials](https://pytorch.org/tutorials/)
- [TensorFlow tutorials](https://www.tensorflow.org/tutorials)
- [Papers with Code](https://paperswithcode.com/) (State-of-art implementations with code)

---

## Advanced Level: Deep Dive and Specialization

### Learning Outcomes

After this section, you'll understand:

- Advanced training techniques and optimization
- Model compression and deployment
- Specialized architectures (Vision Transformers, etc.)
- Debugging and improving model performance

### Advanced Topics

#### Model Optimization

**Quantization:** Reduce precision (float32 → int8) for 4-8x memory/speed improvements with minimal accuracy loss.

**Pruning:** Remove unnecessary weights to reduce model size.

**Knowledge Distillation:** Train small model to mimic large model (small model gets large model's knowledge).

**Mixed Precision Training:** Use float16 for some operations, float32 for others (faster, same accuracy).

#### Distributed Training

Training very large models across multiple GPUs or machines.

- **Data Parallelism**: Split batch across GPUs (easy)
- **Model Parallelism**: Split model across GPUs (complex)
- **Distributed frameworks**: PyTorch DistributedDataParallel, TensorFlow distributed training

#### Advanced Architectures

**Vision Transformers (ViT):** Apply transformers to images, often better than CNNs.

**Diffusion Models:** Generate images/text by iteratively denoising random noise.

**Graph Neural Networks (GNNs):** Learn on graph-structured data (molecules, social networks).

**Multimodal Models:** Learn from images + text simultaneously (CLIP, GPT-4V).

#### Hyperparameter Tuning

Systematic approaches to finding best settings:

- **Grid Search**: Try all combinations (slow)
- **Random Search**: Sample random combinations (faster)
- **Bayesian Optimization**: Use past results to guide search (smart)
- **Hyperband**: Efficient resource allocation

#### Debugging Deep Learning

Common issues and solutions:

| Problem | Likely Cause | Solution |
|---------|---|---|
| Training loss doesn't decrease | Learning rate too high | Reduce learning rate |
| Loss becomes NaN | Numerical instability | Reduce learning rate, check for bad data |
| Slow training | Small batch size or no GPU | Increase batch size, use GPU |
| Overfitting | Model too large or insufficient regularization | Add dropout, regularization, more data |
| High validation loss | Model underfitting | Increase model capacity or training time |

#### Model Interpretability

Understanding what networks learn:

- **Visualize filters:** What patterns do early layers detect?
- **Attention maps:** Which input parts does model focus on?
- **Saliency maps:** Which pixels are most important for prediction?
- **Tools:** TorchVision, Captum, Grad-CAM

### Advanced Projects

**Project 1: Custom Domain Application**

- Apply deep learning to your domain (medical imaging, satellite imagery, audio, etc.)
- Experiment with transfer learning and fine-tuning
- Deploy model as API

**Project 2: Multi-task Learning**

- Train single model on multiple tasks (classification + localization)
- More efficient than separate models

**Project 3: Generative Model**

- Implement VAE or GAN to generate new images
- Evaluate generation quality

**Project 4: Production Deployment**

- Convert model to ONNX or TFLite format
- Deploy as web service or mobile app
- Monitor inference performance

### Resources

**Books:**

- [Deep Learning](https://www.deeplearningbook.org/) by Goodfellow, Bengio, Courville (comprehensive, theory-heavy)
- [Practical Deep Learning for Coders](https://book.fast.ai/) by Jeremy Howard (practical, code-first)

**Online Courses:**

- [Stanford CS231N: CNN for Visual Recognition](https://cs231n.github.io/) (Excellent, free)
- [Stanford CS224N: NLP with Deep Learning](https://web.stanford.edu/class/cs224n/) (Best for NLP)
- [Andrew Ng Deep Learning Specialization](https://www.deeplearning.ai/programs/deep-learning-specialization/) (Comprehensive)

**Communities & Benchmarks:**

- [Papers with Code](https://paperswithcode.com/) - Latest research with implementations
- [Hugging Face Model Hub](https://huggingface.co/models) - Pre-trained models
- [Kaggle Competitions](https://www.kaggle.com/competitions) - Benchmark datasets

---

## Framework Choice: PyTorch vs. TensorFlow

| Aspect | PyTorch | TensorFlow |
|--------|---------|-----------|
| **Learning Curve** | Easier, Pythonic | Steeper, more verbose |
| **Debugging** | Better (eager execution) | Harder (graph mode) |
| **Production** | Improving (TorchServe) | Excellent (TensorFlow Serving) |
| **Community** | Research-focused | Industry-focused |
| **Documentation** | Very good | Good |
| **Performance** | Comparable | Comparable |

**Recommendation:** Start with **PyTorch** for learning, evaluate **TensorFlow** for production.

---

## Common Mistakes to Avoid

1. **Using test data during training** → Data leakage, inflated performance
2. **Not scaling features** → Poor convergence
3. **Training too long** → Overfitting; use early stopping
4. **Poor batch size choice** → Too small = slow; too large = poor convergence
5. **Not monitoring validation loss** → Can't detect overfitting early
6. **Forgetting to normalize images** → Unstable training
7. **Using wrong loss function** → Wrong optimization direction
8. **Not saving checkpoints** → Lose best model if training interrupted

---

## Next Steps

1. **Start with Beginner level** if you're new to deep learning
2. **Practice on MNIST, CIFAR-10** before custom datasets
3. **Move to Intermediate** once basic networks work
4. **Specialize** based on your interests:
   - **Computer Vision** → Focus on CNNs and Vision Transformers
   - **NLP** → Focus on Transformers and LLMs
   - **Time Series** → Focus on RNNs and LSTMs
5. **Check [Common Issues & Solutions](../common-issues.md)** if stuck

---

## Learning Resources

- [PyTorch Official Tutorials](https://pytorch.org/tutorials/)
- [TensorFlow Documentation](https://www.tensorflow.org/)
- [StatQuest with Josh Starmer](https://www.youtube.com/@statquest) - Math fundamentals
- [Krish Naik](https://www.youtube.com/@krishnaik06) - Practical deep learning
- [3Blue1Brown Neural Networks Series](https://www.youtube.com/playlist?list=PLZHQObOWTQDNU6R1_67000Dx_LFCT9B52) - Beautiful intuition

---

## Math and Statistics

Once you are comfortable with the basics of deep learning and its libraries, understanding the mathematical foundations will help you:

- Debug training issues (vanishing/exploding gradients)
- Tune hyperparameters effectively
- Design custom layers and loss functions

Refer to [Math Essentials](../foundations/math-essentials.md) for:

- Linear algebra review
- Calculus for gradients
- Probability and distributions

Additional resources:

- [StatQuest with Josh Starmer](https://www.youtube.com/@statquest) - Mathematical concepts explained simply
- [Krish Naik](https://www.youtube.com/@krishnaik06) - Practical deep learning explanations
