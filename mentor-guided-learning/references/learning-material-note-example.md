# Chapter 3 Linear Regression - Study Notes

**Reference Notebooks:**
- [linear-regression.ipynb](linear-regression.ipynb) - Linear Regression
- [oo-design.ipynb](oo-design.ipynb) - Object-Oriented Design for Implementation
- [synthetic-regression-data.ipynb](synthetic-regression-data.ipynb) - Synthetic Regression Data
- [linear-regression-scratch.ipynb](linear-regression-scratch.ipynb) - Linear Regression from Scratch
- [linear-regression-concise.ipynb](linear-regression-concise.ipynb) - Concise Linear Regression Implementation
- [generalization.ipynb](generalization.ipynb) - Generalization
- [weight-decay.ipynb](weight-decay.ipynb) - Weight Decay

---
---

# Principles

---

## 1. Linear Regression <sub>([linear-regression.ipynb](linear-regression.ipynb))</sub>

### 1.1 What is the model actually doing?

Imagine you are a real-estate agent. Over the years, you have seen tons of transactions. Each record has property features (size, age, floor, etc.) and the final selling price. Now a new client asks: "How much is my 80-square-meter, 10-year-old apartment worth?"

How would you estimate it? From experience, you already know some rough patterns: larger homes usually cost more; older homes usually cost less. More precisely, you have a mental "model": **each feature contributes its own influence to price, and you add all those influences together, plus a baseline value.**

That is exactly what linear regression does. Its core assumption is: **the output can be written as a weighted sum of features plus a constant.** Why "linear"? Because the prediction changes linearly with each feature: if one feature increases a little, the prediction changes by a fixed amount (up or down).

Written as math:

$$\hat{y} = \mathbf{w}^\top \mathbf{x} + b = w_1 x_1 + w_2 x_2 + \cdots + w_d x_d + b \tag{1.1}$$

> $x_1, x_2, \ldots$ are input features (like size, house age), $w_1, w_2, \ldots$ are the corresponding weights (how important each feature is), and $b$ is the bias (baseline). In plain terms: **feature times weight, sum them up, add a baseline -> prediction $\hat{y}$.**

If we compute predictions for $n$ samples together, we can stack them into a matrix and do one matrix multiplication:

$$\hat{\mathbf{y}} = \mathbf{X}\mathbf{w} + b \tag{1.2}$$

> Stack the $n$ samples into an $n \times d$ matrix $\mathbf{X}$ (each row = one sample, each column = one feature). One matrix multiplication computes all predictions at once, no need for a for-loop.

This is an **affine transform** = linear transform (multiply by weights) + translation (add bias). Linear regression is a **regression** task: output is a continuous value (price, temperature), not a category label.

### 1.2 How do we measure prediction quality? Loss function

Now we have a model. How do we tell if it predicts well? We need a "scoring rule" that measures the gap between prediction and ground truth. That scoring rule is the **loss function**.

The most direct thought is prediction minus truth: larger difference means worse performance. But raw subtraction has a problem: positive and negative errors can cancel out (overestimate by 100k and underestimate by 100k sum to 0, but the model is clearly not good). The fix is **square the difference**: both overestimation and underestimation become positive, and bigger mistakes get punished more.

Loss for one sample:

$$l^{(i)}(\mathbf{w}, b) = \frac{1}{2}\left(\hat{y}^{(i)} - y^{(i)}\right)^2 \tag{1.3}$$

> $\hat{y}^{(i)}$ is the prediction for sample $i$, and $y^{(i)}$ is the true value. Their squared difference tells us "how wrong" this sample is. The $\frac{1}{2}$ is just for cleaner derivatives (a 2 appears during differentiation and cancels out).

Average loss over the full dataset, i.e. **MSE**:

$$L(\mathbf{w}, b) = \frac{1}{n}\sum_{i=1}^{n} l^{(i)} = \frac{1}{n}\sum_{i=1}^{n}\frac{1}{2}\left(\mathbf{w}^\top\mathbf{x}^{(i)} + b - y^{(i)}\right)^2 \tag{1.4}$$

