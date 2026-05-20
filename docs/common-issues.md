# Common Issues & Solutions

This page addresses common problems, errors, and conceptual confusions that learners encounter across the AI/ML learning path. Check here if you're stuck!

---

## Environment & Setup Issues

### Issue: "ModuleNotFoundError: No module named 'X'"

**Problem:** You're trying to import a library that isn't installed.

**Solutions:**

1. **Check if package is installed:**

   ```bash
   pip list | grep package_name
   ```

2. **Install the missing package:**

   ```bash
   pip install package_name
   ```

3. **For common ML libraries:**

   ```bash
   pip install numpy pandas scikit-learn matplotlib
   pip install torch torchvision  # PyTorch
   pip install tensorflow keras   # TensorFlow
   pip install langchain huggingface-hub  # Gen AI
   ```

4. **Restart your Jupyter kernel** after installing:
   - In Jupyter: Kernel → Restart
   - In VS Code: Restart Kernel button

5. **Check Python environment:**
   - Ensure you're using the right Python environment (especially with virtual envs/conda)
   - In Jupyter: Check kernel name (top-right corner)

---

### Issue: "Permission denied" or "No module named pip"

**Problem:** pip isn't accessible or you lack permissions.

**Solutions:**

1. **Use explicit Python module:**

   ```bash
   python3 -m pip install package_name
   ```

2. **For permission issues (macOS/Linux):**

   ```bash
   pip install --user package_name
   ```

3. **Use a virtual environment** (recommended):

   ```bash
   python3 -m venv env
   source env/bin/activate  # macOS/Linux
   env\Scripts\activate     # Windows
   pip install package_name
   ```

---

### Issue: GPU not being used (PyTorch/TensorFlow)

**Problem:** Training is slow; you expected GPU acceleration.

**Solutions:**

1. **Check if GPU is available:**

   ```python
   import torch
   print(torch.cuda.is_available())      # PyTorch
   print(torch.cuda.get_device_name(0))  # Device name
   ```

2. **For TensorFlow:**

   ```python
   import tensorflow as tf
   print(tf.config.list_physical_devices('GPU'))
   ```

