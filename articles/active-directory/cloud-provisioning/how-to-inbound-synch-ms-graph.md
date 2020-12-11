---
title: Входящая синхронизация для подготовки облачных приложений с помощью MS API Graph
description: В этом разделе описано, как включить входящую синхронизацию, используя только API Graph
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c6ed106462d7dcac3f31a2ab157e5b634391006
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093457"
---
# <a name="inbound-synchronization-for-cloud-provisioning-using-ms-graph-api"></a>Входящая синхронизация для подготовки облачных приложений с помощью MS API Graph

В следующем документе описано, как реплицировать профиль синхронизации с нуля с помощью только API-интерфейсов Мсграф.  
Структура того, как это сделать, состоит из следующих шагов.  К ним относятся:

- [Базовая настройка](#basic-setup)
- [Создание субъектов-служб](#create-service-principals)
- [Создание задания синхронизации](#create-sync-job)
- [Обновление целевого домена](#update-targeted-domain)
- [Запуск задания синхронизации](#start-sync-job)
- [Состояние проверки](#review-status)

Используйте эти [модуль Microsoft Azure Active Directory для Windows PowerShell](https://docs.microsoft.com/powershell/module/msonline/) команды, чтобы включить синхронизацию для рабочего клиента — это предварительная версия для возможности вызова веб-службы администрирования для этого клиента.

## <a name="basic-setup"></a>Базовая настройка

### <a name="enable-tenant-flags"></a>Включить флаги клиента

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
Первая из этих двух команд требует Azure Active Directory учетных данных. Эти командлеты неявно указывают клиента и включают его для синхронизации.

## <a name="create-service-principals"></a>Создание субъектов-служб
Далее необходимо создать [приложение AD2AAD или субъект-службу](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) .

Необходимо использовать идентификатор приложения 1a4721b3-e57f-4451-ae87-ef078703ec94. DisplayName — это URL-адрес домена AD, если он используется на портале (например, contoso.com), но может называться что-то другое.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Создание задания синхронизации
Выходные данные приведенной выше команды будут возвращать идентификатор объекта созданного субъекта-службы. В этом примере objectId имеет значение 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  Используйте Microsoft Graph, чтобы добавить Синчронизатионжоб к этому субъекту-службе.  

Документацию по созданию задания синхронизации можно найти [здесь](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http).

Если вы не зарегистрировали идентификатор, указанный выше, можно найти субъект-службу, выполнив следующий вызов MS Graph. Для этого потребуется разрешение Directory. Read. ALL.
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Затем найдите имя приложения в выходных данных.

Выполните следующие две команды, чтобы создать два задания: одно для подготовки пользователей или групп, а другое для синхронизации хэша паролей. Это тот же запрос дважды, но с разными идентификаторами шаблонов.


Вызовите следующие два запроса:

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

Если вы хотите создать оба метода, потребуется два вызова.

Пример возвращаемого значения (для подготовки):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>Обновление целевого домена
Для этого клиента идентификатор объекта и идентификатор приложения субъекта-службы имеют следующий вид:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-C000-000000000000 DisplayName: testApp

Необходимо обновить домен, для которого предназначена эта конфигурация, поэтому обновите секреты для этого домена.

Убедитесь, что используемое доменное имя совпадает с URL-адресом, заданным для локального контроллера домена.

 ```
 PUT https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 Content-type: application/json
 {
  "value": [
    {
      "key": "Domain",
       {"value":[{"key":"Domain","value":"{\"domain\":\"[YOUR_DOMAIN_NAME]\"}"}]}
    }
  ]
 }
 ```

Ожидаемый ответ:... HTTP 204/нет содержимого

Здесь выделенное значение "домен" — это имя локального домена Active Directory, из которого должны быть подготовлены записи для Azure Active Directory.

## <a name="start-sync-job"></a>Запуск задания синхронизации
Задание можно получить снова с помощью следующей команды:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

Документацию по получению заданий можно найти [здесь](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-list?view=graph-rest-beta&tabs=http). 
 
Чтобы запустить задание, выдайте этот запрос, используя objectId субъекта-службы, созданного на первом шаге, и идентификатор задания, полученный из запроса, создавшего задание.

Документацию по запуску задания можно найти [здесь](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http). 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

Ожидаемый ответ:... HTTP 204/нет содержимого.

Другие команды для управления заданием описаны [здесь](https://docs.microsoft.com/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta).
 
Чтобы перезапустить задание, в одном из них будет использоваться...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Состояние проверки
Получение состояния задания с помощью...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

Просмотрите раздел "Status" возвращаемого объекта, чтобы получить соответствующие сведения.

## <a name="next-steps"></a>Дальнейшие действия 

- [What is Azure AD Connect cloud provisioning?](what-is-cloud-provisioning.md) (Что такое подготовка облака Azure AD Connect?)
- [Преобразования](how-to-transformation.md)
- [API синхронизации Azure AD](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