> Add errors for all $n$ samples and divide by $n$ to get average error. This is the objective we minimize.

Training objective:

$$\mathbf{w}^*, b^* = \arg\min_{\mathbf{w},b} L(\mathbf{w}, b) \tag{1.5}$$

> $\arg\min$ means "the values of $\mathbf{w}$ and $b$ that make $L$ smallest". In other words, find the best weights and bias.

Why square error instead of absolute error or fourth power? Absolute error is not differentiable at zero, which complicates gradients. Fourth power over-penalizes large errors. Squared loss yields a bowl-shaped optimization landscape with a single minimum. Also, as we will see, MSE has a strict probabilistic justification under Gaussian noise.

### 1.3 One-shot solution: closed-form

Linear regression has a special bonus: the model is linear and the loss is squared, so the loss surface is bowl-shaped. The bottom of the bowl is the optimum. At that point, the slope is zero, i.e., derivatives of loss with respect to weights are all zero. Because everything is linear, we can solve this system directly:

$$\mathbf{w}^* = (\mathbf{X}^\top\mathbf{X})^{-1}\mathbf{X}^\top\mathbf{y} \tag{1.6}$$

> Set "derivative of loss w.r.t. $\mathbf{w}$ = 0" and solve directly. $\mathbf{X}^\top\mathbf{X}$ is a $d \times d$ square matrix; take its inverse and multiply by $\mathbf{X}^\top\mathbf{y}$. Meaning: **no iterative steps needed, directly compute the optimum.** But this only works for very specific models like linear regression.

In deep learning we almost never use this, because once models become slightly more complex (e.g., add a nonlinear activation), this closed-form solution disappears. So we need a general method: gradient descent.

### 1.4 Step-by-step solution: gradient descent (Mini-batch SGD)

#### Prerequisites: derivative, partial derivative, and gradient

Core intuition: imagine standing on a mountain blindfolded, trying to reach the valley. The only thing you can do is feel the local slope under your feet and move in the steepest downhill direction. Repeat with small steps.

"Feeling the slope" in math is taking derivatives.

**Derivative (single-variable)**

For a single-variable function $f(x)$, derivative describes: **if $x$ changes a tiny bit, how much does $f$ change?**

$$f'(x) = \frac{df}{dx} = \lim_{h \to 0} \frac{f(x+h) - f(x)}{h} \tag{1.7}$$

> $h$ is an infinitesimal increment. Numerator is change in function value, denominator is change in input. Their ratio is rate of change. Geometrically, derivative is the slope of the tangent line at that point.

Example: if $f(x)=x^2$, then $f'(x)=2x$. At $x=3$, slope is 6, meaning a tiny increase in $x$ causes about 6 times that increase in $f$.

**Partial derivative (multi-variable)**

Loss $L$ depends on many parameters $w_1, w_2, \ldots, w_d, b$. A partial derivative treats a multi-variable function as if only one variable changes at a time:

$$\frac{\partial L}{\partial w_1} = \lim_{h \to 0} \frac{L(w_1 + h, w_2, \ldots, b) - L(w_1, w_2, \ldots, b)}{h} \tag{1.8}$$

> Keep all other parameters fixed, and only see how fast $L$ changes with $w_1$. Symbol $\partial$ ("partial") reminds us this is one-variable-at-a-time inside a multi-variable function.

**Gradient = all partial derivatives packed into a vector**

$$\nabla_{\mathbf{w}} L = \left(\frac{\partial L}{\partial w_1}, \frac{\partial L}{\partial w_2}, \ldots, \frac{\partial L}{\partial w_d}\right) \tag{1.9}$$

> Gradient is a vector. Each component says "how fast loss changes along this parameter direction." **Gradient points to steepest increase**, so if we move in the **opposite direction**, loss goes down. That is the core idea of gradient descent.

**Why partial derivatives matter so much in deep learning**

