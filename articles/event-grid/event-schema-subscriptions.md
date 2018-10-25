---
title: Схема событий подписки для службы "Сетка событий Azure"
description: Описание свойств для событий подписки, используемых со службой "Сетка событий Azure"
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: reference
ms.date: 10/12/2018
ms.author: tomfitz
ms.openlocfilehash: ae6513c503b930d9c953f5245a9c98ea096109bb
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310244"
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Схема событий службы "Сетка событий Azure" для подписок

В этой статье описаны свойства и схема для событий подписки Azure. Введение в схемы событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).

Подписки и группы ресурсов Azure выдают одинаковые типы событий. Типы событий связаны с изменениями в ресурсах или действиями. Основное различие в том, что группы ресурсов выдают события для ресурсов в пределах группы ресурсов, а подписки Azure — для ресурсов во всей подписке.

События ресурса создаются для операций PUT, PATCH, POST и DELETE, которые отправляются в `management.azure.com`. Операции GET не создают события. Операции, передаваемые в плоскость данных (например, в `myaccount.blob.core.windows.net`), не создают события. События действий предоставляют данные о событиях для операций, таких как создание списка ключей для ресурса.

При оформлении подписки на события, связанные с подпиской Azure, ваша конечная точка получает все события для этой подписки. Это могут быть как события, которые вы хотите просмотреть, например обновление виртуальной машины, так и менее важные события, например появление новой записи в журнале развертывания. Вы можете получать все события в конечной точке и написать код, который обрабатывает нужные события. Кроме того, вы можете задать фильтр при создании подписки на события.

Для программной обработки события можно отсортировать по значению `operationName`. Например, конечная точка событий может обрабатывать только события для операций `Microsoft.Compute/virtualMachines/write` или `Microsoft.Storage/storageAccounts/write`.

Тема события — это идентификатор ресурса, который является целевым объектом операции. Для фильтрации событий ресурса укажите идентификатор ресурса при создании подписки на события. Чтобы выполнить фильтрацию по типу ресурсов, используйте значение в следующем формате: `/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

Список примеров сценариев и руководств см. в статье [Источники событий в службе "Сетка событий Azure"](event-sources.md#azure-subscriptions).

## <a name="available-event-types"></a>Доступные типы событий

Подписки Azure создают события управления из Azure Resource Manager, например при создании виртуальной машины или удалении учетной записи хранения.

| Тип события | ОПИСАНИЕ |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionCancel | Возникает при отмене выполнения действия с ресурсом. |
| Microsoft.Resources.ResourceActionFailure | Возникает при сбое выполнения действия с ресурсом. |
| Microsoft.Resources.ResourceActionSuccess | Возникает при успешном выполнении действия с ресурсом. |
| Microsoft.Resources.ResourceDeleteCancel | Возникает при отмене удаления. Это событие происходит при отмене развертывания шаблона. |
| Microsoft.Resources.ResourceDeleteFailure | Возникает при неудачном удалении. |
| Microsoft.Resources.ResourceDeleteSuccess | Возникает при успешном удалении. |
| Microsoft.Resources.ResourceWriteCancel | Возникает при отмене создания или обновления. |
| Microsoft.Resources.ResourceWriteFailure | Возникает при неудачном создании или обновлении. |
| Microsoft.Resources.ResourceWriteSuccess | Возникает при успешном создании или обновлении. |

## <a name="example-event"></a>Пример события

В следующем примере показана схема события **ResourceWriteSuccess**. Та же схема используется для событий **ResourceWriteFailure** и **ResourceWriteCancel** с разными значениями для `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

В следующем примере показана схема события **ResourceDeleteSuccess**. Та же схема используется для событий **ResourceDeleteFailure** и **ResourceDeleteCancel** с разными значениями для `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

В следующем примере показана схема события **ResourceActionSuccess**. Та же схема используется для событий **ResourceActionFailure** и **ResourceActionCancel** с разными значениями для `eventType`.

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "eventType": "Microsoft.Resources.ResourceActionSuccess",
  "eventTime": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}" 
}]
```

## <a name="event-properties"></a>Свойства события

Событие содержит следующие высокоуровневые данные:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| Раздел | строка | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | строка | Определенный издателем путь к субъекту событий. |
| eventType | строка | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | строка | Время создания события с учетом времени поставщика в формате UTC. |
| id | строка | Уникальный идентификатор события. |
| data | object | Данные события подписки. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| authorization | object | Запрошенная авторизация для операции. |
| claims | object | Свойства утверждений. Дополнительные сведения см. в [спецификациях JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | строка | Идентификатор операции для устранения неполадок. |
| httpRequest | object | Подробные сведения об операции. Этот объект включается только при обновлении существующего ресурса или при удалении ресурса. |
| resourceProvider | строка | Поставщик ресурсов для операции. |
| resourceUri | строка | URI ресурса в операции. |
| operationName | строка | Выполненная операция. |
| status | строка | Состояние операции. |
| subscriptionId | строка | Идентификатор подписки ресурса. |
| tenantId | строка | Идентификатор клиента ресурса. |

## <a name="next-steps"></a>Дополнительная информация

* Общие сведения о Сетке событий Azure см. в [этой статье](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
