# Save model

Check callback.md for more detail on ModelCheckpoint

## Saving only weights

checkpoint-formatted ```ckpt``` files are files that contain only the trained weight in a binary format. 
* One or more shards that contain your model's weights
* An index file that indicates which weights are sotred in a which shard

**Checkpoint callback usage**
```python
# this creates a single collection of tf checkpoint files that are updated at the end of each epoch
checkpoint_path = "training_1/cp.ckpt"
checkpoint_dir = os.path.dirname(checkpoint_path)

# Create a callback that saves the model's weights
cp_callback = tf.keras.callbacks.ModelCheckpoint(filepath=checkpoint_path,
        save_weights_only=True,
        verbose=1)
```

**Save uniquely named checkpoints once every Nth epoch**

```python
# Include the epoch in the file name (uses `str.format`)
checkpoint_path = "training_2/cp-{epoch:04d}.ckpt"
checkpoint_dir = os.path.dirname(checkpoint_path)

# Create a callback that saves the model's weights every 5 epochs
cp_callback = tf.keras.callbacks.ModelCheckpoint(
    filepath=checkpoint_path, 
    verbose=1, 
    save_weights_only=True,
    period=5)

# Create a new model instance
model = create_model()

# Save the weights using the `checkpoint_path` format
model.save_weights(checkpoint_path.format(epoch=0))

# Train the model with the new callback
model.fit(train_images, 
          train_labels,
          epochs=50, 
          callbacks=[cp_callback],
          validation_data=(test_images,test_labels),
          verbose=0)

```

**Manually Save weights**

simply call save_weights after train is done

```python
model.save_weights('./checkpoints/my_checkpoint')
```

Load saved weight and create a model 
```python

# Define a simple sequential model
def create_model():
  model = tf.keras.models.Sequential([
    keras.layers.Dense(512, activation='relu', input_shape=(784,)),
    keras.layers.Dropout(0.2),
    keras.layers.Dense(10)
  ])

  model.compile(optimizer='adam',
                loss=tf.losses.SparseCategoricalCrossentropy(from_logits=True),
                metrics=['accuracy'])

  return model


model = create_model() # create model structure and compile

# if saved model once every nth time, use the latest weights 
#latest = tf.train.latest_checkpoint(checkpoint_dir)

# then load the weights from the checkpoint and re-evaluate
model.load_weights(checkpoint_path)
# re-evaluate model
model.evaluate(..)
```


## Whole-model saving

**SavedModel, HDF5**

* The models' architecture
* The model's weight values(which were learned during training)
* The model's training config(what you passed to compile)
* The optimizer and its state, if any(this enables you to restart training where you left)

Entire model can be saved in two different file formats (SavedModel, HDF5). SavedModel format is the default file format in TF2.x

Custom object (subclassed models or layers) require special attention when saving and loading

**HDF5 format**

Keras povides a basic save format using the HDF5 standard
```python
model.save('path/to/save.h5')

# recreate the sxact same model
new_model = keras.model.load_model('path/to/save.h5')
```

**SavedModel(.pb)**

Export a whole model to the TensorFlow ```SaveModel``` format. ```SavedModel``` is a standalone serialization format for TensorFlow objects, supported by tf serving as well as tf implementations other than python. File expension is ```.pb```. The SavedModel format is a directory containing a protobuf binary and a tensorflow checkpoint. 

```python
# Export the model to a SavedModel
# SavedModel format is default in tf2
model.save('path/to/save', save_format='tf')

# recreate the sxact same model
new_model = keras.model.load_model('path/to/save')
```

```SavedModel``` files that were created contains:
* A tf checkpoint containing the model weights
* A ```SavedModel``` proto containing the underlying tf graph


```python
```
```python
```