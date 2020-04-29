### Generate The Tensor

```python
tf.constant(value, dtype=tf.int8)
tf.convert_to_tensor(numpy, dtype=tf.int8)

tf.zeros(dim)
tf.ones(dim)

tf.fill(dim, val)

1 dim: 3
2 dim: [3,5]
3 dim: [3, 5, 8]
    
tf.random.normal(dim, mean=, stddev=) #Generate the Gaussian distribution
tf.random.truncated_normal(dim, mean=, stddev=) #Truncated normal distribution, guarantee all data distribute in 2 stddev.
```

### Common Function

```python
tf.cast(tensor, dtype=) #cast tensor
tf.reduce_min(tensor) #calculate the min value in the tensor
tf.reduce_max(tensor) #calculate the max value in the tensor

tf.reduce_mean(tensor, axis=) #axis=0 means along the row, axis = 1 means along the colom. If not assign the axis, operate all the elements.

tf.Variable() # Mark the tensor as a trainable variable, the variable will records the gradient info in the back propagation.
tf.Variable(tf.random.normal([m,n], mean=0, stddev=1)) 

tf.add(A, B)
tf.subtract(A, B)
tf.multiply(A, B)
tf.divide(A, B)
tf.pow(A, B)

#SP: The operation in A & B will broadcast like numpy
#SP: The dtype between A & B must be same

tf.square(A)
tf.sqrt(A) # SP: The dtype in A must be a float

tf.matmul(A, B)

tf.data.Dataset.from_tensor_slices((features, labels)) # match the features and labels, features and labels should be a tense of the same length, The type in tensor should also be same

# features = tf.constant([1, 2, 3, 4])
# labels = tf.constant([4, 3, 2, 1])
# dataset = tf.data.Dataset.from_tensor_slices((features, labels))
# for element in dataset:
#     print(element) # the element is a tuple, which has features and it's corresponding labels


with tf.GradientTape() as tape:
    grad = tape.gradient(function, w) # Calculate the gradient of the function against the variable.
    #PS: the dtype of function and w must be float
    
    
for index, element in enumerate(list):
    print(i, element) # mapping the index and the element in list

tf.one_hot(data, depth=) #use one_hot code to return the result, the depth indicate how many class you want to show.

# tf.assign(var, cons) # assign the var the constant value
# tf.assign_add(var, cons)
# tf.assign_sub(var, cons) 
# The method below only adapt to TF1

tf.Variable().assign(num/tensor) # assign the value
tf.Variable().assign_sub(num/tensor) # Self - subtracting a variable
tf.Variable().assign_add(num/tensor) # Self - adding a variable


tf.argmax(tensor, axis=) # return the max value index along the axis 


tf.where(conditional statement, A, B) # return A if conditional statement is true else B

np.random.RandomState.rand(dim) #if dim is null, return a scalar, a random value between 0 - 1


```

### Random Value

```python
np.random.normal(mean, stddev, size) #return a normal distribution matrix
np.random.rand(size) #return a union distribution matrix between 0 - 1
np.random.RandomState(seed=1)
```

### Array Merging

```python
np.vstack((A, B) # merge A and B vertically
np.hstack((A, B)) # merge A and B horizontally
```

### Grid Generator

```python
x, y = np.mgrid[start : end : step, start : end : step] # generate the grid of two variable
x.ravel()  # make the x as a 1-D array
np.c_[x.ravel(), y.ravel()] # match x and y
```

### Activating Function

```python
tf.nn.sigmoid(x) # Sigmoid function
tf.nn.tanh(x) # Tanh function
tf.nn.relu(x) # Relu function
tf.nn.leaky_relu(x) # Leaky_relu funtion
```

### Loss Function

```python
tf.losses.categorical_crossentropy(y_, y)
```

$$
H(y_, y) = - \sum y\_ * lny
$$

```python
# y_ should be the one-hot code
# y should indicate the possibility of all outcomes
# y will be normalized if the some of the possibility larger than 1

tf.nn.softmax_cross_entropy_with_logits(y_, y) # Softmax and cross entropy were calculated simultaneously

tf.nn.l2() #use l2 regularization
```

### Optimizer

$$
g_t = \nabla loss = \frac{\partial loss}{\partial (w_t)}
$$

$$
\text{The first-order momentum:}m_t,\\\text{The second-order momentum:}V_t
$$

$$
\eta_t = lr  \frac{m_t}{\sqrt{V_t}}
$$

$$
w_{t+1} = w_t - \eta_t = w_t - lr * \frac{m_t}{\sqrt{V_t}}
$$

- SGD - Stochastic gradient descent
  $$
  w_{t+1} = w_t - lr  \frac{\partial loss}{\partial w_t}
  $$

- SGDM - Stochastic gradient descent With Momentum

$$
m_t = \beta m_{t-1} + (1 - \beta) g_t, V_t = 1\\
n_t = lr * \frac{m_t}{\sqrt{V_t}} = lr * m_t\\
w_{t+1} = w_t - lr  (\beta m_{t-1} + (1 - \beta) g_t)
$$

- Adagrad
  $$
  m_t = g_t, V_t = V_{t-1} + g_t^2 = \sum_{\tau = 1}^t g_\tau^2\\
  w_{t+1} = w_t - \eta_t = w_t - lr  \frac{g_t}{\sqrt{\sum_{\tau = 1}^t g_\tau^2}}
  $$

- RMSProp
  $$
  m_t = g_t, V_t = \beta V_{t-1} + (1-\beta) g_t^2\\
  w_{t+1} = w_t - lr  \frac{g_t}{\sqrt{\beta V_{t-1} + (1-\beta)g_t^2}}
  $$

- Adam
  $$
  m_t = \beta_1 m_{t-1} + (1-\beta_1)g_t\\
  \hat m_t = \frac{m_t}{1-\beta_1^t}\\
  V_t = \beta_2 V_{step-1} + (1 - \beta_2) g_t^2\\
  \hat V_t = \frac{V_t}{1-\beta_2^t}\\
  n_t = lr\frac{\hat m_t}{\sqrt{\hat V_t}}\\
  w_{t+1} = w_t - lr\frac{\hat m_t}{\sqrt{\hat V_t}}
  $$
  