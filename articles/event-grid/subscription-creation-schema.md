---
title: Схема подписки для службы "Сетка событий Azure"
description: В этой статье описаны свойства для подписки на событие в службе "Сетка событий Azure".
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/02/2019
ms.author: babanisa
ms.openlocfilehash: 9464ab89e08f53f61cb6f5a4b1e91da35b785af0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822787"
---
# <a name="event-grid-subscription-schema"></a>Схема подписки для службы "Сетка событий"

Чтобы создать подписку для службы "Сетка событий", отправьте запрос на выполнение операции по созданию подписки на события. Используйте следующий формат:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Например, чтобы создать подписку на события для учетной записи хранения с именем `examplestorage` в группе ресурсов `examplegroup`, используйте следующий формат:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Имя подписки на события должны состоять из 3–64 символов и содержать только буквы ( a–z, A–Z), цифры (0–9) и дефис ("-"). В этой статье приведены свойства и схема для основного текста запроса.
 
## <a name="event-subscription-properties"></a>Свойства подписки на события

| Свойство | type | Описание |
| -------- | ---- | ----------- |
| destination | object | Объект, который определяет конечную точку. |
| filter | object | Необязательное поле для фильтрации событий по типам. |

### <a name="destination-object"></a>Объект destination

| Свойство | type | Описание |
| -------- | ---- | ----------- |
| endpointType | string | Тип конечной точки для подписки (веб-перехватчик или HTTP, концентратор событий либо очередь). | 
| endpointUrl | string | URL-адрес назначения для событий в подписке на событие. | 

### <a name="filter-object"></a>Объект filter

| Свойство | type | Описание |
| -------- | ---- | ----------- |
| includedEventTypes | массив | Выполняет сопоставление, если тип события, указанный в сообщении о событии, полностью соответствует одному из этих типов. Вызывает ошибку, если имя события не соответствует зарегистрированному имени типа для источника события. По умолчанию соответствует всем типам событий. |
| subjectBeginsWith | string | Фильтр соответствия префиксу для поля темы в сообщении о событии. Строка по умолчанию или пустая строка соответствует всем типам. | 
| subjectEndsWith | string | Фильтр соответствия суффиксу для поля темы в сообщении о событии. Строка по умолчанию или пустая строка соответствует всем типам. |
| isSubjectCaseSensitive | string | Управляет сопоставлением с учетом регистра в фильтрах. |


## <a name="example-subscription-schema"></a>Пример схемы подписки

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о [службе "Сетка событий Azure"](overview.md).