---
title: Интерпретируемость модели в автоматизированном машинном обучении
titleSuffix: Azure Machine Learning
description: Узнайте, как получить объяснения о том, как автоматизированная модель ML определяет важность функций и делает прогнозы при использовании пакета SDK для Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: b2c7825b10feab45df9cb89dbe2b82da1c143866
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129752"
---
# <a name="model-interpretability-in-automated-machine-learning"></a>Интерпретируемость модели в автоматизированном машинном обучении

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как включить функции интерпретации для автоматизированного машинного обучения (ML) в Машинное обучение Azure. Автоматизированное средство ML помогает понять важность созданной функции. 

Все версии пакета SDK после 1.0.85 устанавливаются `model_explainability=True` по умолчанию. В пакете SDK версии 1.0.85 и более ранних версий пользователям необходимо задать `model_explainability=True` в объекте `AutoMLConfig`, чтобы использовать интерпретируемость модели. 

Вы узнаете, как выполнять следующие задачи:

- Выполняйте интерпретацию во время обучения для лучшей модели или любой модели.
- Включите визуализации, чтобы видеть закономерности в данных и объяснениях.
- Реализуйте интерпретируемость во время вывода или оценки.

## <a name="prerequisites"></a>предварительные требования

- Функции интерпретации. Запустите `pip install azureml-interpret azureml-contrib-interpret`, чтобы получить необходимые пакеты.
- Знание создания автоматических экспериментов ML. Дополнительные сведения об использовании пакета SDK для Машинное обучение Azure см. в руководстве по [модели регрессии](tutorial-auto-train-models.md) или о [настройке автоматизированных экспериментов ML](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Возможности интерпретации во время обучения для наилучшей модели

Извлеките пояснения из `best_run`, которое содержит объяснения для сконструированных функций.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Скачивание характеристик сконструированной функции из хранилища артефактов

Вы можете использовать `ExplanationClient` для скачивания объяснений инженерных функций из хранилища артефактов `best_run`. 

```python
from azureml.explain.model._internal.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Возможности интерпретации во время обучения для любой модели 

При вычислении объяснений модели и визуализации их можно не ограничиваться описанием существующей модели для автоматизированной модели ML. Вы также можете получить описание модели с различными тестовыми данными. Действия, описанные в этом разделе, показывают, как вычислять и визуализировать уровень важности функций, основанных на тестовых данных.

### <a name="retrieve-any-other-automl-model-from-training"></a>Получение любой другой модели Аутомл из обучения

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Настройка объяснений модели

Используйте `automl_setup_model_explanations` для получения инженерных объяснений. `fitted_model` может создавать следующие элементы:

- Рекомендуемые данные из обученных или тестовых примеров
- Списки имен сконструированных функций
- Искомые классы в столбце с меткой в сценариях классификации

`automl_explainer_setup_obj` содержит все структуры из списка выше.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Инициализация объяснения "имитировать" для важности функции

Чтобы создать пояснения для моделей Аутомл, используйте класс `MimicWrapper`. Мимиквраппер можно инициализировать с помощью следующих параметров:

- Объект настройки пояснения
- Рабочая область
- Модель LightGBM, которая выступает в качестве суррогата для `fitted_model` автоматизированной модели ML

Мимиквраппер также принимает объект `automl_run`, в который будут отправлены инженерные объяснения.

```python
from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.explain.model.mimic_wrapper import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Использование Мимицексплаинер для вычисления и визуализации важности сконструированных функций

Вы можете вызвать метод `explain()` в Мимиквраппер с преобразованными примерами тестов, чтобы получить важность признаков для созданных сконструированных функций. Можно также использовать `ExplanationDashboard`, чтобы просмотреть панель мониторинга значения важности функций созданных сконструированных функций, автоматически феатуризерс ML.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="interpretability-during-inference"></a>Интерпретируемость во время вывода

В этом разделе вы узнаете, как эксплуатацию автоматизированную Модель ML с помощью объяснения, которое использовалось для расчета объяснений в предыдущем разделе.

### <a name="register-the-model-and-the-scoring-explainer"></a>Регистрация модели и пояснения к оценкам

Используйте `TreeScoringExplainer`, чтобы создать пояснение к оценке, которое будет вычислять значения важности функций во время вывода. Вы инициализируйте пояснение к оценке с помощью `feature_map`, который был вычислен ранее. 

Сохраните описание оценки, а затем зарегистрируйте модель и описание оценки в службе Управление моделями. Выполните следующий код:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Создание зависимостей conda для настройки службы

Затем создайте необходимые зависимости среды в контейнере для развернутой модели. Обратите внимание, что azureml — значения по умолчанию с версией > = 1.0.45 должны быть перечислены как зависимость PIP, так как она содержит функции, необходимые для размещения модели в качестве веб-службы.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>Развертывание службы

Разверните службу с помощью файла conda и файла оценки из предыдущих шагов.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Вывод с тестовыми данными

Вывод с некоторыми тестовыми данными для просмотра прогнозируемого значения из автоматизированной модели ML. Просмотр важности функциональной функции для прогнозируемого значения.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Визуализация для обнаружения закономерностей в данных и объяснениях во время обучения

Диаграмму важности функций можно визуализировать в рабочей области в [машинное обучение Azure Studio](https://ml.azure.com). После завершения автоматического запуска ML выберите **Просмотреть сведения о модели** , чтобы просмотреть конкретный запуск. Перейдите на вкладку **пояснения** , чтобы увидеть панель мониторинга визуализации пояснения.

[Архитектура интерпретации Машинное обучение ![](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о том, как включить пояснения к модели и важность признаков в областях пакета SDK Машинное обучение Azure, отличного от автоматизированного машинного обучения, см. в [статье о концепции, посвященной возможностям интерпретации](how-to-machine-learning-interpretability.md).
