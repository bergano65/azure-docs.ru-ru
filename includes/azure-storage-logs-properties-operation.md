---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 0e23e537043664929bdc1a3636de359953b66db6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711404"
---
| Свойство | Описание |
|:--- |:---|
|**time** | Время получения запроса хранилищем (время в формате UTC). Например: `2018/11/08 21:09:36.6900118`.|
|**resourceId** | Идентификатор ресурса учетной записи хранения. Например: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | Категория запрошенной операции. Например, `StorageRead`, `StorageWrite`или `StorageDelete`.|
|**operationName** | Тип выполненной операции REST. <br> Полный список операций см. на странице [Операции с протоколированием и сообщения о состоянии Аналитики Службы хранилища](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | Версия службы хранилища, указанная при осуществлении запроса. Эквивалентна значению заголовка **x-ms-version**. Например: `2017-04-17`.|
|**schemaVersion** | Версия схемы журнала. Например: `1.0`.|
|**statusCode** | Код состояния HTTP для запроса. Если запрос будет прерван, может быть задано значение `Unknown`. <br> Например: `206` |
|**statusText** | Состояние запрошенной операции.  Полный список сообщений о состоянии см. на странице [Операции с протоколированием и сообщения о состоянии Аналитики Службы хранилища](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). В версии 2017-04-17 и более поздних сообщение о состоянии `ClientOtherError` не используется. Вместо этого поле содержит код ошибки. Например: `SASSuccess`  |
|**durationMs** | Общее время выполнения запрошенной операции в миллисекундах. Это значение включает время чтения входящего запроса и отправки ответа инициатору запроса. Например: `12`.|
|**callerIpAddress** | IP-адрес инициатора запроса, включая номер порта. Например: `192.100.0.102:4362`. |
|**correlationId** | Идентификатор, используемый для сопоставления журналов между ресурсами. Например: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**расположение** | Расположение учетной записи хранения. Например: `North Europe`. |
|**protocol**|Протокол, используемый в операции. Например, `HTTP`, `HTTPS`, `SMB`или `NFS`.|
| **uri** | Запрашиваемый универсальный идентификатор ресурса. |