Deep learning models may have thousands, millions, or even billions of parameters. Training is essentially: compute partial derivatives for each parameter (which direction and how much to adjust), then update them together. `backward()` in PyTorch computes these derivatives automatically.

> **Fun fact: autograd is doing real differentiation, not a rough limit trick**
>
> The mathematical definition of derivative uses limits. But from this definition, mathematicians derived analytical differentiation rules long ago ($x^2 \to 2x$, chain rule, etc.). PyTorch autograd implements these analytical rules in code: it records operations during forward pass (multiply, add, ReLU...) and applies local derivative rules in backward pass, chaining them with the chain rule. This is precise symbolic/automatic differentiation, not approximation.
>
> Then what is `numerical_lim` in d2l (decreasing $h$)? That is **numerical differentiation**: finite-difference approximation from the limit definition. It is useful for gradient checking (verification), not for real training. If you used numerical differentiation to train a model with 1M parameters, you'd need about 1M forward passes per step and suffer from floating-point error. Autograd gets all gradients in one backward pass.
>
> | Method | How it works | Accuracy | Speed | Real use |
> |---|---|---|---|---|
> | Numerical differentiation | Use small $h$, compute $\frac{f(x+h)-f(x)}{h}$ | Approximate | Very slow (one forward per parameter) | Gradient check only |
> | Automatic differentiation (autograd) | Record graph + analytical local rules + chain rule | Exact (up to floating-point arithmetic) | Fast (all params in one backward) | **Actual training** |

#### SGD update rule

If we use all training data at every step to compute full gradients, training can be very slow on large datasets. The standard fix is **mini-batch stochastic gradient descent** (Mini-batch SGD): randomly sample a small batch each step, estimate gradient from that batch, and update parameters with this estimate.

Why does this work? Because random batch averages are unbiased estimators of full-data averages. Each step may be noisy, but on average the direction is right.

$$(\mathbf{w}, b) \leftarrow (\mathbf{w}, b) - \frac{\eta}{|\mathcal{B}|}\sum_{i \in \mathcal{B}} \partial_{(\mathbf{w},b)} l^{(i)}(\mathbf{w}, b) \tag{1.10}$$

> $\mathcal{B}$ is the sampled mini-batch (e.g., 256 samples), $|\mathcal{B}|$ is batch size, and $\eta$ is learning rate (step size). $l^{(i)}$ is per-sample loss (Eq. 1.3). $\partial_{(\mathbf{w},b)} l^{(i)}$ is gradient of that sample's loss w.r.t. parameters (related to Eq. 1.8). $\leftarrow$ means overwrite with updated value. In plain terms: **compute average gradient of the batch, then take a small step opposite to gradient direction.**

Expanded form:

$$\mathbf{w} \leftarrow \mathbf{w} - \frac{\eta}{|\mathcal{B}|}\sum_{i \in \mathcal{B}} \mathbf{x}^{(i)}\left(\mathbf{w}^\top\mathbf{x}^{(i)} + b - y^{(i)}\right) \tag{1.11}$$

$$b \leftarrow b - \frac{\eta}{|\mathcal{B}|}\sum_{i \in \mathcal{B}}\left(\mathbf{w}^\top\mathbf{x}^{(i)} + b - y^{(i)}\right) \tag{1.12}$$

> The term in parentheses, $\mathbf{w}^\top\mathbf{x}^{(i)} + b - y^{(i)}$, is "prediction - ground truth" (the residual). For $\mathbf{w}$, derivative includes an extra $\mathbf{x}^{(i)}$ from the chain rule. For $b$, derivative is just the residual itself. Learning rate $\eta$ controls how far each update moves.

Key hyperparameters:

| Hyperparameter | Meaning | If too large | If too small |
|--------|------|-------------|-------------|
| $\eta$ (learning rate) | step size per update | oscillation/divergence | very slow convergence |
| $|\mathcal{B}|$ (batch size) | samples per update | slower updates (but cleaner gradients) | noisy/unstable gradients |
| epochs | full passes over dataset | overfitting risk | undertrained |

