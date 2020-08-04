---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 2b4f768b25917e712380ca4a7f8ac58cb6140777
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542816"
---
Скрипт входа принимает данные, отправляемые в развернутую веб-службу, и передает их в модель. Затем он принимает ответ, возвращенный моделью, и возвращает его клиенту. *Сценарий зависит от модели*. Он должен понимать данные, которые предположительно и возвращает модель.

Скрипт содержит две функции, которые загружают и запускают модель:

* `init()`. Как правило, эта функция загружает модель в глобальный объект. Эта функция выполняется только один раз при запуске контейнера DOCKER для веб-службы.

* `run(input_data)`: Эта функция использует модель для прогнозирования значения на основе входных данных. Ко входным и выходным данным для запуска обычно применяется формат JSON для сериализации и десериализации. Вы также можете работать с необработанными двоичными данными. Вы можете преобразовать данные, прежде чем отправлять их в модель или возвращать клиенту.

REST API ожидает, что текст запроса содержит документ JSON со следующей структурой:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

В следующем примере показано, как загрузить зарегистрированную модель scikit-учиться и оценить ее с помощью данных NumPy:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')

    # If your model were stored in the same directory as your score.py, you could also use the following:
    # model_path = os.path.abspath(os.path.join(os.path.dirname(__file_), 'sklearn_mnist_model.pkl')

    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Дополнительные примеры см. в следующих скриптах:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow);
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras);
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Двоичные данные](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#binary-data)