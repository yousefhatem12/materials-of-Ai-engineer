# PyTorch — From Basics to Advanced

A complete reference guide to PyTorch, covering tensors, autograd, neural networks, training pipelines, and advanced deep learning techniques.

## Table of Contents

1. [Introduction](#1-introduction)
2. [Setup & Installation](#2-setup--installation)
3. [Tensors — The Basics](#3-tensors--the-basics)
4. [Autograd (Automatic Differentiation)](#4-autograd-automatic-differentiation)
5. [Building Neural Networks (`nn.Module`)](#5-building-neural-networks-nnmodule)
6. [Loss Functions & Optimizers](#6-loss-functions--optimizers)
7. [Datasets & DataLoaders](#7-datasets--dataloaders)
8. [Training Loop](#8-training-loop)
9. [Evaluation & Metrics](#9-evaluation--metrics)
10. [GPU / Device Management](#10-gpu--device-management)
11. [Saving & Loading Models](#11-saving--loading-models)
12. [Common Layers & Architectures](#12-common-layers--architectures)
13. [Convolutional Neural Networks](#13-convolutional-neural-networks)
14. [Recurrent & Sequence Models](#14-recurrent--sequence-models)
15. [Transformers & Attention](#15-transformers--attention)
16. [Transfer Learning](#16-transfer-learning)
17. [Advanced Training Techniques](#17-advanced-training-techniques)
18. [Custom Autograd Functions](#18-custom-autograd-functions)
19. [Mixed Precision & Performance](#19-mixed-precision--performance)
20. [Distributed Training](#20-distributed-training)
21. [Model Deployment](#21-model-deployment)
22. [Debugging & Best Practices](#22-debugging--best-practices)
23. [Useful Utilities](#23-useful-utilities)
24. [Resources](#24-resources)

---

## 1. Introduction

PyTorch is an open-source deep learning framework known for its dynamic computation graph ("define-by-run"), Pythonic API, and strong GPU acceleration. It's widely used in both research and production for computer vision, NLP, reinforcement learning, and more.

**Why PyTorch?**
- Intuitive, Pythonic syntax — feels like NumPy with GPU support
- Dynamic computation graphs (easy debugging, flexible architectures)
- Massive ecosystem: `torchvision`, `torchaudio`, `torchtext`, Hugging Face `transformers`
- Strong community and industry adoption

---

## 2. Setup & Installation

```bash
# CPU only
pip install torch torchvision torchaudio

# With CUDA (check pytorch.org for the exact command for your CUDA version)
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
```

### Verify Installation

```python
import torch

print(torch.__version__)
print(torch.cuda.is_available())   # True if GPU/CUDA is available
print(torch.cuda.get_device_name(0) if torch.cuda.is_available() else "No GPU")
```

---

## 3. Tensors — The Basics

Tensors are PyTorch's core data structure — similar to NumPy arrays but with GPU support and autograd tracking.

### Creating Tensors

```python
import torch

a = torch.tensor([1, 2, 3])                 # from a list
b = torch.zeros(3, 4)                         # 3x4 tensor of zeros
c = torch.ones(2, 2)                            # 2x2 tensor of ones
d = torch.rand(2, 3)                              # uniform random [0,1)
e = torch.randn(2, 3)                               # normal distribution
f = torch.arange(0, 10, 2)                            # [0,2,4,6,8]
g = torch.linspace(0, 1, 5)                             # 5 evenly spaced values
h = torch.eye(3)                                          # identity matrix

# From NumPy
import numpy as np
np_array = np.array([1, 2, 3])
t = torch.from_numpy(np_array)
```

### Tensor Attributes

```python
x = torch.rand(3, 4)
x.shape         # torch.Size([3, 4])
x.dtype          # torch.float32
x.device           # cpu or cuda
x.ndim               # number of dimensions
x.numel()              # total number of elements
```

### Indexing & Slicing

```python
x = torch.arange(12).reshape(3, 4)
x[0]           # first row
x[:, 0]         # first column
x[0, 1]          # single element
x[1:, 2:]         # sub-matrix
x[x > 5]           # boolean mask indexing
```

### Reshaping

```python
x = torch.arange(12)
x.reshape(3, 4)
x.view(3, 4)              # like reshape, requires contiguous memory
x.unsqueeze(0)              # add a dimension -> shape [1, 12]
x.squeeze()                   # remove dimensions of size 1
x.permute(1, 0)                 # reorder dimensions
x.flatten()                       # flatten to 1D
x.transpose(0, 1)                   # swap two dimensions
```

### Operations

```python
a = torch.tensor([1.0, 2.0, 3.0])
b = torch.tensor([4.0, 5.0, 6.0])

a + b            # element-wise add
a * b              # element-wise multiply
a @ b                # dot product (1D) / matrix multiply (2D+)
torch.matmul(a, b)     # same as @
a.sum()                  # sum of all elements
a.mean()                   # mean
a.max()                      # max value
a.argmax()                     # index of max value

# In-place operations (suffixed with _)
a.add_(1)          # modifies a in place
```

### Broadcasting

```python
a = torch.rand(3, 1)
b = torch.rand(1, 4)
c = a + b   # broadcasts to shape [3, 4]
```

---

## 4. Autograd (Automatic Differentiation)

PyTorch automatically tracks operations on tensors with `requires_grad=True` and computes gradients via backpropagation.

```python
x = torch.tensor(2.0, requires_grad=True)
y = x ** 2 + 3 * x + 1

y.backward()        # compute dy/dx
print(x.grad)          # tensor(7.) since dy/dx = 2x + 3 = 7 at x=2
```

### Gradients with Vectors

```python
x = torch.tensor([1.0, 2.0, 3.0], requires_grad=True)
y = (x ** 2).sum()
y.backward()
print(x.grad)   # tensor([2., 4., 6.])
```

### Disabling Gradient Tracking

```python
# For inference / evaluation
with torch.no_grad():
    y = x * 2

# Or detach a tensor from the graph
z = x.detach()

# Or use inference mode (faster than no_grad)
with torch.inference_mode():
    y = x * 2
```

### Zeroing Gradients

Gradients accumulate by default, so they must be reset each training step:

```python
x.grad.zero_()   # or optimizer.zero_grad() in training loops
```

---

## 5. Building Neural Networks (`nn.Module`)

```python
import torch.nn as nn
import torch.nn.functional as F

class SimpleNet(nn.Module):
    def __init__(self, input_size, hidden_size, output_size):
        super().__init__()
        self.fc1 = nn.Linear(input_size, hidden_size)
        self.fc2 = nn.Linear(hidden_size, output_size)

    def forward(self, x):
        x = F.relu(self.fc1(x))
        x = self.fc2(x)
        return x

model = SimpleNet(784, 128, 10)
print(model)

# View parameters
for name, param in model.named_parameters():
    print(name, param.shape)
```

### Sequential Models

```python
model = nn.Sequential(
    nn.Linear(784, 128),
    nn.ReLU(),
    nn.Linear(128, 64),
    nn.ReLU(),
    nn.Linear(64, 10)
)
```

### Common Layer Types

```python
nn.Linear(in_features, out_features)      # fully connected layer
nn.Conv2d(in_channels, out_channels, kernel_size)  # convolution
nn.MaxPool2d(kernel_size)                    # pooling
nn.BatchNorm2d(num_features)                   # batch normalization
nn.Dropout(p=0.5)                                # dropout regularization
nn.Embedding(num_embeddings, embedding_dim)        # embedding lookup
nn.LSTM(input_size, hidden_size)                     # recurrent layer
nn.TransformerEncoderLayer(d_model, nhead)             # transformer block
```

### Activation Functions

```python
nn.ReLU()
nn.Sigmoid()
nn.Tanh()
nn.Softmax(dim=1)
nn.LeakyReLU(0.1)
nn.GELU()
```

---

## 6. Loss Functions & Optimizers

### Loss Functions

```python
criterion = nn.CrossEntropyLoss()     # classification (expects raw logits)
criterion = nn.MSELoss()                # regression
criterion = nn.BCELoss()                  # binary classification (with sigmoid)
criterion = nn.BCEWithLogitsLoss()          # binary classification (raw logits, more stable)
criterion = nn.NLLLoss()                      # negative log-likelihood

loss = criterion(predictions, targets)
```

### Optimizers

```python
import torch.optim as optim

optimizer = optim.SGD(model.parameters(), lr=0.01, momentum=0.9)
optimizer = optim.Adam(model.parameters(), lr=0.001)
optimizer = optim.AdamW(model.parameters(), lr=0.001, weight_decay=0.01)

# Learning rate scheduling
scheduler = optim.lr_scheduler.StepLR(optimizer, step_size=10, gamma=0.1)
scheduler = optim.lr_scheduler.CosineAnnealingLR(optimizer, T_max=50)
scheduler = optim.lr_scheduler.ReduceLROnPlateau(optimizer, mode='min', patience=5)
```

---

## 7. Datasets & DataLoaders

### Custom Dataset

```python
from torch.utils.data import Dataset, DataLoader

class MyDataset(Dataset):
    def __init__(self, data, labels):
        self.data = data
        self.labels = labels

    def __len__(self):
        return len(self.data)

    def __getitem__(self, idx):
        return self.data[idx], self.labels[idx]

dataset = MyDataset(data, labels)
loader = DataLoader(dataset, batch_size=32, shuffle=True, num_workers=4)

for batch_data, batch_labels in loader:
    ...
```

### Built-in Datasets (torchvision)

```python
from torchvision import datasets, transforms

transform = transforms.Compose([
    transforms.ToTensor(),
    transforms.Normalize((0.5,), (0.5,))
])

train_dataset = datasets.MNIST(root="./data", train=True, download=True, transform=transform)
train_loader = DataLoader(train_dataset, batch_size=64, shuffle=True)
```

### Common Transforms

```python
transforms.Resize((224, 224))
transforms.RandomHorizontalFlip()
transforms.RandomCrop(32, padding=4)
transforms.ColorJitter(brightness=0.2, contrast=0.2)
transforms.ToTensor()
transforms.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225])
```

---

## 8. Training Loop

```python
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
model = SimpleNet(784, 128, 10).to(device)
criterion = nn.CrossEntropyLoss()
optimizer = optim.Adam(model.parameters(), lr=0.001)

num_epochs = 10

for epoch in range(num_epochs):
    model.train()
    running_loss = 0.0

    for inputs, labels in train_loader:
        inputs, labels = inputs.to(device), labels.to(device)

        optimizer.zero_grad()               # reset gradients
        outputs = model(inputs)               # forward pass
        loss = criterion(outputs, labels)       # compute loss
        loss.backward()                           # backward pass
        optimizer.step()                            # update weights

        running_loss += loss.item()

    avg_loss = running_loss / len(train_loader)
    print(f"Epoch {epoch+1}/{num_epochs}, Loss: {avg_loss:.4f}")
```

---

## 9. Evaluation & Metrics

```python
model.eval()
correct, total = 0, 0

with torch.no_grad():
    for inputs, labels in test_loader:
        inputs, labels = inputs.to(device), labels.to(device)
        outputs = model(inputs)
        _, predicted = torch.max(outputs, 1)
        total += labels.size(0)
        correct += (predicted == labels).sum().item()

accuracy = 100 * correct / total
print(f"Accuracy: {accuracy:.2f}%")
```

### Common Metrics (with `torchmetrics`)

```bash
pip install torchmetrics
```

```python
from torchmetrics import Accuracy, F1Score, Precision, Recall

accuracy = Accuracy(task="multiclass", num_classes=10).to(device)
acc = accuracy(predictions, labels)
```

---

## 10. GPU / Device Management

```python
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

model.to(device)                  # move model to GPU
tensor = tensor.to(device)          # move tensor to GPU

# Check GPU memory
torch.cuda.memory_allocated()
torch.cuda.memory_reserved()
torch.cuda.empty_cache()             # free unused cached memory

# Multiple GPUs (simple approach)
if torch.cuda.device_count() > 1:
    model = nn.DataParallel(model)
```

---

## 11. Saving & Loading Models

### Save/Load State Dict (recommended)

```python
# Save
torch.save(model.state_dict(), "model.pth")

# Load
model = SimpleNet(784, 128, 10)
model.load_state_dict(torch.load("model.pth"))
model.eval()
```

### Save/Load Full Model

```python
torch.save(model, "model_full.pth")
model = torch.load("model_full.pth")
```

### Checkpointing (for resuming training)

```python
checkpoint = {
    "epoch": epoch,
    "model_state_dict": model.state_dict(),
    "optimizer_state_dict": optimizer.state_dict(),
    "loss": loss,
}
torch.save(checkpoint, "checkpoint.pth")

# Resume
checkpoint = torch.load("checkpoint.pth")
model.load_state_dict(checkpoint["model_state_dict"])
optimizer.load_state_dict(checkpoint["optimizer_state_dict"])
start_epoch = checkpoint["epoch"]
```

---

## 12. Common Layers & Architectures

```python
# Fully connected network
nn.Linear(in_features, out_features)

# Dropout for regularization
nn.Dropout(p=0.5)

# Batch/Layer normalization
nn.BatchNorm1d(num_features)
nn.LayerNorm(normalized_shape)

# Residual connection example
class ResidualBlock(nn.Module):
    def __init__(self, channels):
        super().__init__()
        self.conv1 = nn.Conv2d(channels, channels, 3, padding=1)
        self.conv2 = nn.Conv2d(channels, channels, 3, padding=1)
        self.bn1 = nn.BatchNorm2d(channels)
        self.bn2 = nn.BatchNorm2d(channels)

    def forward(self, x):
        residual = x
        out = F.relu(self.bn1(self.conv1(x)))
        out = self.bn2(self.conv2(out))
        return F.relu(out + residual)
```

---

## 13. Convolutional Neural Networks

```python
class CNN(nn.Module):
    def __init__(self, num_classes=10):
        super().__init__()
        self.features = nn.Sequential(
            nn.Conv2d(3, 32, kernel_size=3, padding=1),
            nn.BatchNorm2d(32),
            nn.ReLU(),
            nn.MaxPool2d(2),               # 32x32 -> 16x16

            nn.Conv2d(32, 64, kernel_size=3, padding=1),
            nn.BatchNorm2d(64),
            nn.ReLU(),
            nn.MaxPool2d(2),               # 16x16 -> 8x8
        )
        self.classifier = nn.Sequential(
            nn.Flatten(),
            nn.Linear(64 * 8 * 8, 256),
            nn.ReLU(),
            nn.Dropout(0.5),
            nn.Linear(256, num_classes)
        )

    def forward(self, x):
        x = self.features(x)
        x = self.classifier(x)
        return x
```

**Key concepts:**
- `kernel_size`: size of the filter window
- `stride`: step size the filter moves
- `padding`: zero-padding added to input borders
- `Conv2d` output size: `(W - K + 2P) / S + 1`

---

## 14. Recurrent & Sequence Models

```python
class LSTMModel(nn.Module):
    def __init__(self, input_size, hidden_size, num_layers, num_classes):
        super().__init__()
        self.lstm = nn.LSTM(input_size, hidden_size, num_layers, batch_first=True)
        self.fc = nn.Linear(hidden_size, num_classes)

    def forward(self, x):
        out, (hidden, cell) = self.lstm(x)      # out: [batch, seq_len, hidden]
        out = self.fc(out[:, -1, :])              # use last time step
        return out

# GRU is a lighter-weight alternative
nn.GRU(input_size, hidden_size, num_layers, batch_first=True)
```

---

## 15. Transformers & Attention

### Using Built-in Transformer Layers

```python
encoder_layer = nn.TransformerEncoderLayer(d_model=512, nhead=8, dim_feedforward=2048)
transformer_encoder = nn.TransformerEncoder(encoder_layer, num_layers=6)

src = torch.rand(10, 32, 512)  # (seq_len, batch, features)
output = transformer_encoder(src)
```

### Simple Self-Attention (from scratch, for understanding)

```python
class SelfAttention(nn.Module):
    def __init__(self, embed_size):
        super().__init__()
        self.query = nn.Linear(embed_size, embed_size)
        self.key = nn.Linear(embed_size, embed_size)
        self.value = nn.Linear(embed_size, embed_size)
        self.scale = embed_size ** 0.5

    def forward(self, x):
        Q, K, V = self.query(x), self.key(x), self.value(x)
        scores = torch.matmul(Q, K.transpose(-2, -1)) / self.scale
        weights = F.softmax(scores, dim=-1)
        return torch.matmul(weights, V)
```

### Using Hugging Face Transformers (built on PyTorch)

```bash
pip install transformers
```

```python
from transformers import AutoModel, AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")
model = AutoModel.from_pretrained("bert-base-uncased")

inputs = tokenizer("Hello, PyTorch!", return_tensors="pt")
outputs = model(**inputs)
```

---

## 16. Transfer Learning

```python
import torchvision.models as models

# Load a pretrained model
model = models.resnet18(weights=models.ResNet18_Weights.DEFAULT)

# Freeze all layers
for param in model.parameters():
    param.requires_grad = False

# Replace the final layer for your task
num_classes = 10
model.fc = nn.Linear(model.fc.in_features, num_classes)

# Only the new layer will be trained
optimizer = optim.Adam(model.fc.parameters(), lr=0.001)
```

### Fine-Tuning (unfreeze some layers)

```python
for name, param in model.named_parameters():
    if "layer4" in name or "fc" in name:
        param.requires_grad = True
```

---

## 17. Advanced Training Techniques

### Gradient Clipping

```python
torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)
```

### Early Stopping

```python
best_loss = float("inf")
patience, patience_counter = 5, 0

for epoch in range(num_epochs):
    val_loss = validate(model, val_loader)
    if val_loss < best_loss:
        best_loss = val_loss
        patience_counter = 0
        torch.save(model.state_dict(), "best_model.pth")
    else:
        patience_counter += 1
        if patience_counter >= patience:
            print("Early stopping triggered")
            break
```

### Gradient Accumulation (simulate larger batch sizes)

```python
accumulation_steps = 4
optimizer.zero_grad()

for i, (inputs, labels) in enumerate(train_loader):
    outputs = model(inputs)
    loss = criterion(outputs, labels) / accumulation_steps
    loss.backward()

    if (i + 1) % accumulation_steps == 0:
        optimizer.step()
        optimizer.zero_grad()
```

### Custom Learning Rate Warmup

```python
def warmup_lr(step, warmup_steps=1000, base_lr=1e-3):
    if step < warmup_steps:
        return base_lr * step / warmup_steps
    return base_lr

scheduler = optim.lr_scheduler.LambdaLR(optimizer, lr_lambda=lambda step: warmup_lr(step) / base_lr)
```

---

## 18. Custom Autograd Functions

```python
class MyReLU(torch.autograd.Function):
    @staticmethod
    def forward(ctx, input):
        ctx.save_for_backward(input)
        return input.clamp(min=0)

    @staticmethod
    def backward(ctx, grad_output):
        input, = ctx.saved_tensors
        grad_input = grad_output.clone()
        grad_input[input < 0] = 0
        return grad_input

# Usage
relu = MyReLU.apply
output = relu(x)
```

### Gradient Checking

```python
from torch.autograd import gradcheck

input = torch.randn(3, requires_grad=True, dtype=torch.double)
gradcheck(MyReLU.apply, (input,))
```

---

## 19. Mixed Precision & Performance

### Automatic Mixed Precision (AMP)

```python
scaler = torch.cuda.amp.GradScaler()

for inputs, labels in train_loader:
    inputs, labels = inputs.to(device), labels.to(device)
    optimizer.zero_grad()

    with torch.cuda.amp.autocast():
        outputs = model(inputs)
        loss = criterion(outputs, labels)

    scaler.scale(loss).backward()
    scaler.step(optimizer)
    scaler.update()
```

### `torch.compile` (PyTorch 2.0+)

```python
model = torch.compile(model)   # JIT-compiles the model for speed
```

### Performance Tips

```python
# Use pinned memory + more workers for faster data loading
DataLoader(dataset, batch_size=64, num_workers=4, pin_memory=True)

# Use non_blocking transfers with pinned memory
inputs = inputs.to(device, non_blocking=True)

# Enable cuDNN autotuner for fixed input sizes
torch.backends.cudnn.benchmark = True
```

---

## 20. Distributed Training

### DistributedDataParallel (DDP) — recommended over DataParallel

```python
import torch.distributed as dist
from torch.nn.parallel import DistributedDataParallel as DDP

def setup(rank, world_size):
    dist.init_process_group("nccl", rank=rank, world_size=world_size)
    torch.cuda.set_device(rank)

def train(rank, world_size):
    setup(rank, world_size)
    model = SimpleNet(784, 128, 10).to(rank)
    model = DDP(model, device_ids=[rank])
    # ... training loop as usual
```

Launch with:
```bash
torchrun --nproc_per_node=4 train.py
```

---

## 21. Model Deployment

### TorchScript (for production, no Python dependency)

```python
# Tracing (for models without control flow)
example_input = torch.rand(1, 784)
traced_model = torch.jit.trace(model, example_input)
traced_model.save("model_traced.pt")

# Scripting (handles control flow / loops)
scripted_model = torch.jit.script(model)
scripted_model.save("model_scripted.pt")

# Load in production
loaded_model = torch.jit.load("model_traced.pt")
```

### ONNX Export (for cross-framework deployment)

```python
torch.onnx.export(
    model,
    example_input,
    "model.onnx",
    input_names=["input"],
    output_names=["output"],
    dynamic_axes={"input": {0: "batch_size"}, "output": {0: "batch_size"}}
)
```

### Serving Options
- **TorchServe** — official model-serving framework
- **ONNX Runtime** — cross-platform inference
- **FastAPI / Flask** — wrap the model in a REST API

---

## 22. Debugging & Best Practices

```python
# Check for NaN/Inf values
torch.isnan(tensor).any()
torch.isinf(tensor).any()

# Anomaly detection (find where NaN gradients originate)
torch.autograd.set_detect_anomaly(True)

# Print model summary
print(model)
sum(p.numel() for p in model.parameters() if p.requires_grad)  # count trainable params

# Reproducibility
torch.manual_seed(42)
torch.cuda.manual_seed_all(42)
```

### Common Pitfalls
- Forgetting `optimizer.zero_grad()` → gradients accumulate incorrectly
- Forgetting `model.eval()` during inference → BatchNorm/Dropout behave differently
- Forgetting `with torch.no_grad()` during inference → wastes memory tracking gradients
- Mismatched tensor shapes → use `.shape` liberally when debugging
- Forgetting to move both model AND data to the same device

---

## 23. Useful Utilities

```python
# tqdm for progress bars
from tqdm import tqdm
for batch in tqdm(train_loader):
    ...

# TensorBoard for experiment tracking
from torch.utils.tensorboard import SummaryWriter
writer = SummaryWriter("runs/experiment1")
writer.add_scalar("Loss/train", loss.item(), epoch)
writer.close()
# View with: tensorboard --logdir=runs

# Weights & Biases (alternative experiment tracker)
import wandb
wandb.init(project="my-project")
wandb.log({"loss": loss.item()})
```

| Library | Purpose |
|---|---|
| `torchvision` | Datasets, models, and transforms for vision |
| `torchaudio` | Audio processing tools |
| `torchtext` | NLP datasets and utilities |
| `torchmetrics` | Standardized evaluation metrics |
| `pytorch-lightning` | High-level training framework (less boilerplate) |
| `transformers` (Hugging Face) | Pretrained transformer models |
| `timm` | Pretrained vision models |

---

## 24. Resources

- [Official PyTorch Documentation](https://pytorch.org/docs/stable/index.html)
- [PyTorch Tutorials](https://pytorch.org/tutorials/)
- [PyTorch Examples (GitHub)](https://github.com/pytorch/examples)
- [Hugging Face Transformers](https://huggingface.co/docs/transformers)
- [PyTorch Lightning](https://lightning.ai/docs/pytorch/stable/)

---

**Happy training! 🔥**