### 1.5 Why MSE? A probabilistic view

At first glance, choosing squared error might feel arbitrary. But it has a strict probabilistic foundation.

Real-world data is noisy. Even if the model form is correct, observed targets can differ due to measurement noise, missing factors, etc. A very common assumption is Gaussian noise:

$$y = \mathbf{w}^\top\mathbf{x} + b + \epsilon, \quad \epsilon \sim \mathcal{N}(0, \sigma^2) \tag{1.13}$$

> $\epsilon$ is random noise with mean 0 and variance $\sigma^2$. Most noise is small, occasionally larger, and positive/negative noise are symmetric. Ground truth = linear signal + random disturbance.

Under this assumption, "finding best weights" is equivalent to "finding weights that maximize probability of observed data" - i.e., maximum likelihood estimation.

Probability of observing $y$ given $\mathbf{x}$:

$$P(y|\mathbf{x}) = \frac{1}{\sqrt{2\pi\sigma^2}}\exp\left(-\frac{(y - \mathbf{w}^\top\mathbf{x} - b)^2}{2\sigma^2}\right) \tag{1.14}$$

> This is the Gaussian density centered at prediction $\mathbf{w}^\top\mathbf{x} + b$. Closer prediction means higher probability.

Negative log-likelihood over dataset:

$$-\log P(\mathbf{y}|\mathbf{X}) = \sum_{i=1}^{n} \frac{1}{2\sigma^2}\left(y^{(i)} - \mathbf{w}^\top\mathbf{x}^{(i)} - b\right)^2 + \text{constant} \tag{1.15}$$

> Taking log turns products into sums. Adding minus turns maximization into minimization. Up to constants, this is exactly the MSE form.

> **Conclusion: minimizing MSE is equivalent to maximum likelihood under Gaussian noise.** MSE is not an arbitrary pick. If noise distribution is very non-Gaussian (e.g., many extreme outliers), other losses may be better.

### 1.6 Vectorization: why not loops?

Suppose you have 1,000 samples and 100 features. A natural implementation is nested loops. But vectorization (one matrix multiply for all predictions) can be **~400x faster** because matrix ops use optimized BLAS/GPU parallelization, while loops are mostly serial.

This is a key deep learning practice: **if matrix ops can do it, avoid explicit loops.**

<img src="img/linear_regression_flow.png" alt="Linear regression flow" style="max-width:640px; max-height:600px;">

---

## 2. Generalization <sub>([generalization.ipynb](generalization.ipynb))</sub>

### 2.1 Training error vs generalization error

Doing well on training data does not guarantee good performance on unseen data. Think of a student who memorizes every old exam question perfectly but cannot solve new questions in the real exam. That's **overfitting**: memorizing details (including noise) instead of learning true patterns.

**Training error (empirical risk):** average loss on training set:

$$R_{\text{emp}}[f] = \frac{1}{n}\sum_{i=1}^{n} l\left(f(\mathbf{x}^{(i)}),\ y^{(i)}\right) \tag{2.1}$$

> This value is directly computable.

**Generalization error (population risk):** expected loss over true data distribution:

$$R[f] = E_{(\mathbf{x}, y) \sim P}\left[l\left(f(\mathbf{x}),\ y\right)\right] \tag{2.2}$$

> This is the real target, but we cannot compute it exactly; we estimate it with validation/test sets.

The gap between them is the **generalization gap**. A key assumption is IID (independent and identically distributed): train and test data come from the same distribution. If IID breaks (e.g., train on summer data, test on winter data), low training error is not enough.

### 2.2 Model complexity: polynomial fitting example

More complex models can overfit more easily. But overly simple models can **underfit** and fail to capture basic structure. A classic example is polynomial regression:

$$\hat{y} = \sum_{i=0}^{d} w_i x^i \tag{2.3}$$

