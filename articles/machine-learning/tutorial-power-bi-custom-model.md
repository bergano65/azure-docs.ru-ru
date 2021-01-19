---
title: Руководство по Создание прогнозной модели с помощью записной книжки (часть 1 из 2)
titleSuffix: Azure Machine Learning
description: Узнайте, как создать и развернуть модель машинного обучения с помощью кода в записной книжке Jupyter Notebook. Также создайте скрипт оценки, который определяет входные и выходные данные для упрощения интеграции в Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 29b340448f3ce3e18a649065bdcd0b335bab8b73
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108251"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-jupyter-notebook-part-1-of-2"></a>Руководство по Интеграция Power BI. Создание прогнозной модели с помощью Jupyter Notebook (часть 1 из 2)

В первой части этого руководства показано, как обучить и развернуть прогнозную модель машинного обучения с помощью кода в записной книжке Jupyter Notebook. Вы также создадите скрипт оценки, чтобы определить входную и выходную схему модели для интеграции в Power BI.  Во второй части показано, как использовать модель для прогнозирования результатов в Microsoft Power BI.

Изучив это руководство, вы:

> [!div class="checklist"]
> * Создание записной книжки Jupyter.
> * создадите вычислительный экземпляр Машинного обучения Azure;
> * обучите модель регрессии с помощью scikit-learn;
> * Создайте скрипт оценки, который определяет входные и выходные данные для упрощения интеграции в Microsoft Power BI.
> * развернете модель в конечной точке оценки в реальном времени.

Есть три способа создания и развертывания модели, которая будет использоваться в Power BI.  В этой статье рассматривается вариант А — обучение и развертывание моделей с помощью записных книжек.  Этот вариант предусматривает использование подхода Code First и записных книжек Jupyter Notebook, размещенных в Студии машинного обучения Azure. 

Но вместо него можно использовать один из следующих вариантов:

* [Вариант Б. Обучение и развертывание моделей с помощью конструктора Машинного обучения Azure.](tutorial-power-bi-designer-model.md) Этот вариант предусматривает разработку модели с помощью пользовательского интерфейса перетаскивания, при использовании которого не требуется писать много кода.
* [Вариант В. Обучение и развертывание моделей с помощью автоматизированного машинного обучения.](tutorial-power-bi-automated-model.md) Этот вариант предусматривает разработку без использования кода с полной автоматизацией подготовки данных и обучения модели.


## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас нет подписки, вы можете использовать [бесплатную пробную версию](https://aka.ms/AMLFree). 
- Рабочая область машинного обучения Azure. Если у вас нет рабочей области, см. раздел [Создание рабочих областей Машинного обучения Azure и управление ими](./how-to-manage-workspace.md#create-a-workspace).
- Вводные знания о языке Python и рабочих процессах машинного обучения.

## <a name="create-a-notebook-and-compute"></a>Создание записной книжки и вычислительного экземпляра

На домашней странице [**Студии машинного обучения Azure**](https://ml.azure.com) выберите **Создать** > **Записная книжка**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Снимок экрана: создание записной книжки.":::
 
На странице **Создание файла** сделайте следующее:

1. Присвойте записной книжке имя (например, *my_model_notebook*).
1. Измените значение параметра **Тип файла** на **Записная книжка**.
1. Щелкните **Создать**. 
 
Затем для выполнения ячеек кода создайте вычислительный экземпляр и подключите его к записной книжке. Для этого щелкните значок плюса в верхней части записной книжки:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Снимок экрана: создание вычислительного экземпляра.":::

На странице **Создание вычислительного экземпляра** сделайте следующее:

1. Выберите размер виртуальной машины с ЦП. Для работы с этим руководством можно выбрать **Standard_D11_v2** с 2 ядрами и 14 ГБ ОЗУ.
1. Выберите **Далее**. 
1. На странице **Настройка параметров** укажите допустимое **имя вычисления**. Допустимые символы: прописные и строчные буквы, цифры и дефисы (-).
1. Щелкните **Создать**.

В записной книжке вы можете заметить, что кружок рядом с надписью **Вычисление** сменил цвет на голубой. Это указывает, что создается вычислительный экземпляр:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Снимок экрана: создание вычислительного экземпляра.":::

> [!NOTE]
> Подготовка вычислительного экземпляра занимает от 2 до 4 минут.

После подготовки вычислительного экземпляра вы можете использовать записную книжку для выполнения ячеек кода. Например, в ячейке можно ввести следующий код:

```python
import numpy as np

np.sin(3)
```

Затем нажмите клавиши SHIFT+ВВОД (или CTRL+ВВОД либо нажмите кнопку **Воспроизвести** рядом с ячейкой). Вы должны увидеть следующий результат:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Снимок экрана: выходные данные ячейки.":::

Теперь можно создать модель машинного обучения.

## <a name="build-a-model-by-using-scikit-learn"></a>Создание модели с помощью scikit-learn

В этом руководстве используется [набор данных Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Этот набор данных доступен в [Открытых наборах данных Azure](https://azure.microsoft.com/services/open-datasets/).


### <a name="import-data"></a>Импорт данных

Чтобы импортировать данные, скопируйте и вставьте следующий код в новую *ячейку кода* в записной книжке.

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

Кадр данных Pandas `X_df` содержит 10 базовых входных переменных. Эти переменные описывают возраст, пол, индекс массы тела, среднее кровяное давление и шесть показателей сыворотки крови. Кадр данных Pandas `y_df` является целевой переменной. Она содержит количественный показатель развития болезни за год в сравнении с исходным значением. Кадр данных содержит 442 записи.

### <a name="train-the-model"></a>Обучение модели

Создайте новую *ячейку кода* в записной книжке. Скопируйте следующий код и вставьте его в ячейку. Этот фрагмент кода создает модель гребневой регрессии и сериализует модель с помощью формата модуля pickle для Python.

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Регистрация модели

Кроме содержимого файла модели, в зарегистрированной модели будут храниться метаданные. Метаданные включают описание модели, теги и сведения о платформе. 

Метаданные полезны при управлении моделями в рабочей области и их развертывании. С помощью тегов, например, можно классифицировать модели и применять фильтры при составлении списка моделей в рабочей области. Кроме того, маркировка этой модели с использованием scikit-learn упростит развертывание в качестве веб-службы.

Скопируйте и вставьте следующий код в новую *ячейку кода* в записной книжке.

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

Вы также можете просмотреть модель в Студии машинного обучения Azure. В меню слева выберите **Модели**:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Снимок экрана: просмотр модели.":::

## <a name="define-the-scoring-script"></a>Определение скрипта оценки

При развертывании модели, которая должна быть интегрирована в Power BI, необходимо определить *скрипт оценки* Python и пользовательскую среду. Скрипт оценки содержит две функции:

- Функция `init()` выполняется при запуске службы. Она загружает модель (которая автоматически скачивается из реестра модели) и десериализует ее.
- Функция `run(data)` выполняется, когда вызов к службе включает входные данные для оценки. 

>[!NOTE]
> Декораторы Python в приведенном ниже коде определяют схему входных и выходных данных, что важно для интеграции в Power BI.

Скопируйте и вставьте следующий код в новую *ячейку кода* в записной книжке. В следующем фрагменте кода содержится магическая команда, которая записывает код в файл с именем *score.py*.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Определение пользовательской среды

Затем определите среду для оценки модели. В среде определите пакеты Python, такие как Pandas и scikit-learn, которые требуются для скрипта оценки (*score.py*).

Чтобы определить среду, скопируйте и вставьте следующий код в новую *ячейку кода* в записной книжке.

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>Развертывание модели

Чтобы развернуть модель, скопируйте и вставьте следующий код в новую *ячейку кода* в записной книжке:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> Развертывание службы занимает от 2 до 4 минут.

Если служба будет развернута успешно, отобразятся следующие выходные данные:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Вы также можете просмотреть службу в Студии машинного обучения Azure. В меню слева выберите **Конечные точки**:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Снимок экрана: просмотр службы.":::

Рекомендуется протестировать веб-службу, чтобы убедиться, что она работает должным образом. Чтобы вернуться в записную книжку, в Студии машинного обучения Azure в меню слева выберите **Записные книжки**. Затем скопируйте и вставьте следующий код в новую *ячейку кода* в записной книжке, чтобы протестировать службу.

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

Выходные данные должны выглядеть, как в следующей структуре JSON: `{'predict': [[205.59], [68.84]]}`.

## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как создать и развернуть модель так, чтобы ее можно было использовать в Power BI. В следующей части показано, как использовать эту модель в отчете Power BI.

> [!div class="nextstepaction"]
> [Учебник. Использование модели в Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
