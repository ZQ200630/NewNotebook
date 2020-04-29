### FlowChart

- import
- train， test
- model = tf.keras.models.Sequential
- model.compile
- model.fit
- model.summary



### Model Building

```python
model = tf.keras.models.Sequential([struct of net])
1. tf.keras.layers.Flatten() #make a n-D tensor to 1-D vector
2. tf.keras.layers.Dense(num, activation="function", kernel_regularizer="regular")
# activation: relu, softmax, sigmoid, tanh， softsign, softplus, selu, hard_sigmoid, linear
# kernel_regularizer: tf.keras.regularizers.l1(), tf.keras.regularizers.l2(), keras.regularizers.l1_l2(l1=, l2=)
3. tf.keras.layers.Conv2D(filters = kernelNum, kernel_size = size, strides = step, padding = "valid" or "same") #the Convolutional layer
4. tf.keras.layers.LSTM() #LSTM RNN layer
```

### Model Compile

```python
model.compile(optimizer = , loss = , metrics = [""])
# optimizer: sgd, adagrad, adadelta, adam
# loss: mse, sparse_categorical_crossentropy
# metrics: accuracy, categorical_accuracy, sparse_categorical_accuracy
```

### Model Fit

```python
model.fit(x, y, batch_size= , epochs= , 
         validation_data=(测试集输入特征，测试集标签),
         validation_split=从训练集划分多少比例给测试集
         validation_freq=多少次epoch测试一次)
```

### Model Summary

```python
model.summary()
```

### Model Class

```python
class MyModel(Model):
    def __init__(self):
        super(Mymodel, self).__init__()
    def call(self, x):
        return y
    
model = MyModel()
# __init__() 定义所需网络结构块
# call() 写出前向传播

class IrisModel(Model):
    def __init__(self):
        super(IrisModel, self).__init__()
        self.d1 = Dense(3)
    def call(self, x):
        y = self.d1(x)
        return y
model = IrisModel()
```

### Data Processing

```python
image_gen_train = tf.keras.preprocessing.images.ImageDataGenerator(
	rescale=
    rotation_range=
    width_shift_range=
    height_shift_range=
    horizontal_flip=
    zoom_range=
)

# x_train = x_train.reshape(x_train.shape[0],28,28,1)
image_gen_train.fit(x_train)
model.fit(image_gen_train.flow(x_train, y_train, batch_size=32))
```

### Read Model Weight

```python
load_weights(path)

checkpoint_save_path = "***.ckpt"
if os.path.exists(checkpoint_save_path + '.index'):
    print('-------------load the model-----------------')
    model.load_weights(checkpoint_save_path)
```

### Save Model Weight

```python
cp_callback = tf.keras.callbacks.ModelCheckpoint(
	filepath = path,
    save_weights_only=True/False,
    save_best_only=True/False)
history = model.fit(callbacks=[cp_callback])
#PS: if the save_best_only be true, you should add valid_data
```

### Print Weight

```python
np.set_printoptions(threshold=np.inf) # Do not use ... show the middle data
model.trainable_variables # Return the trainable param in model

np.set_printoptions(threshold=np.inf)
print(model.trainable_variables)
file=open('./weights.txt', 'w')
for v in model.trainable_variables:
    file.write(str(v.name) + '\n')
    file.write(str(v.shape) + '\n')
    file.write(str(v.numpy()) + '\n')
file.close()
```

### Accuracy and Loss

```python
acc = history.history['sparse_categorical_accuracy']
val_acc = history.history['val_sparse_categorical_accuracy']
loss = history.history['loss']
val_loss = history.history['val_loss']
```

### Predict

```python
x_predict = image_arr[tf.newaxis, ...]
```

### Convolution Network

```python
padding='valid' # 不使用全零填充
padding='same' # 使用全零填充
Conv2D(filters=6, 5, padding='valid', activation='sigmoid'， strides=1)
#kernel_size: 如果宽高相等可以只写一个值，否则使用元祖
#strides 滑动步长，默认为1
```

### Batch Normaliztation

```python
tf.keras.layers.BatchNormalization()

model = tf.keras.models.Sequential([
    Conv2D(),
    BatchNormalization(),
    Activation('relu'),
    MaxPool2D(),
    Dropout(0.2)
])
```

### Pooling

```python
tf.keras.layers.MaxPool2D(poolsize=, strides=整数或元祖, padding=)
tf.keras.layers.AveragePooling2D(poolsize=, strides=, padding=)
```

### Dropout