> $d=1$ is a line, $d=2$ is a parabola, larger $d$ means higher complexity. A line may underfit; a high-degree polynomial can pass through every training point but oscillate wildly between points (overfit). A proper degree captures trend without memorizing noise. If $d \geq n$ (degree not smaller than sample count), perfect interpolation is possible - but that's often just memorization.

### 2.3 Diagnosing underfitting and overfitting

| Metric | Underfitting | Good fit | Overfitting |
|------|--------|------|--------|
| Training error | High | Moderate | Very low |
| Validation error | High | Moderate | High |
| Generalization gap | Small | Small | Large |
| Treatment | Increase model complexity | Keep | Regularization / more data |

Interesting fact: deep models often have far more parameters than training samples (which should overfit in classic theory), yet they still generalize well in practice. This remains an active research question.

### 2.4 Model selection and cross-validation

**Three-way split:** train / validation / test. Use train to fit parameters, validation to tune hyperparameters and architecture, and test only once at the very end for final report.

Why not tune on test set? Because once test-set performance influences your design, test data is no longer truly unseen.

**K-fold cross-validation:** when data is small, split into $K$ folds. Rotate one fold as validation, others as training, repeat $K$ times, then average. Each sample serves as validation exactly once.

---

## 3. Weight Decay <sub>([weight-decay.ipynb](weight-decay.ipynb))</sub>

### 3.1 Why regularization?

Complex models overfit. One option is reducing parameter count, but in deep learning this is often not practical enough. Another strategy: **keep parameter count, but constrain parameter magnitude.**

Intuition: if weights are tiny and close to zero, the model behaves like a simpler function. Larger weights can make output react too sharply to tiny input changes.

So we add a penalty term to the original loss: **sum of squared weights**.

$$L_{\text{reg}}(\mathbf{w}, b) = L(\mathbf{w}, b) + \frac{\lambda}{2}\|\mathbf{w}\|^2 \tag{3.1}$$

> $\|\mathbf{w}\|^2 = \sum_j w_j^2$ is squared L2 norm. $\lambda \ge 0$ is regularization strength: $\lambda=0$ means no regularization; larger $\lambda$ shrinks weights more. The $\frac{1}{2}$ is for cleaner differentiation. Usually **bias $b$ is not regularized** since it shifts outputs globally but does not control sensitivity in the same way as weights.

### 3.2 SGD update with weight decay

After differentiating Eq. (3.1), the weight update becomes:

$$\mathbf{w} \leftarrow (1 - \eta\lambda)\,\mathbf{w} - \frac{\eta}{|\mathcal{B}|}\sum_{i \in \mathcal{B}} \mathbf{x}^{(i)}\left(\mathbf{w}^\top\mathbf{x}^{(i)} + b - y^{(i)}\right) \tag{3.2}$$

> Compared with Eq. (1.11), there is an extra factor $(1-\eta\lambda)$ multiplying $\mathbf{w}$. Since $\eta\lambda>0$, this factor is smaller than 1, so weights shrink a little at every step - hence the name "weight decay".

Effect: training error may increase (stronger constraints), but validation error often decreases (better generalization). This is the regularization tradeoff: sacrifice some training fit to improve unseen-data performance.

### 3.3 L2 vs L1 regularization

$$\text{L2 penalty:}\quad \frac{\lambda}{2}\|\mathbf{w}\|^2 = \frac{\lambda}{2}\sum_j w_j^2 \tag{3.3}$$

$$\text{L1 penalty:}\quad \lambda\|\mathbf{w}\|_1 = \lambda\sum_j |w_j| \tag{3.4}$$

| | L2 (Ridge) | L1 (Lasso) |
|---|---|---|
| Penalty | $\sum w_j^2$ | $\sum |w_j|$ |
| Gradient | $2w_j$ (smooth) | $\text{sign}(w_j)$ (non-differentiable at 0) |
| Effect | uniform shrinkage | sparse weights (many exactly zero) |
| Geometric intuition | constraint is a "ball" | constraint is a "diamond" |
| Typical use | default in deep learning | feature selection |

### 3.4 Probabilistic view

