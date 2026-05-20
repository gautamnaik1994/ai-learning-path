# Production Deployment Guide

This guide covers taking your AI/ML models from development to production deployment.

## Overview

Deploying AI models to production involves:

- **Model serialization** (saving trained models)
- **API creation** (exposing model as service)
- **Containerization** (Docker for consistency)
- **Cloud hosting** (AWS, GCP, Azure, etc.)
- **Monitoring & scaling** (track performance, handle load)
- **Cost optimization** (efficient resource usage)

---

## Phase 1: Preparation

### Pre-deployment Checklist

Before deploying, ensure:

- [ ] Model training is complete and reproducible
- [ ] Model performance validated on test set
- [ ] Dependencies documented (requirements.txt)
- [ ] Code tested locally
- [ ] Security considerations addressed (API keys, data protection)
- [ ] Performance benchmarks established (latency, throughput)
- [ ] Rollback plan exists
- [ ] Monitoring/logging setup planned

### Model Serialization

Save your trained model for later loading:

**PyTorch:**

```python
import torch

# Save model
torch.save(model.state_dict(), 'model.pth')

# Load model
model = MyModel()  # Define architecture
model.load_state_dict(torch.load('model.pth'))
model.eval()  # Set to evaluation mode
```

**TensorFlow:**

```python
import tensorflow as tf

# Save model
model.save('model_directory')  # SavedModel format

# Load model
model = tf.keras.models.load_model('model_directory')
```

**Scikit-learn:**

```python
import pickle

# Save model
with open('model.pkl', 'wb') as f:
    pickle.dump(model, f)

# Load model
with open('model.pkl', 'rb') as f:
    model = pickle.load(f)
```

### Dependency Management

Create requirements file for reproducibility:

```bash
pip freeze > requirements.txt
```

**requirements.txt example:**

```
torch==2.0.0
torchvision==0.15.0
numpy==1.24.0
pandas==2.0.0
fastapi==0.95.0
uvicorn==0.21.0
pydantic==1.10.0
```

---

## Phase 2: API Creation

### Why APIs?

APIs allow client applications to use your model without running it locally.

```
┌─────────────────┐
│ Client App      │
│ (Web, Mobile)   │
└────────┬────────┘
         │ HTTP/REST
         │
    ┌────▼────────┐
    │ API Server  │
    │ (FastAPI)   │
    └────┬────────┘
         │
    ┌────▼────────┐
    │ Model       │
    │ (PyTorch)   │
    └─────────────┘
```

### FastAPI: Building ML APIs

**Installation:**

```bash
pip install fastapi uvicorn python-multipart
```

**Simple Prediction API:**

```python
from fastapi import FastAPI, File, UploadFile
from fastapi.responses import JSONResponse
import torch
import numpy as np
from PIL import Image
import io

app = FastAPI(title="Image Classification API", version="1.0")

# Load model at startup
model = None

@app.on_event("startup")
async def load_model():
    global model
    model = torch.jit.load('model.pth')
    model.eval()

@app.get("/health")
async def health_check():
    """Health check endpoint."""
    return {"status": "healthy"}

@app.post("/predict")
async def predict(file: UploadFile = File(...)):
    """Make prediction on uploaded image."""
    try:
        # Read image
        contents = await file.read()
        image = Image.open(io.BytesIO(contents))
        
        # Preprocess
        image = image.resize((224, 224))
        image_array = np.array(image) / 255.0
        image_tensor = torch.from_numpy(image_array).permute(2, 0, 1).unsqueeze(0).float()
        
        # Predict
        with torch.no_grad():
            output = model(image_tensor)
            prediction = torch.argmax(output, dim=1).item()
            confidence = torch.softmax(output, dim=1)[0][prediction].item()
        
        return {
            "prediction": prediction,
            "confidence": float(confidence),
            "status": "success"
        }
    except Exception as e:
        return {"error": str(e), "status": "error"}, 500

@app.post("/batch_predict")
async def batch_predict(file: UploadFile = File(...)):
    """Make predictions on batch of images."""
    # Implementation for multiple images
    pass

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

**Run locally:**

```bash
python app.py
# Access at http://localhost:8000
# Docs at http://localhost:8000/docs
```

### API Best Practices

1. **Input validation:**

   ```python
   from pydantic import BaseModel, Field
   
   class PredictionRequest(BaseModel):
       text: str = Field(..., min_length=1, max_length=5000)
       confidence_threshold: float = Field(0.5, ge=0, le=1)
   ```

2. **Error handling:**

   ```python
   from fastapi import HTTPException
   
   @app.post("/predict")
   async def predict(request: PredictionRequest):
       try:
           result = model.predict(request.text)
           return {"result": result}
       except ValueError as e:
           raise HTTPException(status_code=400, detail=str(e))
       except Exception as e:
           raise HTTPException(status_code=500, detail="Internal error")
   ```

3. **Rate limiting:**

   ```bash
   pip install slowapi
   ```

4. **Caching:**

   ```python
   from functools import lru_cache
   
   @lru_cache(maxsize=100)
   def get_cached_prediction(input_text: str):
       return model.predict(input_text)
   ```

---

## Phase 3: Containerization (Docker)

### Why Docker?

Docker ensures your model runs identically everywhere:

- Local machine
- Staging server
- Production cloud

### Dockerfile Example

```dockerfile
# Base image with Python
FROM python:3.11-slim

