# Tensorflow Serving


### Install
Install ```tensorflow-model-server``` package. Add the tensorflow-model-server package to the list of packages that Aptitude knows about. 

Or use docker instead(easiest way)
```bash
echo "deb [arch=amd64] http://storage.googleapis.com/tensorflow-serving-apt stable tensorflow-model-server tensorflow-model-server-universal" | sudo tee /etc/apt/sources.list.d/tensorflow-serving.list && \
# curl https://storage.googleapis.com/tensorflow-serving-apt/tensorflow-serving.release.pub.gpg | sudo apt-key add -

apt-get install tensorflow-model-server
```

### Basic idea of tf serving
Model file should be saved in ```SavedModel``` format. This will create a protobuf file in a well-defined directory herarchy, and will include a version number. 

tf serving allows us to select which version of a model, or "servable" we want to use when we make inference requests. 

```
import tempfile 

MODEL_DIR = tempfile.gettempdir()
version = 1
export_path = os.path.join(MODEL_DIR, str(version))
print('export_path = {}\n'.format(export_path))

tf.keras.models.save_model(
    model,
    export_path,
    overwrite=True,
    include_optimizer=True,
    save_format=None,
    signatures=None,
    options=None
)
```

#### Examine your saved model
use the command line utility ```saved_model_cli``` to look at the MetaGraphDefs(the models) and SignatureDefs(the methods you can call) in our SavedModel. 

```bash
saved_model_cli show --dir {export_path} --all
```

### Start running Tensorflow Serving
After it loads we can start making inference requests using REST. 
* ```rest_api_port```: The port that you will use for REST requests.
* ```model_name```: You'll use this in the URL of REST requests. It can be anything.
* ```model_base_path``` : this is the path to the directory where you've saved your model. 


```python
os.environ['MODEL_DIR"] = MODEL_DIR
```

```bash
nohup tensorflow_model_server \
    --rest_api_port=8501 \
    --model_name=fashion_model \
    --model_base_path="${MODEL_DIR}" > server.log 2>&1
```


### Tensorflow Serving with Docker

Pull tensorflow serving image.
``` bash
docker pull tensorflow/serving
```

* The serving images(both CPU and GPU ) have the following properties:
  * Port 8500 exposed for gRPC
  * Port 8501 exposed for the REST API
  * optional ev var ```MODEL_NAME``` (defaults to ```model```)
  * optional ev var ```MODEL_BASE_PATH``` (defaults to ```/models```) 

Tensorflow searches serverable under ```${MODEL_BASE_PATH}/${MODEL_NAME}``` directory. Make sure to mount your models from host to ```${MODEL_BASE_PATH}/${MODEL_NAME}/``` when running docker container. See the example below.


Example.

Saved model directory and corresponding files.
```bash
$ pwd
/blah/blah/model_dir/1
$ ls
assets  saved_model.pb  variables
```

Serving with docker container
```bash
docker run -dt --name name --rm -p 8501:8501 \
        # when you mount(-v)
        # 1. point to model_dir(do not include 1, which is the version)
        # 2. bind MODEL_NAME(fashion_model) to the MODEL_BASE_PATH(which is /models (default)) -> /models/fashion_model
        -v ${PWD}/model_dir:/models/fashion_model \   # ${MODEL_BASE_PATH}/${MODEL_NAME} 
        -e MODEL_NAME=fashion_model \
        tensorflow/serving 
```
if you set ```MODEL_BASE_PATH=/m``` then you should mount as the following.
```bash
docker run -dt --name name --rm -p 8501:8501 \
        -v ${PWD}/model_dir:/m/fashion_model \   
        -e MODEL_NAME=fashion_model \
        -e MODEL_BASE_PATH=/m \
        tensorflow/serving 
```

### Make a request to your model in TensorFlow Serving

**Make REST requests**

Send a predict request as a POST to our server's REST endpoint. 

```python
import json
data = json.dumps({"signature_name": "serving_default", "instances": test_images[0:3].tolist()})

import requests
headers = {"content-type": "application/json"}
# select version 1
json_response = requests.post('http://localhost:8501/v1/models/fashion_model:predict', data = data, headers=headers)

# returns in list
predictions = json.loads(json_response.text)['predictions']
```