L2 regularization corresponds to MAP estimation under a **Gaussian prior** $\mathbf{w} \sim \mathcal{N}(0, 1/\lambda)$. L1 corresponds to a **Laplace prior**.

---

## Key Concept Connection Table

| Dimension | Linear Regression |
|------|---------|
| **Model** | $\hat{y} = \mathbf{w}^\top\mathbf{x} + b$ |
| **Loss** | MSE: $\frac{1}{2}(\hat{y} - y)^2$ |
| **Probabilistic view** | Gaussian noise assumption -> MLE = minimizing MSE |
| **Closed-form** | $\mathbf{w}^* = (\mathbf{X}^\top\mathbf{X})^{-1}\mathbf{X}^\top\mathbf{y}$ |
| **Optimization** | Mini-batch SGD |
| **Gradient** | $\hat{y} - y$ (times feature vector) |
| **Regularization** | L2 weight decay: $(1-\eta\lambda)\mathbf{w}$ |
| **Generalization** | Train/val/test split + cross-validation |

---

### Numeric Walkthrough: from data to gradient

Let's use the simplest case: 2 features (size $x_1$, age $x_2$), 1 sample.

**Given:**
- Input feature: $\mathbf{x} = (80, 10)$ (80 m^2, 10 years old)
- True price: $y = 300$ (in ten-thousands)
- Current weight: $\mathbf{w} = (1.5, -0.5)$, bias $b = 100$
- Learning rate: $\eta = 0.001$

**Step 1: Forward pass (1.1)**

$$\hat{y} = 1.5 \times 80 + (-0.5) \times 10 + 100 = 120 - 5 + 100 = 215$$

**Step 2: Compute loss (1.3)**

$$l = \frac{1}{2}(\hat{y} - y)^2 = \frac{1}{2}(215 - 300)^2 = \frac{1}{2} \times 7225 = 3612.5$$

> Predicted 215 vs true 300: error is 85. Loss 3612.5 is large, so current parameters are poor.

**Step 3: Compute gradients (1.11, 1.12)**

Residual: $\hat{y} - y = 215 - 300 = -85$

$$\frac{\partial l}{\partial w_1} = x_1 \cdot (\hat{y} - y) = 80 \times (-85) = -6800$$

$$\frac{\partial l}{\partial w_2} = x_2 \cdot (\hat{y} - y) = 10 \times (-85) = -850$$

$$\frac{\partial l}{\partial b} = \hat{y} - y = -85$$

> Gradients are negative, so under gradient descent subtraction, these parameters will increase.

**Step 4: Update parameters (1.10)**

$$w_1 \leftarrow 1.5 - 0.001 \times (-6800) = 1.5 + 6.8 = 8.3$$

$$w_2 \leftarrow -0.5 - 0.001 \times (-850) = -0.5 + 0.85 = 0.35$$

$$b \leftarrow 100 - 0.001 \times (-85) = 100 + 0.085 = 100.085$$

> Updated prediction becomes $8.3 \times 80 + 0.35 \times 10 + 100.085 = 767.6$, which overshoots heavily. This means the learning rate is too large for this single-sample update. In practice, mini-batch averaging and careful learning-rate tuning stabilize training.

**Next chapter preview:** In this chapter, we learned linear regression, the simplest prediction model. Next, we extend similar ideas to classification: instead of predicting one continuous number, we predict which class an input belongs to. That brings us to Softmax regression.

---
---

# Part III Code Implementation

---

## 1. Object-Oriented Design <sub>([oo-design.ipynb](oo-design.ipynb))</sub>

The book defines three core classes used throughout later chapters:

| Class | Responsibility | Key methods |
|---|---|---|
| `Module` | model (network structure + training/validation steps) | `forward`, `loss`, `training_step`, `configure_optimizers` |
| `DataModule` | data (loading, preprocessing, batching) | `train_dataloader`, `val_dataloader` |
| `Trainer` | orchestrate training loop | `fit(model, data)`, `fit_epoch` |

### 1.1 Core design patterns

