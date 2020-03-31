---
title: Схема событийного события Azure Event Grid
description: Описывает свойства, которые предусмотрены для событий рабочего пространства машинного обучения с помощью Azure Event Grid
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 4051598a9abd787f6707e67a8c4dab12fc6d626a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79202150"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Схема событий Azure Event Grid для машинного обучения Azure

В этой статье представлены свойства и схема для событий рабочего пространства машинного обучения. Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).

Список примеров скриптов и учебников можно найти [в источнике событий AzureML.](event-sources.md#azure-machine-learning)

## <a name="available-event-types"></a>Доступные типы событий

Azure Machine Learning излучает следующие типы событий:

| Тип события | Описание |
| ---------- | ----------- |
| Microsoft.MachineLearningServices.ModelRegistered | Поднято, когда новая версия модели или модели была успешно зарегистрирована. |
| Microsoft.MachineLearningServices.ModelDeployed | Поднятый, когда модель (ы) были успешно развернуты в конечную точку. |
| Microsoft.MachineLearningServices.RunЗавершено | Поднятый, когда запуск успешно завершен. |
| Microsoft.MachineLearningServices.DatasetDriftDetected | Поднятый, когда монитор дрейфа dataset обнаруживает дрейф. |
| Microsoft.MachineLearningServices.RunStatusChanged | Поднят при изменении статуса запуска на 'неудавшийся'. |

## <a name="the-contents-of-an-event-response"></a>Содержание ответа на события

При срабатывании события служба Event Grid отправляет данные об этом событии в конечную точку.

В этом разделе содержится пример того, как будут выглядеть эти данные для каждого события.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Microsoft.MachineLearningServices.ModelRegistered событие

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Microsoft.MachineLearningServices.ModelDeployed событие

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Microsoft.MachineLearningServices.RunЗавершено событие

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Microsoft.MachineLearningServices.DatasetDriftDetected событие

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Microsoft.MachineLearningServices.RunStatusChanged событие

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    },
   "RunStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```




## <a name="event-properties"></a>Свойства события

Событие содержит следующие высокоуровневые данные:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| Раздел | строка | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | строка | Определенный издателем путь к субъекту событий. |
| eventType | строка | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | строка | Время создания события с учетом времени поставщика в формате UTC. |
| идентификатор | строка | Уникальный идентификатор события. |
| . | объект | Данные события хранилища BLOB-объектов. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства для каждого типа события:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft.MachineLearningServices.ModelRegistered

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| ModelName | строка | Название зарегистрированной модели. |
| МодельВерсия | строка | Версия модели, которая была зарегистрирована. |
| ModelTags | объект | Теги зарегистрированной модели. |
| МодельСвойства | объект | Свойства зарегистрированной модели. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft.MachineLearningServices.ModelDeployed

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| ServiceName | строка | Название развернутой службы. |
| ServiceComputeType | строка | Тип вычислений (например, ACI, AKS) развернутой службы. |
  | МоделиИ | строка | Запятая разделенный список идотов модели. Идобыты моделей, развернутых в службе. |
| ServiceTags | объект | Теги развернутой службы. |
| ServiceProperties | объект | Свойства развернутой службы. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft.MachineLearningServices.RunЗавершено

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| ЭкспериментId | строка | Идентификатор эксперимента, к которому принадлежит пробег. |
| ЭкспериментИмя | строка | Название эксперимента, к которого принадлежит пробег. |
| RunId | строка | Идентификатор запуска, который был завершен. |
| RunType | строка | Тип выполнения завершенного запуска. |
| RunTags | объект | Теги завершенного запуска. |
| RunProperties | объект | Свойства завершенного запуска. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft.MachineLearningServices.DatasetDriftDetected

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| DataDriftId | строка | Идентификатор монитора дрейфа данных, который вызвал событие. |
| DataDriftName | строка | Название монитора дрейфа данных, который вызвал событие. |
| RunId | строка | Идентификатор run, обнаруживаемый дрейф данных. |
| BaseDatasetId | строка | Идентификатор базового набора данных, используемый для обнаружения дрейфа. |
| TargetDatasetId | строка | Идентификатор целевого набора данных, используемый для обнаружения дрейфа. |
| ДрифтКокоэффициент | double | Результат коэффициента, который вызвал событие. |
| StartTime | DATETIME | Время начала целевых временных рядов набора данных, что привело к обнаружению дрейфа.  |
| EndTime | DATETIME | Время окончания целевых временных рядов набора данных, что привело к обнаружению дрейфа. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Microsoft.MachineLearningServices.RunStatusChanged

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| ЭкспериментId | строка | Идентификатор эксперимента, к которому принадлежит пробег. |
| ЭкспериментИмя | строка | Название эксперимента, к которого принадлежит пробег. |
| RunId | строка | Идентификатор запуска, который был завершен. |
| RunType | строка | Тип выполнения завершенного запуска. |
| RunTags | объект | Теги завершенного запуска. |
| RunProperties | объект | Свойства завершенного запуска. |
| РунСтатус | строка | Статус бега. |

## <a name="next-steps"></a>Дальнейшие действия

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Для получения дополнительной информации о создании подписки Azure Event Grid [см.](subscription-creation-schema.md)
* Для введения в использование Azure Event Grid с помощью машинного обучения Azure [см.](/azure/machine-learning/service/concept-event-grid-integration)
* Например, при использовании Azure Event Grid с помощью машинного обучения Azure [см.](/azure/machine-learning/service/how-to-use-event-grid)
