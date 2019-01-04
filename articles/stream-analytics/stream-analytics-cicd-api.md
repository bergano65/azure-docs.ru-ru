---
title: Реализация CI/CD для Azure Stream Analytics с использованием REST API
description: Сведения о реализации конвейера непрерывной интеграции и развертывании для Azure Stream Analytics с использованием REST API.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 727c1ce79fbd20252f7455f95ad00a6c44d67c65
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090522"
---
# <a name="implement-cicd-for-stream-analytics-using-apis"></a>Реализация CI/CD для Stream Analytics с использованием API-интерфейсов

Вы можете включить непрерывную интеграцию и развертывание для заданий Azure Stream Analytics с помощью REST API. Эта статья содержит примеры и способы использования нужных API-интерфейсов. Интерфейсы REST API не поддерживаются в службе Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Вызов API-интерфейсов из различных сред

Интерфейсы REST API могут вызываться из Windows и Linux. В следующих командах продемонстрирован правильный синтаксис вызова API-интерфейсов. Использование определенных API-интерфейсов будет показано в следующих разделах этой статьи.

### <a name="linux"></a>Linux

Для Linux можно использовать команды `Curl` или `Wget`:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” { <url> }   
```

```bash
wget -q -O- --{ <method> }-data="<request body>”--header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a> Windows

Для Windows используйте Powershell: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url>-Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Создание задания ASA с помощью Edge 
 
Чтобы создать задание Stream Analytics, вызовите метод PUT с помощью API-интерфейса Stream Analytics.

|Метод|Request URL (URL-адрес запроса)|
|------|-----------|
|ОТПРАВКА|https://management.azure.com/subscriptions/{**идентификатор_подписки**}/resourcegroups/{**имя_группы_ресурсов**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**имя_задания**}?api-version=2017-04-01-preview|
 
Пример использования команды **curl**:

```curl
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Пример текста запроса в формате JSON:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
Дополнительные сведения см. в [документации по API-интерфейсам](/rest/api/streamanalytics/stream-analytics-job).  
 
## <a name="publish-edge-package"></a>Публикация пакета Edge 
 
Чтобы опубликовать задание Stream Analytics в IoT Edge, вызовите метод POST с помощью API-интерфейса Edge Package Publish.

|Метод|Request URL (URL-адрес запроса)|
|------|-----------|
|ПУБЛИКАЦИЯ|https://management.azure.com/subscriptions/{**идентификатор_подписки**}/resourceGroups/{**имя_группы_ресурсов**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**имя_задания**}/publishedgepackage?api-version=2017-04-01-preview|

Эта асинхронная операция возвращает состояние 202 до того, как задание будет успешно опубликовано. Заголовок ответа расположения содержит универсальный код ресурса (URI), который используется для получения состояния процесса. Во время выполнения процесса вызов универсального кода ресурса (URI) в заголовке расположения возвращает состояние 202. После того как процесс завершится, универсальный код ресурса (URI) возвращает состояние 200. 

Пример вызова публикации пакета Edge с помощью команды **curl**: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
После отправки POST-запроса ожидается пустой текст ответа. Найдите URL-адрес, расположенный в заголовке ответа, и запишите его для дальнейшего использования.
 
Пример URL-адреса из заголовка ответа:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Подождите одну-две минуты, прежде чем запускать следующую команду, чтобы выполнить вызов API с помощью URL-адреса, который вы взяли из заголовка ответа. Повторите запрос, если не удалось получить ответ 200.
 
Пример вызова API с возвращаемым URL-адресом с помощью команды **curl**:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

Ответ включает сведения, которые необходимо добавить в скрипт развертывания Edge. В примерах ниже показаны сведения, которые вам необходимо собрать, а также места их вставки в манифесте развертывания.
 
Пример текста ответа после успешной публикации:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}",”PublishTimeStamp”:”{publishtimestamp}”}}}}" 
  status : "Succeeded" 
} 
```

Пример манифеста развертывания: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

После того как вы настроите манифест развертывания, ознакомьтесь со статьей о [развертывании модулей Azure IoT Edge с помощью Azure CLI](/iot-edge/how-to-deploy-modules-cli.md), чтобы приступить к развертыванию.


## <a name="next-steps"></a>Дополнительная информация 
 
* [Azure Stream Analytics в IoT Edge (предварительная версия)](stream-analytics-edge.md)
* [Deploy Azure Stream Analytics as an IoT Edge module - preview](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) (Развертывание Azure Stream Analytics в качестве модуля IoT Edge (предварительная версия))
* [Разработка заданий Edge Stream Analytics с помощью средств Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
