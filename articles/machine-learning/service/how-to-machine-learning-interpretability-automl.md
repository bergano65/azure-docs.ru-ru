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
ms.openlocfilehash: 5bde67913bf5e23345974f52dd6a9a22e2dd4865
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158453"
---
# <a name="model-interpretability-in-automated-machine-learning"></a>Интерпретируемость модели в автоматизированном машинном обучении

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как включить функции интерпретации для автоматизированного машинного обучения (ML) в службе Машинное обучение Azure. Автоматизированное средство ML помогает понять важность необработанных и сконструированных функций. Чтобы использовать интерпретируемость модели, установите `model_explainability=True` в объекте `AutoMLConfig`.  

В этой статье раскрываются следующие темы:

- Выполняйте интерпретацию во время обучения для лучшей модели или любой модели.
- Включите визуализации, чтобы видеть закономерности в данных и объяснениях.
- Реализуйте интерпретируемость во время вывода или оценки.

## <a name="prerequisites"></a>предварительным требованиям

- Функции интерпретации. Запустите `pip install azureml-interpret azureml-contrib-interpret`, чтобы получить необходимые пакеты.
- Знание создания автоматических экспериментов ML. Дополнительные сведения об использовании пакета SDK для Машинное обучение Azure см. в руководстве по [модели регрессии](tutorial-auto-train-models.md) или о [настройке автоматизированных экспериментов ML](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Возможности интерпретации во время обучения для наилучшей модели

Извлеките пояснения из `best_run`, которое содержит пояснения для сконструированных функций и необработанных функций.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Скачивание характеристик сконструированной функции из хранилища артефактов

Вы можете использовать `ExplanationClient` для скачивания объяснений инженерных функций из хранилища артефактов `best_run`. Чтобы получить пояснения для необработанных функций, установленных `raw=True`.

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Возможности интерпретации во время обучения для любой модели 

При вычислении объяснений модели и визуализации их можно не ограничиваться описанием существующей модели для автоматизированной модели ML. Вы также можете получить описание модели с различными тестовыми данными. В этом разделе показано, как вычислить и визуализировать уровень важности функций и необработанное значение функции на основе тестовых данных.

### <a name="retrieve-any-other-automl-model-from-training"></a>Получение любой другой модели Аутомл из обучения

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="set-up-the-model-explanations"></a>Настройка объяснений модели

Используйте `automl_setup_model_explanations` для получения объяснений инженерных и необработанных функций. `fitted_model` может создавать следующие элементы:

- Рекомендуемые данные из обученных или тестовых примеров
- Списки имен компонентов, подготовленных и необработанных
- Искомые классы в столбце с меткой в сценариях классификации

`automl_explainer_setup_obj` содержит все структуры из списка выше.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Инициализация объяснения "имитировать" для важности функции

Чтобы создать пояснения для моделей Аутомл, используйте класс `MimicWrapper`. Мимиквраппер можно инициализировать с помощью следующих параметров:

- Объект настройки пояснения
- Рабочая область
- Модель LightGBM, которая выступает в качестве суррогата для `fitted_model` автоматизированной модели ML

Мимиквраппер также принимает объект `automl_run`, в который будут отправлены необработанные и инженерные объяснения.

```python
from azureml.interpret.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Использование Мимицексплаинер для вычисления и визуализации важности сконструированных функций

Вы можете вызвать метод `explain()` в Мимиквраппер с преобразованными примерами тестов, чтобы получить важность признаков для созданных сконструированных функций. Можно также использовать `ExplanationDashboard`, чтобы просмотреть панель мониторинга значения важности функций созданных сконструированных функций, автоматически феатуризерс ML.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Использование пояснения к процедуре "имитировать" для вычисления и визуализации необработанных важных компонентов

Вы можете снова вызвать метод `explain()` в Мимиквраппер с преобразованными примерами тестов и задать `get_raw=True`, чтобы получить важность признаков для функций RAW. Можно также использовать `ExplanationDashboard` для просмотра панели мониторинга значений важности функций необработанных компонентов.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Интерпретируемость во время вывода

В этом разделе вы узнаете, как эксплуатацию автоматизированную Модель ML с помощью объяснения, которое использовалось для расчета объяснений в предыдущем разделе.

### <a name="register-the-model-and-the-scoring-explainer"></a>Регистрация модели и пояснения к оценкам

Используйте `TreeScoringExplainer`, чтобы создать описание оценки, которое будет вычислять значения важности необработанных и инженерных функций во время определения. Вы инициализируйте пояснение к оценке с помощью `feature_map`, который был вычислен ранее. В пояснении к оценкам используется `feature_map` для возврата необработанного значения функции.

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

Затем создайте необходимые зависимости среды в контейнере для развернутой модели.

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
from azureml.core.model import InferenceConfig
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"}, 
                                               description='Get local explanations for Bank marketing test data')

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score_local_explain.py",
                                   conda_file="myenv.yml")

# Use configs and models generated above
service = Model.deploy(ws, 'model-scoring', [scoring_explainer_model, original_model], inference_config, aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Вывод с тестовыми данными

Вывод с некоторыми тестовыми данными для просмотра прогнозируемого значения из автоматизированной модели ML. Для прогнозируемого значения следует просмотреть важность функциональной функции для прогнозируемого значения и необработанной важности функции.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Визуализация для обнаружения закономерностей в данных и объяснениях во время обучения

Диаграмму важности функций можно визуализировать в рабочей области в [машинное обучение Azure Studio](https://ml.azure.com). После завершения автоматического запуска ML выберите **Просмотреть сведения о модели** , чтобы просмотреть конкретный запуск. Перейдите на вкладку **пояснения** , чтобы увидеть панель мониторинга визуализации пояснения.

[Архитектура интерпретации Машинное обучение ![](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о том, как включить пояснения к модели и важность признаков в областях пакета SDK Машинное обучение Azure, отличного от автоматизированного машинного обучения, см. в [статье о концепции, посвященной возможностям интерпретации](how-to-machine-learning-interpretability.md).