```python
tf.keras.layers.Dropout(proportion)
```

### DataSet

- MNIST

- FASHION

- CIFAR10

  ```python
  tf.keras.datasets.*
  (x_train, y_train),(x_test, y_test) = *.load_data()
  ```


### LeNet

```python
model = Sequential()
model.add(Conv2D(input_shape=(28, 28, 1), kernel_size=(5, 5), filters=20, activation='relu'))
model.add(MaxPooling2D(pool_size=(2,2), strides=2, padding='same'))

model.add(Conv2D(kernel_size=(5, 5), filters=50,  activation='relu', padding='same'))
model.add(MaxPooling2D(pool_size=(2,2), strides=2, padding='same'))

model.add(Flatten())
model.add(Dense(500, activation='relu'))
model.add(Dense(10, activation='softmax'))
model.compile(optimizer='rmsprop', loss='categorical_crossentropy', metrics=['accuracy'])
```

### AlexNet

```python
# 创建模型序列
model = Sequential()
#第一层卷积网络，使用96个卷积核，大小为11x11步长为4， 要求输入的图片为227x227， 3个通道，不加边，激活函数使用relu
model.add(Conv2D(96, (11, 11), strides=(1, 1), input_shape=(28, 28, 1), padding='same', activation='relu',
                 kernel_initializer='uniform'))
# 池化层
model.add(MaxPooling2D(pool_size=(3, 3), strides=(2, 2)))
# 第二层加边使用256个5x5的卷积核，加边，激活函数为relu
model.add(Conv2D(256, (5, 5), strides=(1, 1), padding='same', activation='relu', kernel_initializer='uniform'))
#使用池化层，步长为2
model.add(MaxPooling2D(pool_size=(3, 3), strides=(2, 2)))
# 第三层卷积，大小为3x3的卷积核使用384个
model.add(Conv2D(384, (3, 3), strides=(1, 1), padding='same', activation='relu', kernel_initializer='uniform'))
# 第四层卷积,同第三层
model.add(Conv2D(384, (3, 3), strides=(1, 1), padding='same', activation='relu', kernel_initializer='uniform'))
# 第五层卷积使用的卷积核为256个，其他同上
model.add(Conv2D(256, (3, 3), strides=(1, 1), padding='same', activation='relu', kernel_initializer='uniform'))
model.add(MaxPooling2D(pool_size=(3, 3), strides=(2, 2)))

model.add(Flatten())
model.add(Dense(4096, activation='relu'))
model.add(Dropout(0.5))
model.add(Dense(4096, activation='relu'))
model.add(Dropout(0.5))
model.add(Dense(10, activation='softmax'))
model.compile(loss='categorical_crossentropy', optimizer='sgd', metrics=['accuracy'])
model.summary()
```

### VGGNet

```python
model = Sequential()
model.add(Conv2D(64, (3, 3), strides=(1, 1), input_shape=(32, 32, 3), padding='same', activation='relu',
                 kernel_initializer='uniform'))
model.add(Conv2D(64, (3, 3), strides=(1, 1), padding='same', activation='relu', kernel_initializer='uniform'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Conv2D(128, (3, 2), strides=(1, 1), padding='same', activation='relu', kernel_initializer='uniform'))
model.add(Conv2D(128, (3, 3), strides=(1, 1), padding='same', activation='relu', kernel_initializer='uniform'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Conv2D(256, (3, 3), strides=(1, 1), padding='same', activation='relu', kernel_initializer='uniform'))
model.add(Conv2D(256, (3, 3), strides=(1, 1), padding='same', activation='relu', kernel_initializer='uniform'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Conv2D(512, (3, 3), strides=(1, 1), padding='same', activation='relu', kernel_initializer='uniform'))
model.add(Conv2D(512, (3, 3), strides=(1, 1), padding='same', activation='relu', kernel_initializer='uniform'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Conv2D(512, (3, 3), strides=(1, 1), padding='same', activation='relu', kernel_initializer='uniform'))
model.add(Conv2D(512, (3, 3), strides=(1, 1), padding='same', activation='relu', kernel_initializer='uniform'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Flatten())
model.add(Dense(4096, activation='relu'))
model.add(Dropout(0.5))
model.add(Dense(4096, activation='relu'))
model.add(Dropout(0.5))
model.add(Dense(10, activation='softmax'))
adam = Adam(lr=1e-5)
model.compile(loss='categorical_crossentropy', optimizer=adam, metrics=['accuracy'])
```

### Inception Net

### ResNet

