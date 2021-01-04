---
title: Руководство. Создание прогнозной модели с помощью Notebook (часть 1 из 2)
titleSuffix: Azure Machine Learning
description: Узнайте, как создать и развернуть прогнозную модель машинного обучения с помощью кода и Jupyter Notebook, чтобы использовать ее для прогнозирования результатов в Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f8209c0d26cf8c572d10666696231b0468cfcbc6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370842"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-notebook-part-1-of-2"></a>Руководство. Интеграция Power BI. Создание прогнозной модели с помощью Notebook (часть 1 из 2)

В первой части этого руководства показано, как обучить и развернуть прогнозную модель машинного обучения с помощью кода и Jupyter Notebook. Во второй части показано, как использовать модель для прогнозирования результатов в Microsoft Power BI.

Изучив это руководство, вы:

> [!div class="checklist"]
> * Создание записной книжки Jupyter
> * создадите вычислительный экземпляр Машинного обучения Azure;
> * обучите модель регрессии с помощью scikit-learn;
> * развернете модель в конечной точке оценки в реальном времени.

Есть три способа создания и развертывания модели, которая будет использоваться в Power BI.  В этой статье рассматривается вариант А — обучение и развертывание моделей с помощью записных книжек.  Этот вариант предусматривает использование подхода code-first и записных книжек Jupyter, размещенных в Студии машинного обучения Azure. 

Вместо этого можно использовать:

* [Вариант Б. Обучение и развертывание моделей с помощью конструктора](tutorial-power-bi-designer-model.md) (пользовательский интерфейс перетаскивания), при использовании которого не требуется писать много кода.
* [Вариант В. Обучение и развертывание моделей с помощью автоматизированного машинного обучения](tutorial-power-bi-automated-model.md) — интерфейс для разработки, при использовании которого не нужно писать код и который полностью автоматизирует подготовку и обучение модели.


## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure ([доступна бесплатная пробная версия](https://aka.ms/AMLFree)). 
- Рабочая область машинного обучения Azure. Если у вас еще нет рабочей области, см. инструкции по [созданию рабочей области Машинного обучения Azure](./how-to-manage-workspace.md#create-a-workspace).
- Вводные знания о языке Python и рабочих процессах машинного обучения.

## <a name="create-a-notebook-and-compute"></a>Создание записной книжки и вычислительного экземпляра

На домашней странице [Студии машинного обучения Azure Studio](https://ml.azure.com) выберите **Создать** и щелкните **Записная книжка**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Снимок экрана: создание записной книжки":::
 
Отобразится диалоговое окно **Создание нового файла**, где:

1. нужно указать имя файла записной книжки (например, `my_model_notebook`);
1. нужно указать для параметра **Тип файла** значение **Записная книжка**.

Щелкните **Создать**. Далее необходимо создать несколько вычислительных экземпляров и подключить их к записной книжке для выполнения ячеек кода. Для этого щелкните значок плюса в верхней части записной книжки:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Снимок экрана: создание вычислительного экземпляра":::

Затем на странице **Создание вычислительного экземпляра**:

1. Укажите размер виртуальной машины ЦП — для работы с этим руководством выберите **Standard_D11_v2** (два ядра, 14 ГБ ОЗУ).
1. Выберите **Далее**. 
1. На странице **Настройка параметров** укажите допустимое имя **вычислительного экземпляра** (допустимые символы: буквы верхнего и нижнего регистра, цифры и символ -).
1. Щелкните **Создать**.

Вы можете заметить, что в блокноте рядом с полем **Вычисление** появился голубой кружок, указывающий на то, что создается экземпляр вычислений:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Снимок экрана: создание вычислительного экземпляра":::

> [!NOTE]
> Подготовка вычислительного экземпляра занимает около 2–4 минут.

После подготовки вычислительного экземпляра можно использовать записную книжку для выполнения ячеек кода. Например, введите в ячейку:

```python
import numpy as np

np.sin(3)
```

Затем нажмите клавиши **SHIFT+ВВОД** (либо **CTRL+ВВОД** или нажмите кнопку воспроизведения рядом с ячейкой). Вы должны увидеть следующий результат:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Снимок экрана: выполнение ячейки":::

Теперь можно создать модель машинного обучения.

## <a name="build-a-model-using-scikit-learn"></a>Создание модели с помощью scikit-learn

В этом руководстве показано, как использовать набор данных [diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), который доступен в [Открытых наборах данных Azure](https://azure.microsoft.com/services/open-datasets/). 


### <a name="import-data"></a>Импорт данных

Чтобы импортировать данные, скопируйте и вставьте следующий код в новую **ячейку кода** в записной книжке:

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

Кадр данных Pandas `X_df` содержит 10 базовых входных переменных (например, возраст, пол, индекс массы тела, среднее кровяное давление и шесть показателей сыворотки крови). Кадр данных Pandas `y_df` — это целевая переменная, содержащая количественный показатель развития болезни за год в сравнении с исходным значением. Всего доступно 442 записи.

### <a name="train-model"></a>Обучение модели

Создайте новую **ячейку кода** в записной книжке, а затем скопируйте и вставьте следующий фрагмент кода, который создает модель регрессии и сериализует модель, используя формат файла Python:

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Регистрация модели

Вместе с содержимым файла модели в зарегистрированной модели также будут храниться метаданные модели: описание модели, теги и сведения о платформе, которые будут полезны при управлении моделями в рабочей области и их развертывании. С помощью тегов, например, можно классифицировать модели и применять фильтры при составлении списка моделей в рабочей области. Кроме того, маркировка этой модели с использованием scikit-learn упростит развертывание в качестве веб-службы, как будет показано далее.

Скопируйте и вставьте следующий код в новую **ячейку кода** в записной книжке:

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

Вы также можете просмотреть модель в Студии машинного обучения Azure, перейдя в раздел **Конечная точка** в меню слева:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Снимок экрана: модель":::

### <a name="define-the-scoring-script"></a>Определение скрипта оценки

При развертывании модели, которая должна быть интегрирована в Microsoft Power BI, необходимо определить *скрипт оценки* Python и пользовательскую среду. Скрипт оценки содержит две функции:

- `init()` — эта функция выполняется после запуска службы. Она загружает модель (обратите внимание, что модель автоматически скачивается из реестра модели) и десериализует ее.
- `run(data)` — эта функция выполняется при вызове службы с некоторыми входными данными, требующими оценки. 

>[!NOTE]
> Для определения схемы входных и выходных данных, которые требуются для интеграции Microsoft Power BI, используются декораторы Python.

Скопируйте и вставьте следующий код в новую **ячейку кода** в записной книжке. В следующем фрагменте кода содержится магическая ячейка, которая записывает код в файл score.py.

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
    # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Определение пользовательской среды

Далее необходимо определить среду для оценки модели, а в среде — пакеты Python, требуемые для выполнения скрипта оценки (score.py), определенного выше, например, с использованием Pandas, scikit-learn и пр.

Чтобы определить среду, скопируйте и вставьте следующий код в новую **ячейку кода** в записной книжке:

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

Чтобы развернуть модель, скопируйте и вставьте следующий код в новую **ячейку кода** в записной книжке:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> Развертывание службы занимает около 2–4 минут.

Если служба будет развернута успешно, отобразятся следующие выходные данные:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Вы также можете просмотреть службу в Студии машинного обучения Azure, перейдя в раздел **Конечная точка** в меню слева:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Снимок экрана: конечная точка":::

Рекомендуется протестировать веб-службу, чтобы убедиться, что она работает должным образом. Вернитесь к записной книжке, выбрав **Записные книжки** в меню слева в Студии машинного обучения Azure. Чтобы протестировать службу, скопируйте и вставьте следующий код в новую **ячейку кода** в записной книжке:

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

Из этого руководства вы узнали, как создать и развернуть модель так, чтобы ее можно было использовать в Microsoft Power BI. В следующей части показано, как использовать эту модель из отчета Power BI.

> [!div class="nextstepaction"]
> [Руководство. Использование модели в Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
