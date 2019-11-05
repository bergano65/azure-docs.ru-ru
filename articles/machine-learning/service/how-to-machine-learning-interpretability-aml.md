---
title: Интерпретируемость модели для локальных и удаленных запусков
titleSuffix: Azure Machine Learning
description: Узнайте, как объяснить, почему модель выполняет прогнозы с помощью пакета SDK для Машинное обучение Azure. Его можно использовать во время обучения и вывода, чтобы понять, как модель определяет важность функций и делает прогнозы.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a2b71a10606b7cd20f06b2497515b758426833a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515308"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Интерпретируемость модели для локальных и удаленных запусков

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как объяснить, почему модель выполнила прогнозы с помощью пакета интерпретатора SDK Машинное обучение Azure Python. Вы узнаете о следующих задачах:

* Анализ моделей машинного обучения, обученных локально и на удаленных ресурсах вычислений
* Хранение локальных и глобальных объяснений в журнале выполнения Azure
* Просмотр визуализаций интерпретации в [машинное обучение Azure Studio](https://ml.azure.com)
* Развертывание объяснения оценки с помощью модели

Дополнительные сведения о интерпретируемости модели см. в [статье концепции](how-to-machine-learning-interpretability.md).

## <a name="local-interpretability"></a>Локальная интерпретируемость

В следующем примере показано, как использовать пакет интерпретации локально без обращения к службам Azure. Запустите `pip install azureml-interpret`, чтобы получить пакет интерпретации.

1. Обучение образца модели в локальной записной книжке Jupyter.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Вызов пояснения в локальной среде: для инициализации объекта объяснения необходимо передать модель и некоторые обучающие данные в конструктор пояснения. Кроме того, можно передать имена функций и имена классов вывода (если вы выполняете классификацию), которые будут использоваться для повышения информативных объяснений и визуализаций. Вот как можно создать экземпляр объекта пояснения с помощью `TabularExplainer`, `MimicExplainer`и `PFIExplainer` локально. `TabularExplainer` вызывает один из трех объяснений ШАП под (`TreeExplainer`, `DeepExplainer`или `KernelExplainer`) и автоматически выбирает наиболее подходящий из них для вашего варианта использования. Однако можно вызвать каждый из трех базовых методов объяснения напрямую.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    или

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns. 
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```
   или

    ```python
    from interpret.ext.blackbox import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>Общие (глобальные) значения важности функций

Получите глобальные значения важности функций.
    
```python

# you can use the training data or the test data here
global_explanation = explainer.explain_global(x_train)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_test)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="instance-level-local-feature-importance-values"></a>Значения важности для функций уровня экземпляра (локальных)

Получить значения важности локального компонента: используйте следующие вызовы функций для объяснения отдельного экземпляра или группы экземпляров. Обратите внимание, что Пфиексплаинер не поддерживает местные объяснения.

```python
# explain the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>Интерпретируемость для удаленных запусков

В этом примере показано, как использовать класс `ExplanationClient`, чтобы обеспечить возможность интерпретации модели для удаленных запусков. Понятие аналогично предыдущему разделу, но вы используете `ExplanationClient` в удаленном запуске для передачи контекста интерпретации, а затем можете скачать контекст позже в локальной среде. Чтобы получить необходимый пакет, используйте `pip install azureml-contrib-interpret`.

1. Создайте сценарий обучения в локальной записной книжке Jupyter (например, train_explain. корректировка).

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Следуйте инструкциям по [настройке целевых объектов вычислений для обучения модели](how-to-set-up-training-targets.md#amlcompute) , чтобы узнать, как настроить машинное обучение Azure вычислений в качестве целевого объекта вычислений и отправить обучающий запуск. Можно также просмотреть [примеры записных книжек](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation).

1. Скачайте пояснение в локальной записной книжке Jupyter.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="raw-feature-transformations"></a>Преобразования необработанных функций

При необходимости можно передать конвейер преобразования компонента в пояснение (в train_explain. корректировки), чтобы получить объяснения в терминах необработанных функций перед преобразованием (а не сконструированными функциями). При пропуске этого объяснения пояснения приводятся в терминах инженерных функций.

Формат поддерживаемых преобразований совпадает со стандартом, описанным в [sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Как правило, все преобразования поддерживаются при условии, что они работают с одним столбцом и, таким образом, явно являются одними и многими. 

Объясните необработанные функции либо с помощью `sklearn.compose.ColumnTransformer`, либо из списка заданных кортежей трансформатора. В приведенном ниже коде используется `sklearn.compose.ColumnTransformer`. 


```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Если вы хотите выполнить пример со списком заданных кортежей трансформатора, используйте следующий код.

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="visualizations"></a>Визуализации

После скачивания объяснений в локальной записной книжке Jupyter можно использовать панель мониторинга визуализации для понимания и интерпретации модели.

### <a name="global-visualizations"></a>Глобальные визуализации

На следующих диаграммах представлено глобальное представление обученной модели, а также ее прогнозов и объяснений.

|Отобразить|Description (Описание)|
|----|-----------|
|Исследование данных| Общие сведения о наборе данных и прогнозируемых значениях.|
|Глобальная важность|Отображает первые K (с возможностью настройки K) важные функции в глобальном масштабе. Эта диаграмма полезна для понимания глобального поведения базовой модели.|
|Исследование объяснений|Демонстрируется, как функция отвечает за внесение изменений в прогнозируемые значения модели (или вероятность прогнозируемых значений). В нем также показано, как взаимодействуют два компонента, которые влияют на прогнозы.|
|Сводка по важности| Использует значения важности подписанных локальных компонентов для всех точек данных, чтобы продемонстрировать распределение влияния каждого компонента на прогнозируемое значение.|

[Глобальная панель мониторинга визуализации ![](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Локальные визуализации

Щелкните любую отдельную точку данных в любое время на предыдущих диаграммах, чтобы загрузить график важности локального компонента для заданной точки данных.

|Отобразить|Description (Описание)|
|----|-----------|
|Локальная важность|Отображает первые K (с возможностью настройки K) важные функции в глобальном масштабе. Эта диаграмма полезна для понимания локального поведения базовой модели на определенной точке данных.|
|Исследование пертурбатион|Позволяет изменять значения компонентов выбранной точки данных и следить за тем, как эти изменения повлияют на прогнозируемое значение.|
|Отдельное условное ожидание (ICE)| Позволяет изменить значение компонента с минимального на максимальное значение, чтобы увидеть, как изменяется прогноз точки данных при изменении компонента.|

[Важность локального компонента панели мониторинга визуализации ![](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[Пертурбатион функции панели мониторинга визуализации ![](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[Графики на панели мониторинга ![визуализации](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Обратите внимание, что перед запуском ядра Jupyter необходимо включить расширения мини-приложений панели мониторинга визуализации.

* Записные книжки Jupyter

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Чтобы загрузить панель мониторинга визуализации, используйте следующий код.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Визуализация в Машинное обучение Azure Studio

Выполнив действия, описанные в разделе [Remote интерпретируемость](how-to-machine-learning-interpretability-aml.md#interpretability-for-remote-runs) , можно проверить панель мониторинга визуализации в [машинное обучение Azure Studio](https://ml.azure.com). Панель мониторинга, показанная в Машинное обучение Azure Studio, представляет собой более простую версию панели мониторинга визуализации, описанную выше, и поддерживает только следующие две вкладки.

|Отобразить|Description (Описание)|
|----|-----------|
|Глобальная важность|Отображает первые K (с возможностью настройки K) важные функции в глобальном масштабе. Эта диаграмма полезна для понимания глобального поведения базовой модели.|
|Сводка по важности| Использует значения важности подписанных локальных компонентов для всех точек данных, чтобы продемонстрировать распределение влияния каждого компонента на прогнозируемое значение.|

Если доступны как глобальные, так и локальные объяснения, то обе вкладки будут заполнены данными. Если доступно только глобальное объяснение, то Вторая вкладка будет отключена.

Для доступа к панели мониторинга визуализации в Машинное обучение Azure Studio можно использовать один из следующих путей:

1. Вкладка "эксперименты" (Предварительная версия). щелкнув вкладку "эксперименты", вы увидите список экспериментов, которые вы выполняли в Машинное обучение Azureной службе. В этом списке можно выбрать конкретный эксперимент, который будет перенаправлен на страницу, где все запуски выполняются под выбранным именем эксперимента. Щелкнув каждый запуск и вкладку "объяснения", вы увидите панель мониторинга визуализации объяснение.


[Важность локального компонента панели мониторинга визуализации ![](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)


2. Вкладка модели. Если вы зарегистрировали исходную модель, выполнив действия, описанные в разделе [Развертывание моделей с помощью машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where), модель будет отображаться в списке на вкладке "модели". Щелкнув каждую модель и соответствующую ей вкладку "объяснения", вы увидите панель мониторинга визуализации объяснение.

## <a name="interpretability-at-inference-time"></a>Возможные интерпретации во время вывода

Объяснение может быть развернуто вместе с исходной моделью и может использоваться во время вывода для предоставления сведений о локальных объяснениях. Мы также предлагаем более легкие дополнительные пояснения к оценке для повышения производительности интерпретации во время вывода. Процесс развертывания пояснения к более светлой оценке аналогичен развертыванию модели и включает следующие шаги:


1. Создайте объект пояснения (например, с помощью Табуларексплаинер):

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Создайте пояснение к оценке с помощью объекта пояснения:

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Настройка и регистрация образа, использующего модель пояснения к оценкам.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Используемых Получение объяснения оценки из облака и тестирование объяснений

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Разверните образ в целевом объекте вычислений:

   1. Создайте файл оценки (перед выполнением этого шага выполните действия, описанные в разделе [Развертывание моделей с машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) для регистрации исходной модели прогнозирования).

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import pandas as pd
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. Определите конфигурацию развертывания (Эта конфигурация зависит от требований модели. В следующем примере определяется конфигурация, использующая одно ядро ЦП и 1 ГБ памяти.

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. Создание файла с зависимостями окружения

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

        # specify CondaDependencies obj
        myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                         pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                         pin_sdk_version=False)


        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. Создание настраиваемого dockerfile с установленным g + +

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Развертывание созданного образа (оценка времени: 5 минут)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ```

1. тестирование развертывания

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Очистка: чтобы удалить развернутую веб-службу, используйте `service.delete()`.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о возможностях интерпретации модели см. в [концептуальной статье](how-to-machine-learning-interpretability.md).
