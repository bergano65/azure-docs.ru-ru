---
title: Используйте REST для управления ресурсами ML
titleSuffix: Azure Machine Learning
description: Как использовать APIs REST для создания, запуска и удаления ресурсов Azure ML
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 419dbd998abc5cbd2da64a990e13d46f3fb2efbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77580634"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Создание, запуск и удаление ресурсов Azure ML с помощью REST

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Существует несколько способов управления ресурсами Azure ML. Вы можете использовать [портал,](https://portal.azure.com/) [интерфейс командной строки,](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)или [Python SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) Или вы можете выбрать API REST. REST API использует глаголы HTTP стандартным способом для создания, извлечения, обновления и удаления ресурсов. REST API работает с любым языком или инструментом, который может делать запросы HTTP. Простая структура REST часто делает его хорошим выбором в средах сценариев и для автоматизации MLOps. 

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Извлечение токена авторизации
> * Создание правильно отформатированный запрос REST с использованием основной аутентификации службы
> * Используйте запросы GET для получения информации об иерархических ресурсах Azure ML
> * Используйте запросы PUT и POST для создания и изменения ресурсов
> * Используйте запросы DELETE для очистки ресурсов 
> * Используйте авторизацию на основе ключей для оценки развернутых моделей

## <a name="prerequisites"></a>Предварительные требования

- **Подписка Azure,** на которую у вас есть административные права. Если у вас нет такой подписки, попробуйте [бесплатную или платную персональную подписку](https://aka.ms/AMLFree)
- [Рабочее пространство для машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- Административные запросы REST используют основную аутентификацию службы. Выполните последующие действия в [настройке аутентификации для ресурсов и рабочих процессов Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) для создания основного отдела службы в рабочем пространстве
- Утилита **завить.** Программа **curl** доступна в [подсистеме Windows для Linux](https://aka.ms/wslinstall/) или любого дистрибутива UNIX. В PowerShell, **локон** является псевдонимом для **Invoke-WebRequest** и `curl -d "key=val" -X POST uri` становится `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 

## <a name="retrieve-a-service-principal-authentication-token"></a>Извлечение токен основной аутентификации службы

Административные запросы REST аутентифицируются с помощью неявного потока OAuth2. В этом потоке проверки подлинности используется маркер, предоставленный директором службы подписки. Чтобы получить этот маркер, вам нужно:

- Идентификатор клиента (определение организации, к которой принадлежит ваша подписка)
- Идентификатор клиента (который будет связан с созданным токеном)
- Ваш секрет клиента (который вы должны сохранить)

Эти значения должны быть получены от ответа на создание основного обслуживания, о чем говорится в [настройке аутентификации для ресурсов и рабочих процессов Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) Если вы используете подписку на компанию, у вас может не быть разрешения на создание основного сервиса. В этом случае следует использовать [бесплатную или платную персональную подписку.](https://aka.ms/AMLFree)

Для получения маркера:

1. Откройте окно терминала.
1. Введите следующий код на командной строке
1. Замените свои собственные `{your-tenant-id}` `{your-client-id}`ценности `{your-client-secret}`для , и . На протяжении всей этой статьи строки, окруженные фигурными скобками, являются переменными, которые вам придется заменить своими собственными соответствующими значениями.
1. Выполните команду следующую команду.

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

Ответ должен обеспечить токен доступа, хороший в течение одного часа:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Обратите внимание на маркер, так как вы будете использовать его для проверки подлинности всех последующих административных запросов. Вы сделаете это, установив заголовок авторизации во всех запросах:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Обратите внимание, что значение начинается со строки "Bearer", включая одно пространство перед добавлением маркера.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Получить список групп ресурсов, связанных с подпиской

Чтобы получить список групп ресурсов, связанных с подпиской, запустите:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

В Azure публикуется множество aA-аДИ REST. Каждый поставщик услуг обновляет свой API на своей каденции, но делает это, не нарушая существующих программ. Поставщик услуг использует `api-version` аргумент для обеспечения совместимости. Аргумент `api-version` варьируется от обслуживания к службе. Например, для Службы машинного обучения `2019-11-01`текущая версия API . Для учетных записей `2019-06-01`хранения, это . Для ключевых хранилищ, это `2019-09-01`. Все вызовы REST `api-version` должны установить аргумент в ожидаемое значение. Вы можете положиться на синтаксис и семантику указанной версии, даже когда API продолжает развиваться. Если вы отправляете запрос поставщику без аргумента, `api-version` ответ будет содержать список поддерживаемых значений, читаемых человеком. 

Вышеупомянутый вызов приведет к уплотняемый jSON ответ формы: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Просверлить в рабочие области и их ресурсы

Для получения набора рабочих областей в группе ресурсов запустите `{your-subscription-id}`следующее, заменив `{your-resource-group}`и: `{your-access-token}` 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Снова вы получите список JSON, на этот раз содержащий список, каждый элемент которого подробно рабочей области:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Для работы с ресурсами в рабочей области вы переключитесь с общего **management.azure.com** сервера на сервер REST API, специфичный для расположения рабочего пространства. Обратите внимание на `discoveryUrl` значение ключа в вышеуказанном ответе JSON. Если вы получите этот URL- данный URL, вы получите ответ что-то вроде:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

Значение `api` ответа — это URL-адрес сервера, который вы будете использовать для дополнительных запросов. Для того чтобы перечислить эксперименты, например, отправьте следующую команду. Заменить `regional-api-server` значение `api` ответа (например, `centralus.api.azureml.ms`). Также `your-subscription-id`заменить `your-resource-group`, `your-access-token` , и, `your-workspace-name`как обычно:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Аналогичным образом, чтобы получить зарегистрированные модели в рабочей области, отправьте:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Обратите внимание, что для `history/v1.0` того, чтобы перечислить эксперименты, путь начинается с того времени, как перечислить модели, путь начинается с `modelmanagement/v1.0`. REST API разделен на несколько операционных групп, каждая из которых имеет определенный путь. В справочных документах API по ссылкам ниже перечисляются данные об операциях, параметрах и кодах реагирования для различных операций.

|Область|путь|Справочник|
|-|-|-|
|Артефакты|артефакт/v2.0/|[Справка по aPI REST](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|Хранилища данных|datastore/v1.0/|[Справка по aPI REST](https://docs.microsoft.com/rest/api/azureml/datastores)|
|Настройка гиперпараметров|гипердрайв/v1.0/|[Справка по aPI REST](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|Модели|моделирование/v1.0/|[Справка по aPI REST](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|Журнал выполнения|исполнение/v1.0/ и история/v1.0/|[Справка по aPI REST](https://docs.microsoft.com/rest/api/azureml/runs)|

Вы можете исследовать API REST, используя общую схему:

|Компонент URL|Пример|
|-|-|
| https://| |
| региональный апи-сервер/ | centralus.api.azureml.ms/ |
| операции-путь/ | история/v1.0/ |
| подписки/«Ваш-подписка-id»/ | подписка/abcde123-абаб-абаб-1234-0123456789abc/ |
| группы ресурсов/«Ваша ресурсная группа»/ | ресурсгруппы/MyResourceGroup/ |
| провайдеров/оператор-провайдер/ | провайдеры/Microsoft.MachineLearningServices/ |
| провайдер-ресурс-путь/ | рабочие области/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| операционная конечная точка/ | артефакты/метаданные/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Создание и изменение ресурсов с помощью запросов PUT и POST

Помимо поиска ресурсов с глаголом GET, REST API поддерживает создание всех ресурсов, необходимых для обучения, развертывания и мониторинга РЕШЕНИЙ ML. 

Обучение и запуск моделей ML требуют вычислительных ресурсов. Вы можете перечислить вычислительные ресурсы рабочего пространства с помощью: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Для создания или перезаписи названного вычислительного ресурса будет использоваться запрос PUT. В следующем, в дополнение к теперь `your-subscription-id`знакомые замены , `your-resource-group`, `your-workspace-name`, `location` `vmSize`и `vmPriority` `your-access-token` `scaleSettings`, `adminUserName`заменить `your-compute-name`, и значения для , , , и `adminUserPassword`. Как указано в справке на [Machine Learning Compute - Создать или обновить SDK Справка](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate), следующая команда создает выделенный, один узла Standard_D1 (основной cPU вычислительный ресурс), который будет сокращаться через 30 минут:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> В терминалах Windows при отправке данных JSON возможно, придется ускользать от символов двойной котировки. То есть, текст, такой как `"location"` становится `\"location\"`. 

Успешный запрос получит `201 Created` ответ, но обратите внимание, что этот ответ просто означает, что процесс подготовки начался. Вам нужно будет опросить (или использовать портал), чтобы подтвердить его успешное завершение.

### <a name="create-an-experimental-run"></a>Создание экспериментального запуска

Чтобы запустить запуск в рамках эксперимента, вам нужна папка на молнии, содержащая сценарий обучения и связанные с ними файлы, а также определение jSON. Папка на молнии должна иметь файл входа Python в корневом каталоге. В качестве примера, застегнуть тривиальную программу Python, такую как следующая, в папку под названием **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Сохранить этот следующий фрагмент как **definition.json**. Подтвердите значение "Сценарий" соответствует названию файла Python, который вы только что застегнули. Подтверждение значения "Цель" соответствует названию доступного вычислительного ресурса. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

Разместите эти `multipart/form-data` файлы на сервере с помощью содержимого:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Успешный запрос POST `200 OK` будет генерировать статус, с телом ответа, содержащим идентификатор созданного запуска:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

Вы можете контролировать запуск с помощью шаблона REST-ful, который теперь должен быть знаком:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Удаление ресурсов, которые вам больше не нужны

Некоторые, но не все ресурсы поддерживают глагол DELETE. Проверьте [справку API](https://docs.microsoft.com/rest/api/azureml/) перед фиксацией на REST API для удаления случаев использования. Для удаления модели, например, можно использовать:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Используйте REST для оценки развернутой модели

Хотя можно развернуть модель таким образом, чтобы она удостоверяла подлинность с помощью принципаслужбы обслуживания, большинство клиентских развертываний используют аутентификацию на основе ключей. Соответствующий ключ можно найти на странице развертывания в вкладке **Endpoints** Studio. То же место покажет ваш конечный балл URI. Входные данные модели должны быть смоделированы `data`как массив JSON под названием:

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Создание рабочего пространства с помощью REST 

Каждое рабочее пространство Azure ML имеет зависимость от четырех других ресурсов Azure: реестр контейнеров с включенным администрированием, хранилище ключей, ресурс Application Insights и учетная запись хранения. Невозможно создать рабочее пространство до тех пор, пока эти ресурсы не существуют. Проконсультируйтесь со ссылкой REST API для получения подробной информации о создании каждого такого ресурса.

Чтобы создать рабочее пространство, PUT вызов `management.azure.com`похож на следующий . Хотя этот вызов требует установить большое количество переменных, он структурно идентичен другим вызовам, которые обсуждала эта статья. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Вы должны `202 Accepted` получить ответ и, в возвращенных заголовках, `Location` URI. Вы можете получить эту URI для получения информации о развертывании, включая полезную информацию об отладке, если есть проблема с одним из зависимых ресурсов (например, если вы забыли включить доступ к админированию в реестре контейнеров). 

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="resource-provider-errors"></a>Ошибки поставщика ресурсов

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Перемещение рабочего пространства

> [!WARNING]
> Перемещение рабочего пространства Azure Machine Learning в другую подписку или перемещение подписки на новый арендатор не поддерживается. Это может привести к ошибкам.

### <a name="deleting-the-azure-container-registry"></a>Удаляние реестра контейнеров Azure

Рабочее пространство Azure Machine Learning использует реестр контейнеров Azure (ACR) для некоторых операций. Он автоматически создаст экземпляр ACR, когда он сначала нужен.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Дальнейшие действия

- Исследуйте полную [ссылку AzureML REST API.](https://docs.microsoft.com/rest/api/azureml/)
- Узнайте, как использовать Studio & Designer [для прогнозирования цены на автомобиль с дизайнером (предварительный просмотр).](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score)
- Исследуйте [машинное обучение Azure с помощью ноутбуков Jupyter.](https://docs.microsoft.com/azure//machine-learning/samples-notebooks)
