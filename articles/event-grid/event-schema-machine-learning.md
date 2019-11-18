---
title: Схема событий Машинное обучение службы "Сетка событий Azure"
description: Описание свойств, предоставляемых для событий рабочей области Машинное обучение с помощью службы "Сетка событий Azure"
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 5f2d23b3fe33691d37dc00b2d4e79036293252d9
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132872"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Схема событий службы "Сетка событий Azure" для Машинное обучение Azure

Эта статья содержит свойства и схему для событий рабочей области машинного обучения. Основные сведения о схеме событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).

Список примеров сценариев и учебников см. в статье [источник событий AzureML](event-sources.md#azure-machine-learning).

## <a name="available-event-types"></a>Доступные типы событий

Машинное обучение Azure выдает следующие типы событий:

| Тип события | ОПИСАНИЕ |
| ---------- | ----------- |
| Microsoft. Мачинелеарнингсервицес. Моделрегистеред | Возникает при успешной регистрации новой модели или версии модели. |
| Microsoft. Мачинелеарнингсервицес. Моделдеплойед | Возникает при успешном развертывании моделей в конечной точке. |
| Microsoft. Мачинелеарнингсервицес. Рункомплетед | Возникает после успешного завершения выполнения. |
| Microsoft. Мачинелеарнингсервицес. Датасетдрифтдетектед | Возникает, когда монитор смещения набора данных обнаруживает смещение. |

## <a name="the-contents-of-an-event-response"></a>Содержимое ответа на событие

При активации события служба службы "Сетка событий" отправляет данные об этом событии в подписку на конечную точку.

В этом разделе содержится пример того, как будут выглядеть данные для каждого события.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Событие Microsoft. Мачинелеарнингсервицес. Моделрегистеред

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

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Событие Microsoft. Мачинелеарнингсервицес. Моделдеплойед

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

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Событие Microsoft. Мачинелеарнингсервицес. Рункомплетед

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

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Событие Microsoft. Мачинелеарнингсервицес. Датасетдрифтдетектед

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

## <a name="event-properties"></a>Свойства событий

Событие содержит следующие данные верхнего уровня:

| Свойство | введите | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| Раздел | строка | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | строка | Определенный издателем путь к субъекту событий. |
| eventType | строка | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | строка | Время создания события с учетом времени поставщика в формате UTC. |
| id | строка | Уникальный идентификатор события. |
| data | object | Данные события хранилища BLOB-объектов. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства для каждого типа событий:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft. Мачинелеарнингсервицес. Моделрегистеред

| Свойство | введите | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| ModelName | строка | Имя зарегистрированной модели. |
| моделверсион | int | Версия зарегистрированной модели. |
| моделтагс | object | Теги зарегистрированной модели. |
| моделпропертиес | object | Свойства зарегистрированной модели. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft. Мачинелеарнингсервицес. Моделдеплойед

| Свойство | введите | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| ServiceName | строка | Имя развернутой службы. |
| сервицекомпутетипе | строка | Тип вычислений (например, ACI, AKS) развернутой службы. |
  | моделидс | строка | Разделенный запятыми список идентификаторов моделей. Идентификаторы моделей, развернутых в службе. |
| сервицетагс | object | Теги развернутой службы. |
| сервицепропертиес | object | Свойства развернутой службы. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft. Мачинелеарнингсервицес. Рункомплетед

| Свойство | введите | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| експериментид | строка | ИДЕНТИФИКАТОР эксперимента, к которому относится выполнение. |
| експериментнаме | строка | Имя эксперимента, которому принадлежит выполнение. |
| RunId | строка | Идентификатор завершенного запуска. |
| рунтипе | строка | Тип запуска завершенного выполнения. |
| рунтагс | object | Теги завершенного выполнения. |
| рунпропертиес | object | Свойства завершенного выполнения. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft. Мачинелеарнингсервицес. Датасетдрифтдетектед

| Свойство | введите | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| датадрифтид | строка | ИДЕНТИФИКАТОР монитора смещения данных, вызвавшего событие. |
| датадрифтнаме | строка | Имя монитора смещения данных, вызвавшего событие. |
| RunId | строка | Идентификатор запуска, который обнаружил отклонение данных. |
| баседатасетид | строка | Идентификатор базового набора данных, используемый для обнаружения смещения. |
| таржетдатасетид | строка | Идентификатор целевого набора данных, используемый для обнаружения смещения. |
| дрифткоеффиЦиент | Double | Результат коэффициента, вызвавшего событие. |
| StartTime | datetime; | Время начала временного ряда целевого набора данных, которое привело к обнаружению отклонения.  |
| EndTime | datetime; | Время окончания временного ряда набора данных, которое привело к обнаружению рассмещений. |


## <a name="next-steps"></a>Дополнительная информация

* Общие сведения о службе "Сетка событий Azure" см. в [этой статье](overview.md).
* Дополнительные сведения о создании подписки на службу "Сетка событий Azure" см. в разделе [Схема подписки службы "Сетка событий](subscription-creation-schema.md) ".
* Общие сведения об использовании службы "Сетка событий Azure" с Машинное обучение Azure см. в статье Использование [событий машинное обучение Azure](/azure/machine-learning/service/concept-event-grid-integration) .
* Пример использования службы "Сетка событий Azure" с Машинное обучение Azure см. в статье [создание рабочих процессов машинного обучения на основе событий](/azure/machine-learning/service/how-to-use-event-grid) .