# Set working directory
WORKDIR /app

# Copy requirements
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY app.py .
COPY model.pth .

# Expose port
EXPOSE 8000

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD python -c "import requests; requests.get('http://localhost:8000/health')"

# Run application
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Build and Test Docker Image

```bash
# Build image
docker build -t my-ml-api:1.0 .

# Run container locally
docker run -p 8000:8000 my-ml-api:1.0

# Test
curl http://localhost:8000/health

# Stop container
docker stop <container_id>
```

### Docker Compose for Development

```yaml
version: '3.8'

services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      - MODEL_PATH=/app/model.pth
      - API_KEY=${API_KEY}
    volumes:
      - ./data:/app/data
    depends_on:
      - redis

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
```

**Run:**

```bash
docker-compose up
```

---

## Phase 4: Cloud Deployment

### Option 1: AWS

#### Using AWS Lambda (Serverless)

Best for: Low-traffic, sporadic predictions

**Limitations:**

- Limited CPU/memory
- 15-minute timeout
- Requires model to be small (<250MB)

#### Using AWS EC2 (Virtual Machines)

Best for: Consistent traffic, large models

```bash
# 1. Launch EC2 instance
# 2. SSH into instance
ssh -i key.pem ec2-user@instance-ip

# 3. Install Docker
sudo yum update -y
sudo yum install -y docker
sudo service docker start

# 4. Pull and run container
docker run -p 8000:8000 my-ml-api:1.0
```

#### Using AWS SageMaker (Managed ML)

Best for: Enterprise ML deployments

```python
import sagemaker
from sagemaker.pytorch.estimator import PyTorch

# Training job
pytorch = PyTorch(
    entry_point='train.py',
    role='arn:aws:iam::123456789:role/SageMakerRole',
    instance_count=1,
    instance_type='ml.p3.2xlarge',
    framework_version='2.0.0'
)
pytorch.fit('s3://bucket/training-data')

# Deployment
predictor = pytorch.deploy(
    initial_instance_count=1,
    instance_type='ml.m5.large'
)
result = predictor.predict(test_data)
```

### Option 2: Google Cloud

#### Using Cloud Run (Containerized)

Best for: Docker containers, simple APIs

```bash
# 1. Build and push to Container Registry
gcloud builds submit --tag gcr.io/my-project/ml-api:1.0

# 2. Deploy to Cloud Run
gcloud run deploy ml-api \
  --image gcr.io/my-project/ml-api:1.0 \
  --platform managed \
  --region us-central1 \
  --memory 2Gi \
  --timeout 3600
```

#### Using Vertex AI (Google's managed ML platform)

```bash
# Create and deploy model
gcloud ai models upload \
  --region=us-central1 \
  --display-name=my-model \
  --container-image-uri=gcr.io/my-project/ml-api:1.0
```

### Option 3: Azure

#### Using Azure App Service

```bash
# 1. Create resource group
az group create --name myGroup --location westus

# 2. Create App Service plan
az appservice plan create --name myPlan --resource-group myGroup --sku FREE

# 3. Create web app from Docker
az webapp create --resource-group myGroup \
  --plan myPlan --name myMLApp \
  --deployment-container-image-name my-ml-api:1.0
```

#### Using Azure Container Instances

```bash
az container create \
  --resource-group myGroup \
  --name ml-api \
  --image my-ml-api:1.0 \
  --ports 8000
```

---

## Phase 5: Monitoring & Logging

### Logging Setup

```python
import logging
import json
from datetime import datetime

# Structured logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

@app.post("/predict")
async def predict(request: PredictionRequest):
    start_time = datetime.now()
    
    logger.info(json.dumps({
        "event": "prediction_request",
        "input_length": len(request.text),
        "timestamp": start_time.isoformat()
    }))
    
    try:
        result = model.predict(request.text)
        
        duration = (datetime.now() - start_time).total_seconds()
        logger.info(json.dumps({
            "event": "prediction_success",
            "duration": duration,
            "confidence": result.confidence
        }))
        
        return {"result": result}
    except Exception as e:
        logger.error(json.dumps({
            "event": "prediction_error",
            "error": str(e),
            "duration": (datetime.now() - start_time).total_seconds()
        }))
        raise
```

### Metrics to Track

- **Latency:** Response time for predictions
- **Throughput:** Predictions per second
- **Accuracy:** Model performance on production data
- **Error rate:** Failed predictions
- **Cost:** Resources consumed

### Monitoring Tools

- **AWS CloudWatch:** Logs, metrics, dashboards
- **Google Cloud Logging:** Centralized logging
- **Datadog:** APM and monitoring
- **Prometheus + Grafana:** Open-source monitoring
- **ELK Stack:** Elasticsearch, Logstash, Kibana

### Example with Prometheus