3. **Install GPU drivers:**
   - [NVIDIA CUDA Toolkit](https://developer.nvidia.com/cuda-downloads)
   - [cuDNN library](https://developer.nvidia.com/cudnn)

4. **Use Google Colab** (easier):
   - Go to Runtime → Change runtime type → GPU
   - Free tier provides access to GPUs

---

### Issue: "Port already in use" or Jupyter won't start

**Problem:** Can't launch Jupyter notebook.

**Solutions:**

1. **Kill process using the port:**

   ```bash
   # Find process using port 8888
   lsof -i :8888  # macOS/Linux
   # Kill the process
   kill -9 <PID>
   ```

2. **Use a different port:**

   ```bash
   jupyter notebook --port 8889
   ```

3. **Use VS Code** instead:
   - Easier, no port conflicts
   - Built-in cell execution

---

## Machine Learning Issues

### Issue: Model accuracy is very low (< 50% for classification)

**Diagnosis & Solutions:**

1. **Check data quality:**

   ```python
   # Look at your data
   df.head()
   df.isnull().sum()  # Missing values?
   df.describe()      # Value ranges make sense?
   ```

2. **Check train-test split:**
   - Are train and test distributions similar?
   - Is test data properly separated?

3. **Simple baseline first:**
   - Start with simple model (logistic regression, decision tree)
   - If baseline fails, problem is likely data quality, not model complexity

4. **Check feature scaling:**

   ```python
   from sklearn.preprocessing import StandardScaler
   scaler = StandardScaler()
   X_train = scaler.fit_transform(X_train)
   ```

5. **Wrong preprocessing:**
   - Fit scaler on training data only
   - Apply same scaler to test data

   ```python
   # CORRECT
   scaler = StandardScaler().fit(X_train)
   X_train_scaled = scaler.transform(X_train)
   X_test_scaled = scaler.transform(X_test)  # Use fitted scaler!
   ```

---

### Issue: Model overfits (high train accuracy, low test accuracy)

**Problem:** Model memorizes training data but doesn't generalize.

**Solutions:**

1. **Get more data:**
   - Overfitting common with small datasets

2. **Add regularization:**

   ```python
   # Scikit-learn
   LogisticRegression(C=1.0, penalty='l2')  # L2 regularization
   
   # Reduce model complexity
   DecisionTreeClassifier(max_depth=5)  # Limit tree depth
   ```

3. **Use cross-validation:**

   ```python
   from sklearn.model_selection import cross_val_score
   scores = cross_val_score(model, X, y, cv=5)
   print(f"Mean: {scores.mean()}, Std: {scores.std()}")
   ```

4. **Early stopping** (neural networks):
   - Stop training when validation loss stops improving

5. **Data augmentation:**
   - Artificially increase dataset size (rotate images, paraphrase text)

---

### Issue: "ConvergenceWarning" or model won't train

**Problem:** Optimization algorithm couldn't converge.

**Solutions:**

1. **Increase max iterations:**

   ```python
   LogisticRegression(max_iter=1000)  # Default is 100
   ```

2. **Reduce learning rate or scale features:**

   ```python
   # Scale features first!
   from sklearn.preprocessing import StandardScaler
   X = StandardScaler().fit_transform(X)
   ```

3. **Check for NaN/infinite values:**

   ```python
   import numpy as np
   print(np.isnan(X).sum())
   print(np.isinf(X).sum())
   ```

---

### Issue: "Remember to fit before transform"

**Problem:** Applying transformation that hasn't been fitted on training data.

**Correct pattern:**

```python
# CORRECT
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
scaler.fit(X_train)      # Learn from training data
X_train = scaler.transform(X_train)
X_test = scaler.transform(X_test)  # Apply learned transformation

# WRONG - Don't fit on combined data!
scaler.fit(np.concatenate([X_train, X_test]))  # Data leakage!
```

---

## Deep Learning Issues

### Issue: "CUDA out of memory" or "RuntimeError: CUDA out of memory"

**Problem:** Model/batch too large for GPU memory.

**Solutions:**

1. **Reduce batch size:**

   ```python
   batch_size = 32  # Reduce from 128 or 256
   dataloader = DataLoader(dataset, batch_size=batch_size)
   ```

2. **Use gradient accumulation:**

   ```python
   # Simulate larger batch without more memory
   for i, (x, y) in enumerate(dataloader):
       loss = model(x, y)
       loss.backward()
       if (i + 1) % 4 == 0:  # Accumulate 4 batches
           optimizer.step()
   ```

3. **Use mixed precision training:**

   ```python
   from torch.cuda.amp import autocast
   with autocast():
       outputs = model(inputs)
   ```

4. **Use CPU** (slower but works):

   ```python
   device = 'cpu'  # Instead of 'cuda'
   model.to(device)
   ```

5. **Use smaller model:**
   - Try MobileNet instead of ResNet152
   - Reduce hidden layer sizes

---

### Issue: Training loss doesn't decrease / Loss becomes NaN

**Problem:** Unstable training, usually gradient-related.

**Solutions:**

1. **Reduce learning rate:**

   ```python
   optimizer = torch.optim.Adam(model.parameters(), lr=1e-4)  # Lower lr
   ```

2. **Use gradient clipping:**

   ```python
   torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)
   ```

3. **Check for NaN inputs:**

   ```python
   print(X.isnan().sum())
   print(y.isnan().sum())
   ```

4. **Normalize inputs:**

   ```python
   # Images should be in [0, 1] or [-1, 1]
   X = X / 255.0  # If pixel values are 0-255
   ```

5. **Batch normalization helps:**

   ```python
   nn.BatchNorm2d(64)  # After convolutional layers
   ```

---

### Issue: Model training too slowly

**Problem:** Training takes forever.

**Solutions:**

1. **Use GPU:** (See GPU setup above)

2. **Reduce model size:**
   - Fewer layers, smaller hidden dimensions

3. **Use pre-trained models (transfer learning):**

   ```python
   model = torchvision.models.resnet18(pretrained=True)
   # Only train new head, freeze backbone
   ```

4. **Increase batch size:**
   - Faster if your GPU can handle it (30-50% speedup)
   - But may hurt convergence

5. **Check for bottlenecks:**

   ```python
   import cProfile
   cProfile.run('train_one_epoch()')
   ```

---

## Generative AI & LLM Issues

### Issue: "API rate limit exceeded"

**Problem:** You've made too many requests to free LLM API.

**Solutions:**

1. **Wait and retry:**
   - Most services reset limits hourly or daily

   ```python
   import time
   time.sleep(60)  # Wait 1 minute
   ```

2. **Switch to free API:**
   - Try Google Gemini, Groq, or Together AI
   - Each has different rate limits

3. **Use local LLM:**

   ```bash
   # Llama.cpp - run locally, no rate limits
   pip install llama-cpp-python
   ```

4. **Batch requests:**
   - Fewer API calls per request

---

### Issue: LLM response is irrelevant or "hallucinated"

**Problem:** Model generates plausible but false information.

**Solutions:**

1. **Use RAG (Retrieval-Augmented Generation):**
   - Ground responses in real documents
   - See [RAG guide](../gen-ai-learning-path/rag.md)

2. **Better prompt engineering:**
   - Give specific instructions
   - Use examples (few-shot prompting)
   - Ask for reasoning step-by-step

3. **Lower temperature:**

   ```python
   response = client.chat.completions.create(
       model="gpt-3.5",
       messages=[...],
       temperature=0.2  # Lower = more deterministic
   )
   ```

4. **Use function calling/tools:**
   - Let agent decide which tool to use
   - Prevents hallucinated responses

---

### Issue: "Max tokens exceeded" or context window full

**Problem:** Your prompt is too long for the model.

**Solutions:**

1. **Summarize context:**
   - Don't send entire documents
   - Extract relevant sections first

2. **Use RAG with chunking:**
   - Break documents into chunks
   - Retrieve only relevant chunks

3. **Use model with larger context:**
   - GPT-4 Turbo: 128K tokens
   - Claude 3 Opus: 200K tokens
   - Open source: Check model specs

---

### Issue: "Invalid API key" or authentication error

**Problem:** Can't authenticate with LLM API.

**Solutions:**

1. **Check credentials:**

   ```python
   import os
   print(os.getenv('OPENAI_API_KEY'))  # Should print your key
   ```

2. **Set environment variable correctly:**

   ```bash
   export OPENAI_API_KEY="sk-..."  # macOS/Linux
   set OPENAI_API_KEY=sk-...        # Windows
   ```

3. **Use .env file** (recommended):

   ```bash
   pip install python-dotenv
   ```

   ```python
   from dotenv import load_dotenv
   load_dotenv()  # Loads from .env file
   ```

4. **Never commit API keys:**
   - Add to `.gitignore`
   - Use environment variables or `.env` files

---

## General Debugging Tips

### Issue: "It works locally but fails in production"

**Common causes & fixes:**

1. **Different data format:**
   - Test on unseen data, not training data
   - Check data types (int vs. float, string encoding)

2. **Version mismatch:**

   ```bash
   pip freeze > requirements.txt  # Save versions locally
   pip install -r requirements.txt  # Use same versions in prod
   ```

3. **Missing preprocessing:**
   - Always apply same preprocessing to new data
   - Save scaler/encoder, load in production

4. **Environment variables:**
   - API keys, model paths might be different
   - Use configuration files

---

### Debugging Checklist

When stuck, try this systematically:

- [ ] Check error message carefully (Google it!)
- [ ] Verify data format and shape
- [ ] Look at example data with `.head()` or `.describe()`
- [ ] Simplify: Try with smaller dataset or simpler model
- [ ] Print intermediate values
- [ ] Read the documentation for the library
- [ ] Check Stack Overflow or GitHub issues
- [ ] Try a fresh Python session (kernel restart)
- [ ] Ask in communities: Reddit r/MachineLearning, Discord servers

---

## Quick Reference by Error

| Error | Likely Cause | Solution |
|-------|---|---|
| `ModuleNotFoundError` | Package not installed | `pip install package_name` |
| `CUDA out of memory` | Batch too large | Reduce batch size |
| `NaN loss` | Learning rate too high | Reduce learning rate, normalize inputs |
| `Low accuracy` | Bad data or wrong features | Check data quality, start with baseline |
| `Overfitting` | Model too complex | Add regularization, get more data |
| `Rate limit exceeded` | Too many API calls | Wait, switch API, or use local model |
| `Hallucination` | LLM making stuff up | Use RAG, better prompts, lower temperature |
| `ConvergenceWarning` | Optimization stuck | Increase iterations, scale features |
| `Confused about concept` | Missing fundamentals | Check [Glossary](./glossary.md) or [Math Essentials](./math-essentials.md) |

---

## Still Stuck?

1. **Check the [Glossary](./glossary.md)** for definitions
2. **Review [Math Essentials](./math-essentials.md)** for foundational concepts
3. **Check relevant section:**
   - ML issues → [Machine Learning](../machine-learning/index.md)
   - DL issues → [Deep Learning](../deep-learning/index.md)
   - Gen AI issues → [Generative AI](../gen-ai-learning-path/index.md)
4. **Search Stack Overflow** with your error message
5. **Post in communities:** Reddit, Discord, GitHub Discussions

Remember: **Everyone gets stuck.** It's normal. Debugging is a key skill in AI/ML!
