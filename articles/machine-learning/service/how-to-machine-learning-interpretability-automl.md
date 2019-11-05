---
title: Интерпретируемость модели в автоматизированном ML
titleSuffix: Azure Machine Learning
description: Сведения о том, почему автоматизированная модель ML делает прогнозы с помощью пакета SDK для Машинное обучение Azure. Его можно использовать во время обучения и вывода, чтобы понять, как модель определяет важность функций и делает прогнозы.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 2c9df55eb319dd45281eca4684c79d83dc6ef933
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515334"
---
# <a name="model-interpretability-for-automated-ml-models"></a>Интерпретируемость модели для автоматизированных моделей ML

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этом пошаговом окне вы узнаете, как включить функцию интерпретации в автоматизированном машинном обучении с помощью службы Машинное обучение Azure. Автоматический ML позволяет понимать как необработанные, так и сконструированные значения функций. Чтобы использовать интерпретируемость модели, установите `model_explainability=True` в объекте `AutoMLConfig`.  

В этой статье вы узнаете о следующих задачах:

* Возможности интерпретации во время обучения для лучшей модели или любой модели
* Предоставление визуализаций для помощи в обнаружении закономерностей в данных и объяснениях
* Способ интерпретации во время вывода или оценки

## <a name="prerequisites"></a>Технические условия

* Запустите `pip install azureml-interpret azureml-contrib-interpret`, чтобы получить необходимые пакеты для функций интерпретации.
* В этой статье предполагается знание создания автоматических экспериментов ML. Дополнительные [сведения об использовании](how-to-configure-auto-train.md) автоматизированного языка ML в пакете SDK см. в этом [руководстве](tutorial-auto-train-models.md) .
 
## <a name="interpretability-during-training-for-the-best-model"></a>Возможности интерпретации во время обучения для наилучшей модели 

Извлеките пояснения из `best_run`, которое содержит пояснения для сконструированных функций и необработанных функций. 

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Скачивание характеристик сконструированной функции из хранилища артефактов

Вы можете использовать `ExplanationClient` для скачивания объяснений инженерных функций из хранилища артефактов best_run. Чтобы получить пояснения для необработанных функций, установленных `raw=True`. 

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Возможности интерпретации во время обучения для любой модели 

В этом разделе вы узнаете, как вычислить объяснения модели и визуализировать объяснения. Помимо получения описания существующей модели для автоматизированной модели ML, можно также объяснить модель с помощью различных тестовых данных. Следующие шаги позволяют вычислять и визуализировать уровень важности функций и необработанных данных для функций, основанных на тестовых данные.

### <a name="retrieve-any-other-automl-model-from-training"></a>Получение любой другой модели Аутомл из обучения

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="setup-the-model-explanations"></a>Настройка пояснений к модели

Fitted_model может создавать следующие элементы, которые будут использоваться для получения необработанных объяснений функций с помощью automl_setup_model_explanations:

* Признаками данные от обучения образцов и тестов
* Сбор списков имен компонентов, подготовленных и необработанных
* Поиск классов в столбце с меткой в сценариях классификации

Automl_explainer_setup_obj содержит все структуры из списка выше.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```
### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Инициализация объяснения "имитировать" для важности функции

Для объяснения моделей Аутомл используйте класс `MimicWrapper`. Мимиквраппер можно инициализировать с параметрами для объекта настройки пояснения, рабочей области и модели LightGBM, которая выступает в качестве суррогатной модели для объяснения автоматизированной модели ML (fitted_model здесь). Мимиквраппер также принимает объект automl_run, где будут отправляться необработанные и инженерные объяснения.

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

Метод объяснить () в Мимиквраппер можно вызвать с помощью преобразованных примеров тестов, чтобы получить важность признаков для созданных сконструированных функций. Вы также можете использовать Експланатиондашбоард для просмотра отображения значений важности функций созданных сконструированных функций с помощью автоматического феатуризерс ML.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```
### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Использование пояснения к процедуре "имитировать" для вычисления и визуализации необработанных важных компонентов

Метод объяснить () в Мимиквраппер можно снова вызвать с преобразованными примерами тестов и установить для `get_raw` значение true, чтобы получить важность функции для необработанных функций. Експланатиондашбоард можно также использовать для просмотра панели мониторинга значений важности функций необработанных компонентов.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Интерпретируемость во время вывода

В этом разделе вы узнаете, как эксплуатацию автоматизированную Модель ML с помощью пояснения, которое использовалось для вычислить объяснения в предыдущем разделе.

### <a name="register-the-model-and-the-scoring-explainer"></a>Регистрация модели и пояснения к оценкам

Используйте `TreeScoringExplainer`, чтобы создать пояснение к оценке, которое будет использоваться для вычисления необработанных и сконструированных значений важности функций во время вывода. Обратите внимание, что вы инициализируем объяснение оценки с feature_map, который был вычислен ранее. Feature_map будет использоваться объяснением оценки, чтобы вернуть необработанную важность функции.

В приведенном ниже коде вы сохраняете пояснение к оценке и регистрируете модель и описание оценки с помощью службы Управление моделями.

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

Далее вы создадите зависимости среды, необходимые в контейнере для развернутой модели.

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

### <a name="inference-using-test-data"></a>Вывод с помощью тестовых данных

Вывод с использованием некоторых тестовых данных для просмотра прогнозируемого значения из автоматизированной модели ML и просмотра важности функциональной функции для прогнозируемого значения и важности необработанных функций для прогнозируемого значения.

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

### <a name="visualizations-to-aid-you-in-the-discovery-of-patterns-in-data-and-explanations-at-training-time"></a>Визуализации, помогающие в обнаружении закономерностей в данных и объяснениях во время обучения

Вы также можете визуализировать диаграмму важности функций в рабочей области в [машинное обучение Azure Studio](https://ml.azure.com). После завершения автоматического запуска ML необходимо щелкнуть "просмотреть сведения о модели", чтобы перейти к конкретному выполнению. Отсюда вы щелкните вкладку "объяснения", чтобы увидеть панель мониторинга визуализации. 

[Архитектура интерпретации Машинное обучение ![](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о том, как пояснения к модели и важность признаков можно включить в других областях пакета SDK за пределами автоматизированного машинного обучения, см. в статье [концепции](how-to-machine-learning-interpretability.md) , посвященной возможностям интерпретации.