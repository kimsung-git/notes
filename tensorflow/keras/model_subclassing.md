# Model

```tf.keras.Model```

```python
from tf.keras import Model
```

#### Two ways to instantiate a Model


1. With the "functional API", where you start from ```Input```

```python
import tensorflow as tf
inputs = tf.keras.Input(shape=(3,))
x = tf.keras.layers.Dense(4, activation=tf.nn.relu)(inputs)
outputs = tf.keras.layers.Dense(5, activation=tf.nn.softmax)(x)
model = tf.keras.Model(inputs=inputs, outputs=outputs)
```

2. By subclassing the ```Model``` class. In that case, you should define your layers in ```__init__``` and you should implement the model's forward pass in ```call```.

Example - skeleton 
```python
class MyModel(tf.keras.Model):
    def __init__(self):
        super(MyModel, self).__init__()
        # define your layers here
    def call(self, inputs, training):
        # define your model's forward pass
        # input data gets passed to this call function
        # optional training argument -  different behavior in training and inference
```

Example - subclassing using Model API

```python
class MyModel(tf.keras.Model):
    def __init__(self):
        super(MyModel, self).__init__()
        self.dense1 = tf.keras.layers.Dense(4, activation=tf.nn.relu)
        self.dense2 = tf.keras.layers.Dense(5, activation=tf.nn.softmax)
        self.dropout = tf.keras.layers.Dropout(0.5)

    def call(self, inputs, training=False):
        x = self.dense1(inputs)
        if training:
            x = self.dropout(x, training=training)
        return self.dense2(x)
    
    def model_build(self, inputs_shape):  # helper function to print summary
        x = tf.keras.Input(shape=inputs_shape)
        return Model(inputs=[x], outputs=self.call(x))

model = MyModel()
optimizer = tf.keras.optimizers.Adam(learning_rate=1e-3)
model.compile(optimizer, loss=tf.keras.losses.MeanSquaredError())
# call build function before summary.
# model needs to know the shape of input 
model.build((None,4))  # pass input shape
# but summary doesnt tell us exact input shape when using subclassing
model.summary()

# use model_build helper function we defined
model.model_build((4,)).summary()
```

``` python
```
``` python
```
``` python
```

