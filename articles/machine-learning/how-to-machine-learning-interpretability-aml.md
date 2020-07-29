---
title: Анализ & объяснения моделей машинного обучения в Python (Предварительная версия)
titleSuffix: Azure Machine Learning
description: Узнайте, как получить объяснения о том, как модель машинного обучения определяет важность функций и делает прогнозы при использовании пакета SDK для Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: 7f2ca2a84123d3bb7dd92a797d517a2490544efa
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87307018"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Использование пакета интерпретации для объяснения моделей машинного обучения & прогнозах в Python (Предварительная версия)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этом пошаговом руководство вы узнаете, как использовать пакет интерпретации пакета SDK для Машинное обучение Azure Python для выполнения следующих задач:


* Объясните все поведение модели или отдельные прогнозы на персональном компьютере локально.

* Включить приемы интерпретации для сконструированных функций.

* Объясните поведение всей модели и отдельных прогнозов в Azure.

* Используйте панель мониторинга визуализации для взаимодействия с пояснениями к модели.

* Разверните пояснение к оценке вместе с моделью, чтобы просмотреть объяснения во время ее формирования.



Дополнительные сведения о поддерживаемых методиках интерпретации и моделях машинного обучения см. [в статье интерпретируемость модели в машинное обучение Azure](how-to-machine-learning-interpretability.md) и [примеры записных книжек](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Создание значения важности функции на личном компьютере 
В следующем примере показано, как использовать пакет интерпретации на персональном компьютере без обращения к службам Azure.

1. Установка `azureml-interpret` и `azureml-contrib-interpret` пакеты.
    ```bash
    pip install azureml-interpret
    pip install azureml-contrib-interpret
    ```

2. Обучение образца модели в локальной записной книжке Jupyter.

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

3. Вызовите объяснение в локальной среде.
   * Чтобы инициализировать объект объяснения, передайте модель и некоторые обучающие данные в конструктор объяснения.
   * Чтобы сделать объяснения и визуализации более информативными, вы можете передать имена функций и имена выходных классов при выполнении классификации.

   В следующих блоках кода показано, как создать экземпляр объекта объяснения с помощью `TabularExplainer` , `MimicExplainer` и `PFIExplainer` локально.
   * `TabularExplainer`вызывает один из трех объяснений ШАП ниже ( `TreeExplainer` , `DeepExplainer` или `KernelExplainer` ).
   * `TabularExplainer`автоматически выбирает наиболее подходящий вариант для вашего варианта использования, но можно напрямую вызвать каждый из трех базовых методов объяснения.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    или диспетчер конфигурации служб

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

    или диспетчер конфигурации служб

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Объясните все поведение модели (глобальное объяснение) 

Чтобы получить сводные (глобальные) значения важности функций, см. Следующий пример.

```python

# you can use the training data or the test data here, but test data would allow you to use Explanation Exploration
global_explanation = explainer.explain_global(x_test)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_train)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>Объясните отдельный прогноз (локальное объяснение)
Получите значения важности отдельных компонентов для различных точек данных, вызвав объяснения для отдельного экземпляра или группы экземпляров.
> [!NOTE]
> `PFIExplainer`не поддерживает локальные объяснения.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Преобразования необработанных функций

Вы можете получить объяснения с точки зрения необработанных, непреобразованных, а не сконструированных функций. Для этого параметра конвейер преобразования компонента передается в объяснение в `train_explain.py` . В противном случае разъяснение содержит объяснения с точки зрения инженерных функций.

Формат поддерживаемых преобразований такой же, как описано в [sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Как правило, все преобразования поддерживаются при условии, что они работают с одним столбцом, чтобы сделать их более простыми.

Ознакомьтесь с описанием необработанных функций с помощью `sklearn.compose.ColumnTransformer` или со списком заданных кортежей трансформатора. В следующем примере используется `sklearn.compose.ColumnTransformer` .

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

Если вы хотите выполнить пример со списком заданных кортежей трансформатора, используйте следующий код:

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>Создание значений важности функций с помощью удаленных запусков

В следующем примере показано, как можно использовать `ExplanationClient` класс, чтобы разрешить интерпретируемость модели для удаленных запусков. Он концептуально напоминает локальный процесс, за исключением:

* Используйте `ExplanationClient` в удаленном запуске, чтобы передать контекст интерпретации.
* Скачайте контекст позже в локальной среде.

1. Установка `azureml-interpret` и `azureml-contrib-interpret` пакеты.
    ```bash
    pip install azureml-interpret
    pip install azureml-contrib-interpret
    ```
1. Создайте сценарий обучения в локальной записной книжке Jupyter. Например, `train_explain.py`.

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

1. Настройте Машинное обучение Azure вычислений в качестве целевого объекта вычислений и отправьте обучающий запуск. Инструкции см. в разделе [Настройка целевых объектов вычислений для обучения модели](how-to-set-up-training-targets.md#amlcompute) . Вы также можете найти [примеры записных книжек](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) .

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


## <a name="visualizations"></a>Визуализации

После скачивания объяснений в локальную записную книжку Jupyter можно использовать панель мониторинга визуализации для понимания и интерпретации модели.

### <a name="understand-entire-model-behavior-global-explanation"></a>Понимание поведения модели (глобальное объяснение) 

На следующих диаграммах представлено общее представление обученной модели, а также ее прогнозов и объяснений.

|Графическое представления|Описание:|
|----|-----------|
|Исследование данных| Отображает общие сведения о наборе данных вместе со значениями прогноза.|
|Глобальная важность|Выполняет статистическую обработку значений важности для отдельных точек данных, чтобы отобразить общие основные функции K (с возможностью настройки K) для модели. Помогает понять общее поведение базовой модели.|
|Исследование объяснений|Демонстрирует, как функция влияет на изменение в прогнозируемых значениях модели или на вероятность прогнозируемых значений. Показывает влияние взаимодействия функций.|
|Сводка по важности|Использует отдельные значения важности функций для всех точек данных, чтобы продемонстрировать распределение воздействия каждого компонента на прогнозируемое значение. С помощью этой схемы вы изучаете, в каком направлении значения функций влияют на прогнозируемые значения.
|

[![Глобальная панель мониторинга визуализации](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Общие сведения о конкретных прогнозах (локальное объяснение) 

Можно загрузить график важности отдельных компонентов для любой точки данных, щелкнув любую из отдельных точек данных в любой из общих графиков.

|Графическое представления|Описание:|
|----|-----------|
|Локальная важность|Показывает первые K (с возможностью настройки K) важные функции для отдельного прогноза. Помогает проиллюстрировать локальное поведение базовой модели на определенной точке данных.|
|Исследование пертурбатион (анализ гипотетических)|Позволяет изменять значения компонентов выбранной точки данных и отслеживать итоговые изменения в прогнозируемом значении.|
|Отдельное условное ожидание (ICE)| Позволяет изменять значение компонента с минимального значения на максимальное значение. Помогает проиллюстрировать изменение прогноза точки данных при изменении компонента.|

[![Важность локального компонента панели мониторинга визуализации](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![Компонент панели мониторинга визуализации Пертурбатион](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![Графики панели мониторинга визуализации](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> Перед запуском ядра Jupyter убедитесь, что вы включили расширения мини-приложений для панели мониторинга визуализации.

* Записные книжки Jupyter

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* JupyterLab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Чтобы загрузить панель мониторинга визуализации, используйте следующий код.

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, dataset=x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Визуализация в Машинное обучение Azure Studio

Если вы завершите действия по [удаленной интерпретации](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) (Отправка созданного пояснения в журнал машинное обучение Azure выполнения), вы можете просмотреть панель мониторинга визуализации в [машинное обучение Azure Studio](https://ml.azure.com). Эта панель мониторинга представляет собой упрощенную версию панели мониторинга визуализации, описанную выше (пояснения исследования и построения неактивны, так как в Studio нет активных вычислений, которые могут выполнять вычисления в реальном времени).

Если доступны наборы данных, глобальные и локальные объяснения, данные заполняют все вкладки (кроме Пертурбатион исследования и ICE). Если доступно только глобальное объяснение, вкладка Сводка по важности и все локальные вкладки объяснения отключены.

Используйте один из этих путей для доступа к панели мониторинга визуализации в Машинное обучение Azure Studio:

* Панель " **эксперименты** " (Предварительная версия)
  1. Выберите **эксперименты** на левой панели, чтобы просмотреть список экспериментов, которые вы выполняли на машинное обучение Azure.
  1. Выберите конкретный эксперимент, чтобы просмотреть все запуски в этом эксперименте.
  1. Выберите выполнение, а затем на вкладке **пояснения** на панели мониторинга визуализации объяснение.

   [![Важность локального компонента панели мониторинга визуализации](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* Панель « **модели** »
  1. Если вы зарегистрировали исходную модель, выполнив действия, описанные в разделе [Развертывание моделей с помощью машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where), можно выбрать **модели** в левой области, чтобы просмотреть их.
  1. Выберите модель, а затем на вкладке **пояснения** Просмотрите панель мониторинга визуализации пояснения.

## <a name="interpretability-at-inference-time"></a>Возможные интерпретации во время вывода

Вы можете развернуть пояснение вместе с исходной моделью и использовать его во время вывода, чтобы предоставить отдельные значения важности функции (локальное объяснение) для новой новой точки. Мы также предлагаем более легкие дополнительные пояснения к оценке для повышения производительности интерпретации во время вывода. Процесс развертывания пояснения к более светлой оценке аналогичен развертыванию модели и включает следующие шаги:

1. Создайте объект пояснения. Например, можно использовать `TabularExplainer` :

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Создайте объяснение оценки с помощью объекта пояснения.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

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

1. В качестве дополнительного шага можно получить описание оценки из облака и протестировать объяснения.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Разверните образ в целевом объекте вычислений, выполнив следующие действия.

   1. При необходимости Зарегистрируйте исходную модель прогноза, выполнив действия, описанные в разделе [Развертывание моделей с помощью машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

   1. Создайте файл оценки.

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
   1. Определите конфигурацию развертывания.

         Эта конфигурация зависит от требований модели. В следующем примере определяется конфигурация, использующая одно ядро ЦП и один ГБ памяти.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Создайте файл с зависимостями окружения.

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

   1. Создайте пользовательский dockerfile с установленным параметром g + +.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Разверните созданный образ.
   
         Этот процесс занимает примерно пять минут.

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

1. Протестируйте развертывание.

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

1. Очистка.

   Для удаления развернутой веб-службы используйте `service.delete()`.

## <a name="next-steps"></a>Дальнейшие шаги

[Дополнительные сведения о интерпретируемости модели](how-to-machine-learning-interpretability.md)

[Ознакомьтесь с примерами Машинное обучение Azure интерпретируемые записные книжки](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