```python
from prometheus_client import Counter, Histogram, start_http_server

# Metrics
prediction_counter = Counter('predictions_total', 'Total predictions')
prediction_latency = Histogram('prediction_latency_seconds', 'Prediction latency')
prediction_errors = Counter('prediction_errors_total', 'Total errors')

@app.post("/predict")
@prediction_latency.time()
async def predict(request: PredictionRequest):
    try:
        result = model.predict(request.text)
        prediction_counter.inc()
        return {"result": result}
    except Exception as e:
        prediction_errors.inc()
        raise

if __name__ == "__main__":
    start_http_server(8001)  # Metrics on port 8001
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

---

## Phase 6: Scaling & Optimization

### Horizontal Scaling

Add more instances to handle increased load:

**Docker Compose with multiple replicas:**

```yaml
services:
  api:
    build: .
    deploy:
      replicas: 3
    ports:
      - "8000-8002:8000"
```

**Kubernetes (for enterprise):**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ml-api
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: api
        image: my-ml-api:1.0
        resources:
          requests:
            memory: "2Gi"
            cpu: "1"
```

### Load Balancing

Distribute requests across multiple instances:

- **AWS ELB:** Elastic Load Balancer
- **NGINX:** Lightweight, open-source
- **HAProxy:** High-performance load balancer

**NGINX example:**

```nginx
upstream api_backend {
    server api1:8000;
    server api2:8000;
    server api3:8000;
}

server {
    listen 80;
    
    location /predict {
        proxy_pass http://api_backend;
    }
}
```

### Model Optimization

For faster predictions:

**Quantization (reduce precision):**

```python
# PyTorch
quantized_model = torch.quantization.quantize_dynamic(
    model, {torch.nn.Linear}, dtype=torch.qint8
)

# TensorFlow
import tensorflow as tf
converter = tf.lite.TFLiteConverter.from_saved_model(model_dir)
converter.optimizations = [tf.lite.Optimize.DEFAULT]
tflite_model = converter.convert()
```

**Model Distillation (train smaller model):**

```python
# Train small model to mimic large model
# Results in 10-100x speedup with minimal accuracy loss
```

**Batch Processing:**

```python
# Instead of individual predictions, process batches
batch_predictions = model.predict_batch(batch_data)
```

---

## Phase 7: Cost Optimization

### Strategies

| Strategy | Impact | Effort |
|----------|--------|--------|
| **Use smaller instances** | 50-70% reduction | Low |
| **Auto-scaling** | 30-50% reduction | Medium |
| **Spot/Reserved instances** | 70% reduction | High |
| **Model optimization** | 30-50% reduction | Medium |
| **Caching responses** | 20-40% reduction | Low |

### Example Cost Comparison

**Deploy image classification on AWS:**

| Configuration | Monthly Cost |
|---|---|
| Single m5.large instance | $50-70 |
| Auto-scaled (2-5 instances) | $100-250 |
| Serverless (Lambda) | $10-500 (depends on usage) |

---

## Deployment Checklist

- [ ] Model serialized and testable
- [ ] API created with error handling
- [ ] Docker image builds and runs locally
- [ ] All dependencies documented
- [ ] Security configured (API keys, HTTPS)
- [ ] Monitoring and logging setup
- [ ] Health checks implemented
- [ ] Performance benchmarked
- [ ] Cost estimated
- [ ] Rollback plan documented
- [ ] Auto-scaling configured
- [ ] Load testing completed
- [ ] Team trained on deployment

---

## Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| **Model too large** | Quantization, distillation, separate smaller services |
| **Slow predictions** | Caching, batching, model optimization, GPU acceleration |
| **High costs** | Auto-scaling, spot instances, smaller models |
| **Out of memory** | Reduce batch size, optimize code, split model |
| **Cold start delays** | Keep instances warm, use provisioned concurrency |
| **Data drift** | Monitor model performance, retrain periodically |

---

## Next Steps

1. **Package your model** as Docker container
2. **Create API** with FastAPI
3. **Test locally** and verify performance
4. **Choose cloud provider** based on needs
5. **Deploy** to staging/production
6. **Monitor** performance and costs
7. **Iterate** based on feedback

---

## Additional Resources

- [FastAPI Deployment Docs](https://fastapi.tiangolo.com/deployment/)
- [Docker Best Practices](https://docs.docker.com/develop/dev-best-practices/)
- [AWS ML Deployment](https://docs.aws.amazon.com/machine-learning/)
- [Google Cloud ML Solutions](https://cloud.google.com/solutions/machine-learning)
- [Azure ML](https://docs.microsoft.com/en-us/azure/machine-learning/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)

---

## Decision Framework

```
Model size < 1GB and traffic moderate?
  ├─ YES, using AWS → Consider Lambda or EC2
  └─ NO → Consider SageMaker or Kubernetes

Using Docker?
  ├─ YES → Cloud Run (GCP) or ECS (AWS) or App Service (Azure)
  └─ NO → SageMaker or Vertex AI

Need auto-scaling?
  ├─ YES → Use managed services (Lambda, Cloud Run)
  └─ NO → EC2/VM instances sufficient

Cost-sensitive?
  ├─ YES → Use serverless or spot instances
  └─ NO → Managed services for ease
```

Good luck deploying your models!
