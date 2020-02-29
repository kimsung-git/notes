# Callback

```tf.keras.callbacks```

Callbacks in Keras are objects that are called at different point during training(at the start of an epoch, at the end of a batch, at the end of an epoch, etc)


#### Writing your own callback
Basic skeleton
```python
class LossHistry(keras.callbacks.Callback):
    def on_train_begin(self, logs):
        self.losses = []
    
    def on_batch_end(self, batch, logs):
        # log is dictionary. 
        self.losses.append(logs.get('loss'))
```


#### Checkpoint
```ModelCheckpoint```
See model_save.md for more detail on model save with callback

**Checkpoint model improvements**
model gets saved only when there is an improvement. 
```python
# file format can be either h5, ckpt, or pb(not sure about .pb)
filepath="weights-improvement-{epoch:02d}-{val_accuracy:.2f}.hdf5"
checkpoint = ModelCheckpoint(filepath, monitor='val_accuracy', verbose=1, save_best_only=True, monitor = 'val_loss', mode='max')
```

**Checkpoint the best model only**
```python
filepath="weights.best.hdf5"
checkpoint = ModelCheckpoint(filepath, monitor='val_accuracy', verbose=1, save_best_only=True, mode='max')
```

#### Using learning rate schedules
gradually reduce the learning as training progresses. AKA "learning rate decay"
The learning decay schedule could be static - fixed in advance, as a function of the current epoch or the current batch index), or dynamic (responding to the current behavior of the model, in particular the validation loss).

you can use a static learning rate decay schedule by passing a schedule object as the ```learning_rate``` argument in your optimizer:
several built-in schedules :
* ExponentialDecay
* PiecewiseConstantDecay
* PolynomiaDecay
* InverseTimeDecay

```python
initial_learning_rate = 0.1
lr_schedule = keras.optimizers.schedules.ExponentialDecay(
    initial_learning_rate,
    decay_step = 100000,
    decay_rate = 0.96,
    staircase = True
)
optimizer = keras.optimizers.RMSprop(learning_rate = lr_schedule)
```

### tensorboard
```python
keras.callbacks.TensorBoard(log_dir = 'path/for/tensorboard')
```


#### EarlyStoppint

``` python
from keras.callbacks import EarlyStopping

earlystop = EarlyStopping(monitor = 'val_loss',
                          min_delta = 0,
                          patience = 3,
                          verbose = 1,
                          restore_best_weights = True)
```