```python
# 1. add_to_class decorator: add methods to a class outside class definition
@d2l.add_to_class(A)
def do(self):
    print('Class A', self.x)

# 2. HyperParameters base class: auto-save __init__ args as attributes
class MyClass(d2l.HyperParameters):
    def __init__(self, a, b):
        self.save_hyperparameters()  # creates self.a = a, self.b = b automatically
```

### 1.2 Trainer loop (shared by all models)

```python
@d2l.add_to_class(d2l.Trainer)
def fit_epoch(self):
    self.model.train()
    for batch in self.train_dataloader:
        loss = self.model.training_step(self.prepare_batch(batch))
        self.optim.zero_grad()
        with torch.no_grad():
            loss.backward()
            self.optim.step()
        self.train_batch_idx += 1
    if self.val_dataloader is None:
        return
    self.model.eval()
    for batch in self.val_dataloader:
        with torch.no_grad():
            self.model.validation_step(self.prepare_batch(batch))
        self.val_batch_idx += 1
```

> This loop structure is almost the same from linear regression to Transformers. What changes is the model and loss, not the orchestration skeleton.

---

## 2. Synthetic Regression Data <sub>([synthetic-regression-data.ipynb](synthetic-regression-data.ipynb))</sub>

```python
class SyntheticRegressionData(d2l.DataModule):
    def __init__(self, w, b, noise=0.01, num_train=1000, num_val=1000, batch_size=32):
        super().__init__()
        self.save_hyperparameters()
        n = num_train + num_val
        self.X = torch.randn(n, len(w))           # random features from standard normal
        noise = torch.randn(n, 1) * noise
        self.y = torch.matmul(self.X, w.reshape((-1, 1))) + b + noise
```

> **Key point:** true parameters ($\mathbf{w}$ and $b$) are known because we define them. So after training we can verify whether the model learned correctly. Noise std = 0.01 is small, so learned parameters should be close to ground truth.

### DataLoader: manual vs framework

```python
# Manual version (teaching purpose)
@d2l.add_to_class(SyntheticRegressionData)
def get_dataloader(self, train):
    i = slice(0, self.num_train) if train else slice(self.num_train, None)
    return self.get_tensorloader((self.X, self.y), train, i)

# Framework version (practical): internally uses TensorDataset + DataLoader,
# automatically handles shuffling, batching, and prefetch workers
```

---

## 3. Linear Regression from Scratch <sub>([linear-regression-scratch.ipynb](linear-regression-scratch.ipynb))</sub>

### 3.1 Model and loss

```python
class LinearRegressionScratch(d2l.Module):
    def __init__(self, num_inputs, lr, sigma=0.01):
        super().__init__()
        self.save_hyperparameters()
        self.w = torch.normal(0, sigma, size=(num_inputs, 1), requires_grad=True)
        self.b = torch.zeros(1, requires_grad=True)

    def forward(self, X):
        return torch.matmul(X, self.w) + self.b  # Eq. (1.2)

    def loss(self, y_hat, y):
        l = (y_hat - y) ** 2 / 2                 # Eq. (1.3)
        return l.mean()
```

### 3.2 Manual SGD

```python
class SGD(d2l.HyperParameters):
    def __init__(self, params, lr):
        self.save_hyperparameters()

    def step(self):
        for param in self.params:
            param -= self.lr * param.grad         # Eq. (1.10)

    def zero_grad(self):
        for param in self.params:
            if param.grad is not None:
                param.grad.zero_()
```

### 3.3 Training

```python
model = LinearRegressionScratch(2, lr=0.03)
data = SyntheticRegressionData(w=torch.tensor([2, -3.4]), b=4.2)
trainer = d2l.Trainer(max_epochs=3)
trainer.fit(model, data)
```

### Notes and caveats

- **Weight initialization:** use small random values (std 0.01), not all zeros. In symmetric networks, zero init can make neurons learn the same thing.
- **`requires_grad=True`:** tells PyTorch to track operations on this tensor so `backward()` can compute gradients.
- **`l.mean().backward()`:** loss must be scalar for backward, so average first.
- **`grad.zero_()`:** must clear gradients after each update; otherwise PyTorch accumulates them.

