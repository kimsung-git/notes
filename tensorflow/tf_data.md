# tf.data

* tf.data.Dataset
* tf.data.DatasetSpec
* tf.data.FixedLengthRecordDataset
* tf.data.Options
* tf.data.TextLineDataset
* tf.data.TFRecordDataset



### tf.data.Dataset

```tf.data.Dataset.from_tensor_slices```
A nested structure of tensors, each having the same size in the 0th dimension. First argument to ```from_tensor_slices``` is either single tensor, tuple or list type. 

**Passing tuple argument**
``` python
dataset1 = tf.data.Dataset.from_tensor_slices(
    (tf.random.uniform([10, 4]), tf.random.uniform([10,2])))  # first dimension should match: both 10, 10 or throws an error

# <TensorSliceDataset shapes: ((4,), (2,)), types: (tf.float32, tf.float32)>

dataset1 = tf.data.Dataset.from_tensor_slices(
    (tf.random.uniform([10, 4]), tf.random.uniform([10]))) 
# <TensorSliceDataset shapes: ((4,), ()), types: (tf.float32, tf.float32)>
```

**Passing list argument**
read a data as a single tensor of the exact same shape
``` python
dataset1 = tf.data.Dataset.from_tensor_slices(
    [tf.random.uniform([10, 4]), tf.random.uniform([10,4])])  # dimension should match or throw an error
# <TensorSliceDataset shapes: (10, 4), types: tf.float32>
```

**Name your dataset**
```python
dataset = tf.data.Dataset.from_tensor_slices(
   {"label": tf.random_uniform([4]),
    "images": tf.random_uniform([4, 100], maxval=100, dtype=tf.int32)})
# <TensorSliceDataset shapes: {label: (), images: (100,)}, types: {labels: tf.float32, images: tf.int32}>
```

```tf.data.Dataset.from_tensors``` 


```tf.data.Dataset.from_tensors``` does not expect its arguments to have the same 0-th dimension. Also takes tuple and list arguments. 
* List arguments require the same data shape. 
* Tuple arguements take different data shape


```python
dataset = tf.data.Dataset.from_tensors(
   (tf.random_uniform([4]),
    tf.random_uniform([4, 10], maxval=10, dtype=tf.int32)))
# <TensorDataset shapes: ((4,), (4, 10)), types: (tf.float32, tf.int32)>
```
**Passing tuple argument**
Take different data shape
``` python
dataset2 = tf.data.Dataset.from_tensors(
    (tf.random.uniform([2, 3]), tf.random.uniform([5,2]))) # takes different shapes
# <TensorDataset shapes: ((2, 3), (5, 2)), types: (tf.float32, tf.float32)>

dataset2 = tf.data.Dataset.from_tensors(
    (tf.random.uniform([2, 3]), tf.random.uniform([2,3])))
# <TensorDataset shapes: ((2, 3), (2, 3)), types: (tf.float32, tf.float32)>
```

**Passing list argument**
Require the same data shape
``` python
dataset1 = tf.data.Dataset.from_tensor(
    [tf.random.uniform([10, 4]), tf.random.uniform([10,2])])  # dimension should match or throw an error
# <TensorSliceDataset shapes: (10, 4), types: tf.float32>

dataset2 = tf.data.Dataset.from_tensors(
    [tf.random.uniform([2, 3]), tf.random.uniform([2,3])])  # concatenates into one large 3D tensor
# <TensorDataset shapes: (2, 2, 3), types: tf.float32>
```


**Comparison**
Tuple
```python
# exception: ValueError: Dimensions 10 and 9 are not compatible
dataset1 = tf.data.Dataset.from_tensor_slices(
    (tf.random_uniform([10, 4]), tf.random_uniform([9])))
# OK, first dimension is same
dataset2 = tf.data.Dataset.from_tensors(
    (tf.random_uniform([10, 4]), tf.random_uniform([10])))
```

List
```python
dataset1 = tf.data.Dataset.from_tensor_slices(
    [tf.random_uniform([2, 3]), tf.random_uniform([2, 3])])

dataset2 = tf.data.Dataset.from_tensors(
    [tf.random_uniform([2, 3]), tf.random_uniform([2, 3])])

print(dataset1) # shapes: (2, 3)
print(dataset2) # shapes: (2, 2, 3)
```

```python
dataset1 = tf.data.Dataset.from_tensor_slices(
    (tf.random_uniform([4, 2]), tf.random_uniform([4])))

dataset2 = tf.data.Dataset.from_tensors(
    (tf.random_uniform([4, 2]), tf.random_uniform([4])))

for i, item in enumerate(dataset1):
    print('element: ' + str(i + 1), item[0], item[1])

print(30*'-')

for i, item in enumerate(dataset2):
    print('element: ' + str(i + 1), item[0], item[1])

# output
#element: 1 tf.Tensor(... shapes: ((2,), ()))
#element: 2 tf.Tensor(... shapes: ((2,), ()))
#element: 3 tf.Tensor(... shapes: ((2,), ()))
#element: 4 tf.Tensor(... shapes: ((2,), ()))
#-------------------------
#element: 1 tf.Tensor(... shapes: ((4, 2), (4,)))
```