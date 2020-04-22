---
title: Интерпретируйте & объясните модели ML в Python
titleSuffix: Azure Machine Learning
description: Узнайте, как получить объяснения того, как модель машинного обучения определяет важность функции и делает прогнозы при использовании SDK Для машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: Luis.Quintanilla
ms.date: 04/12/2020
ms.openlocfilehash: 45eef976fe10bbb5acda2cd348a77b28c3ffbe02
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769808"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python"></a>Используйте пакет интерпретаций для объяснения моделей ML & прогнозы в Python

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этом руководстве вы научитесь использовать пакет интерпретаций Azure Machine Learning Python SDK для выполнения следующих задач:


* Объясните все поведение модели или индивидуальные прогнозы на личной машине локально.

* Включить методы интерпретации инженерных функций.

* Объясните поведение всей модели и индивидуальных прогнозов в Azure.

* Используйте панель мониторинга визуализации для взаимодействия с объяснениями модели.

* Развертывание скоринга объяснить вместе с вашей моделью для наблюдения объяснений во время выводов.



Для получения дополнительной информации об поддерживаемых методах интерпретации и моделях машинного обучения [см.](how-to-machine-learning-interpretability.md) [sample notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Создание значения значения значения функции на персональной машине 
Ниже приводится следующий пример, как использовать пакет интерпретаций на личной машине, не связываясь с службами Azure.

1. Установка `azureml-interpret` `azureml-contrib-interpret` и пакеты.
    ```bash
    pip install azureml-interpret
    pip install azureml-contrib-interpret
    ```

2. Обучите образец модели в местном блокноте Jupyter.

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

3. Позвоните в объяснительную по-местному месту.
   * Чтобы инициализировать объект-объяснитель, передайте модель и некоторые обучающие данные конструктору.
   * Чтобы сделать ваши объяснения и визуализации более информативными, вы можете передать имена функций и имена выходных классов при выполнении классификации.

   Следующие блоки кода показывают, как мгновенно объяснить `TabularExplainer` `MimicExplainer`объект `PFIExplainer` с , и локально.
   * `TabularExplainer`вызывает один из трех объяснений SHAP `DeepExplainer`под `KernelExplainer`(,`TreeExplainer`или ).
   * `TabularExplainer`автоматически выбирает наиболее подходящий для вашего случая использования, но вы можете позвонить каждому из трех его основных объяснений напрямую.

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

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Объясните поведение всей модели (глобальное объяснение) 

Обратитесь к следующему примеру, чтобы помочь вам получить агрегированные (глобальные) значения значений характеристик.

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

### <a name="explain-an-individual-prediction-local-explanation"></a>Объясните индивидуальный прогноз (местное объяснение)
Получите значения индивидуальной важности объектов различных точек данных, вызывая объяснения для отдельного экземпляра или группы экземпляров.
> [!NOTE]
> `PFIExplainer`не поддерживает местные объяснения.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Необработанные преобразования функций

Вы можете выбрать, чтобы получить объяснения с точки зрения сырья, непреобразованные функции, а не инженерии функций. Для этого параметра вы передаете конвейер преобразования `train_explain.py`функций в пояснительное для вас. В противном случае, объяснение дает объяснения с точки зрения инженерных функций.

Формат поддерживаемых преобразований такой же, как описано в [sklearn-панды](https://github.com/scikit-learn-contrib/sklearn-pandas). Как правило, любые преобразования поддерживаются до тех пор, пока они работают на одной колонке, так что ясно, что они один к многим.

Получите объяснение необработанных `sklearn.compose.ColumnTransformer` функций с помощью или со списком установленных трансформаторных tuples. В следующем `sklearn.compose.ColumnTransformer`примере используется .

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

В случае, если вы хотите запустить пример со списком установленных трансформаторных подрастаний, используйте следующий код:

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>Создание значений значения значений функций с помощью удаленных запусков

В следующем примере показано, `ExplanationClient` как можно использовать класс для интерпретации модели для удаленных запусков. Он концептуально похож на локальный процесс, кроме вас:

* Используйте `ExplanationClient` в удаленном запуске для загрузки контекста интерпретации.
* Скачать контекст позже в локальной среде.

1. Установка `azureml-interpret` `azureml-interpret-contrib` и пакеты.
    ```bash
    pip install azureml-interpret
    pip install azureml-interpret-contrib
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

1. Назначьте azure Machine Learning Compute в качестве цели вычислений и отправьте свой учебный забег. Просмотреть [настройку вычислительных целей для обучения модели](how-to-set-up-training-targets.md#amlcompute) для инструкций. Вы также можете найти [пример ноутбуков](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) полезным.

1. Загрузите объяснение в местном блокноте Jupyter.

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

После загрузки объяснений в локальном блокноте Jupyter можно использовать панель мониторинга визуализации для понимания и интерпретации модели.

### <a name="understand-entire-model-behavior-global-explanation"></a>Понять поведение всей модели (глобальное объяснение) 

Следующие сюжеты обеспечивают общее представление об обученной модели вместе с ее прогнозами и объяснениями.

|Печати|Описание|
|----|-----------|
|Исследование данных| Отображает обзор набора данных вместе со значениями прогнозирования.|
|Глобальное значение|Агрегаты имеют значения значений отдельных точек данных, чтобы показать общие топ K модели (настраиваемый K) важные функции. Помогает понять общее поведение базовой модели.|
|Объяснение Исследования|Демонстрирует, как функция влияет на изменение значений прогнозирования модели или вероятность значений прогнозирования. Показывает влияние взаимодействия функций.|
|Резюме Значение|Использует индивидуальные значения значений значений объектов во всех точках данных, чтобы показать распределение влияния каждой функции на значение прогнозирования. Используя эту диаграмму, вы исследуете, в каком направлении значение объекта влияет на значения прогнозирования.
|

[![Визуализация панели мониторинга Глобальный](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Понимание индивидуальных прогнозов (местное объяснение) 

Вы можете загрузить отдельный участок важности объекта для любой точки данных, нажав на любую из отдельных точек данных в любом из общих участков.

|Печати|Описание|
|----|-----------|
|Местное значение|Показывает верхние K (настраиваемый K) важные функции для индивидуального прогнозирования. Помогает проиллюстрировать локальное поведение базовой модели на определенной точке данных.|
|Исследование возмущения (что, если анализ)|Позволяет износить значения выбранной точки данных и наблюдать за полученными изменениями в значении прогнозирования.|
|Индивидуальные условные ожидания (ICE)| Позволяет изменить значение объекта от минимального значения к максимальному. Помогает проиллюстрировать, как изменяется точка данных при изменении объекта.|

[![Элементарной панели мониторинга локальной важности функций](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![Возмущение функции визуализации панели мониторинга](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![Визуализация Dashboard ICE Участки](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> Перед запуском ядра Jupyter убедитесь, что вы включите расширения виджетов для панели визуализации.

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

Для загрузки приборной панели визуализации используйте следующий код.

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Визуализация в студии машинного обучения Azure

При завершении удаленных шагов [интерпретации](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) (загрузка сгенерированного объяснения в Azure Machine Learning Run History) можно просмотреть панель мониторинга визуализации в [студии Azure Machine Learning.](https://ml.azure.com) Эта панель мониторинга является более простой версией панели мониторинга визуализации, изложенной выше (исследования объяснений и ICE-сюжеты отключены, так как в студии нет активных вычислений, которые могут выполнять их вычисления в реальном времени).

При наличии набора данных, глобальных и локальных объяснений данные заполняют все вкладки (кроме исследования возмущения и ICE). Если доступно только глобальное объяснение, вкладка «Резюме важности» и все локальные вкладки-объяснения отключены.

Следуйте по одному из этих путей, чтобы получить доступ к панели визуализации в студии Машинного обучения Azure:

* **Панель экспериментов** (Предварительный просмотр)
  1. Выберите **эксперименты** в левом стеле, чтобы увидеть список экспериментов, которые вы проводили на Azure Machine Learning.
  1. Выберите конкретный эксперимент для просмотра всех запусков в этом эксперименте.
  1. Выберите запуск, а затем вкладку **«Объяснения»** на панели визуализации объяснения.

   [![Элементарной панели мониторинга локальной важности функций](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* **Панели моделей**
  1. Если вы зарегистрировали исходную модель, выдвинув шаги в [моделях Deploy с помощью Azure Machine Learning,](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)вы можете выбрать **модели** в левом стене для ее просмотра.
  1. Выберите модель, а затем вкладку **«Объяснения»** для просмотра панели визуализации объяснения.

## <a name="interpretability-at-inference-time"></a>Интерпретация во время выводов

Вы можете развернуть объяснение вместе с исходной моделью и использовать его в момент выводов, чтобы обеспечить индивидуальные значения значения характеристик (местное объяснение) для новой новой точки данных. Мы также предлагаем более легкий вес скоринга объяснить для повышения производительности интерпретации на момент выводов. Процесс развертывания более легкого скоринга объясняется для развертывания модели и включает в себя следующие шаги:

1. Создайте объект объяснения. Например, можно `TabularExplainer`использовать:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Создайте объяснение скоринга с помощью объекта-объяснения.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Назначайте и регистрируйте изображение, использующее модель объяснения скоринга.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. В качестве дополнительного шага можно получить объяснение скоринга из облака и протестировать объяснения.

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

1. Развертывание изображения в вычислительную цель, выполняя следующие действия:

   1. При необходимости зарегистрируйте исходную модель прогнозирования, выдвинув следующие шаги в [моделях Deploy с помощью Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)

   1. Создайте скоринговый файл.

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

         Эта конфигурация зависит от требований модели. Следующий пример определяет конфигурацию, используюую одно ядро процессора и один ГБ памяти.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Создайте файл с зависимостями среды.

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

   1. Создайте пользовательский докерфайл с установленным g'.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Развертывание созданного изображения.
   
         Этот процесс занимает около пяти минут.

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

## <a name="next-steps"></a>Следующие шаги

[Подробнее о интерпретации модели](how-to-machine-learning-interpretability.md)

[Ознакомьтесь с образцами ноутбуков, протекающих из образцов интерпретации машинного обучения Azure](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