---

## 4. Concise Linear Regression <sub>([linear-regression-concise.ipynb](linear-regression-concise.ipynb))</sub>

### Scratch vs framework API

| Component | Scratch | PyTorch API |
| -------------- | ---------------------------- | ------------------------------------------ |
| Model | `torch.matmul(X, w) + b` | `nn.LazyLinear(1)` |
| Loss | manually `(y_hat - y)**2 / 2` | `nn.MSELoss()` |
| Optimizer | manual SGD | `torch.optim.SGD(net.parameters(), lr=...)` |
| Parameter init | `torch.normal(...)` | `net.weight.data.normal_(0, 0.01)` |

### Full code

```python
class LinearRegression(d2l.Module):
    def __init__(self, lr):
        super().__init__()
        self.save_hyperparameters()
        self.net = nn.LazyLinear(1)                # infer input dim at first forward
        self.net.weight.data.normal_(0, 0.01)
        self.net.bias.data.fill_(0)

    def forward(self, X):
        return self.net(X)

    def loss(self, y_hat, y):
        fn = nn.MSELoss()
        return fn(y_hat, y)

    def configure_optimizers(self):
        return torch.optim.SGD(self.parameters(), self.lr)
```

### Key APIs

- **`nn.LazyLinear(out)`**: delayed initialization for fully connected layer; input dimension is inferred at first forward pass.
- **`nn.MSELoss()`**: returns mean loss (without explicit $\frac{1}{2}$; optimization behavior remains equivalent).
- **`self.parameters()`**: iterator of all learnable parameters.
- Methods ending with **`_`** (`normal_`, `fill_`, `zero_`): in-place operations, no new tensor created.

---

## 5. Weight Decay Implementation <sub>([weight-decay.ipynb](weight-decay.ipynb))</sub>

### 5.1 Experiment setup

```python
# High-dimensional linear regression: 200 features, only 20 training samples -> severe overfitting
# True parameters: w_i = 0.01, b = 0.05
data = d2l.SyntheticRegressionData(
    w=torch.ones((200, 1)) * 0.01, b=0.05,
    num_train=20, num_val=100)
```

### 5.2 Scratch implementation

```python
def l2_penalty(w):
    return (w ** 2).sum() / 2

class WeightDecayScratch(d2l.LinearRegressionScratch):
    def __init__(self, num_inputs, lambd, lr, sigma=0.01):
        super().__init__(num_inputs, lr, sigma)
        self.save_hyperparameters()

    def loss(self, y_hat, y):
        return (super().loss(y_hat, y) +       # original MSE
                self.lambd * l2_penalty(self.w)) # + lambda * L2 penalty
```

### 5.3 Framework implementation

```python
class WeightDecay(d2l.LinearRegression):
    def __init__(self, wd, lr):
        super().__init__(lr)
        self.save_hyperparameters()
        self.wd = wd

    def configure_optimizers(self):
        return torch.optim.SGD([
            {'params': self.net.weight, 'weight_decay': self.wd},  # decay on weights
            {'params': self.net.bias}],                            # no decay on bias
            lr=self.lr)
```

### 5.4 Results

| | No regularization ($\lambda = 0$) | With regularization ($\lambda = 3$) |
|---|---|---|
| Training error | near 0 (memorizes train set) | higher (constrained) |
| Validation error | very high (overfit) | lower (better generalization) |
| Weight L2 norm | larger | much smaller |

### Common pitfalls

1. **Do not regularize bias**: in frameworks, use parameter groups for `weight_decay`.
2. **Pass `weight_decay` directly to optimizer**: no need to manually add penalty inside loss when using optimizer support.
3. **Tune $\lambda$**: too small has little effect; too large underfits. Pick by validation set.

<img src="img/training_loop.png" alt="Training loop" style="max-width:640px; max-height:800px;">
