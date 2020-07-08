---
title: Служба приложений Azure в качестве источника Сетки событий
description: В этой статье описывается, как использовать Службу приложений Azure в качестве источника событий Сетки событий. Она содержит схему и ссылки на учебники и практические руководства.
services: event-grid
author: jasonfreeberg
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: jafreebe
ms.openlocfilehash: 0a24e8ba84739dbc1b5de5e0546a8fe0d2e826f1
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650697"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Служба приложений Azure в качестве источника Сетки событий

В этой статье описаны свойства и схема для событий Службы приложений Azure. Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md). Здесь также приводится список кратких руководств и учебников по использованию Службы приложений Azure в качестве источника событий.

## <a name="event-grid-event-schema"></a>Схема событий службы "Сетка событий Azure"

### <a name="available-event-types"></a>Доступные типы событий

Служба приложений Azure выдает приведенные ниже типы событий.

|    Тип события                                             |    Описание                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft.Web/sites.BackupOperationStarted             |    Активируется при запуске резервного копирования.                             |
|    Microsoft.Web/sites.BackupOperationCompleted           |    Активируется при завершении резервного копирования.                           |
|    Microsoft.Web/sites.BackupOperationFailed              |    Активируется при сбое резервного копирования.                              |
|    Microsoft.Web/sites.RestoreOperationStarted            |    Активируется при запуске восстановления из резервной копии.        |
|    Microsoft.Web/sites.RestoreOperationCompleted          |    Активируется при завершении восстановления из резервной копии.      |
|    Microsoft.Web/sites.RestoreOperationFailed             |    Активируется при сбое восстановления из резервной копии.         |
|    Microsoft.Web/sites.SlotSwapStarted                    |    Активируется при запуске переключения слотов.                          |
|    Microsoft.Web/sites.SlotSwapCompleted                  |    Активируется при завершении переключения слотов.                      |
|    Microsoft.Web/sites.SlotSwapFailed                     |    Активируется при сбое переключения слотов.                           |
|    Microsoft.Web/sites.SlotSwapWithPreviewStarted         |    Активируется при запуске переключения слотов с предварительным просмотром.           |
|    Microsoft.Web/sites.SlotSwapWithPreviewCancelled       |    Активируется при отмене переключения слотов с предварительным просмотром.    |
|    Microsoft.Web/sites.AppUpdated.Restarted               |    Активируется при перезапуске сайта.                      |
|    Microsoft.Web/sites.AppUpdated.Stopped                 |    Активируется при остановке сайта.                          |
|    Microsoft.Web/sites.AppUpdated.ChangedAppSettings      |    Активируется при изменении параметров приложения сайта.             |
|    Microsoft.Web/serverfarms.AppServicePlanUpdated        |    Активируется при обновлении плана службы приложений.                 |

### <a name="the-contents-of-an-event-response"></a>Содержимое ответа на событие

При активации события служба Сетки событий отправляет данные о нем на подписанную конечную точку.
В этом разделе приведен пример того, как будут выглядеть данные для каждого события. Каждое событие содержит приведенные ниже данные верхнего уровня.

|     Свойство          |     Тип     |     Описание                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    Раздел              |    строка    |    Полный путь к ресурсу источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий".                                      |
|    subject            |    строка    |    Определенный издателем путь к субъекту события.                                                                                              |
|    eventType          |    строка    |    Один из зарегистрированных типов событий для этого источника событий.                                                                                  |
|    eventTime          |    строка    |    Время создания события с учетом времени поставщика в формате UTC.                                                                         |
|    идентификатор                 |    строка    |    Уникальный идентификатор события.                                                                                                            |
|    .               |    объект    |    Данные события хранилища BLOB-объектов.                                                                                                                    |
|    dataVersion        |    строка    |    Версия схемы для объекта данных. Версию схемы определяет издатель.                                                          |
|    metadataVersion    |    строка    |    Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет Сетка событий.    |

#### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.BackupOperationStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data: {
        "appEventTypeDetail": { "action": "Started" },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    }
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

Объект данных содержит следующие свойства.

|    Свойство                |    Тип      |    Описание                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    объект    |    Сведения о действии в приложении.                                                                                       |
|    action                  |    строка    |    Тип действия операции.                                                                                   |
|    name                    |    строка    |    Имя веб-сайта с этим событием.                                                                          |
|    clientRequestId         |    строка    |    Идентификатор запроса клиента, созданный службой приложений для операции API сайта, активировавшей это событие.         |
|    correlationRequestId    |    строка    |    Идентификатор запроса корреляции, созданный службой приложений для операции API сайта, активировавшей это событие.    |
|    requestId               |    строка    |    Идентификатор запроса, созданный службой приложений для операции API сайта, активировавшей это событие.                |
|    address                 |    строка    |    URL-адрес HTTP-запроса для этой операции.                                                                                |
|    Команда                    |    строка    |    HTTP-команда для этой операции.                                                                                       |

#### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.RestoreOperationStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: { 
            action: "Started" 
        },
        siteName: "<site-name>",
        clientRequestId: "None",
        correlationRequestId: "None",
        requestId: "292f499d-04ee-4066-994d-c2df57b99198",
        address: "None",
        verb: "POST"
    }
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Объект данных содержит следующие свойства.

|    Свойство                |    Тип      |    Описание                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    объект    |    Сведения о действии в приложении.                                                                                       |
|    action                  |    строка    |    Тип действия операции.                                                                                   |
|    name                    |    строка    |    Имя веб-сайта с этим событием.                                                                          |
|    clientRequestId         |    строка    |    Идентификатор запроса клиента, созданный службой приложений для операции API сайта, активировавшей это событие.         |
|    correlationRequestId    |    строка    |    Идентификатор запроса корреляции, созданный службой приложений для операции API сайта, активировавшей это событие.    |
|    requestId               |    строка    |    Идентификатор запроса, созданный службой приложений для операции API сайта, активировавшей это событие.                |
|    address                 |    строка    |    URL-адрес HTTP-запроса для этой операции.                                                                                |
|    Команда                    |    строка    |    HTTP-команда для этой операции.                                                                                       |

#### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Объект данных содержит следующие свойства.

|    Свойство                |    Тип      |    Описание                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    объект    |    Сведения о действии в приложении.                                                                                       |
|    action                  |    строка    |    Тип действия операции.                                                                                   |
|    name                    |    строка    |    Имя веб-сайта с этим событием.                                                                          |
|    clientRequestId         |    строка    |    Идентификатор запроса клиента, созданный службой приложений для операции API сайта, активировавшей это событие.         |
|    correlationRequestId    |    строка    |    Идентификатор запроса корреляции, созданный службой приложений для операции API сайта, активировавшей это событие.    |
|    requestId               |    строка    |    Идентификатор запроса, созданный службой приложений для операции API сайта, активировавшей это событие.                |
|    address                 |    строка    |    URL-адрес HTTP-запроса для этой операции.                                                                                |
|    Команда                    |    строка    |    HTTP-команда для этой операции.                                                                                       |
|    sourceSlot              |    строка    |    Исходный слот для переключения.                                                                                       |

#### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapWithPreviewStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Объект данных содержит следующие свойства.

|    Свойство                |    Тип      |    Описание                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    объект    |    Сведения о действии в приложении.                                                                                       |
|    action                  |    строка    |    Тип действия операции.                                                                                   |
|    name                    |    строка    |    Имя веб-сайта с этим событием.                                                                          |
|    clientRequestId         |    строка    |    Идентификатор запроса клиента, созданный службой приложений для операции API сайта, активировавшей это событие.         |
|    correlationRequestId    |    строка    |    Идентификатор запроса корреляции, созданный службой приложений для операции API сайта, активировавшей это событие.    |
|    requestId               |    строка    |    Идентификатор запроса, созданный службой приложений для операции API сайта, активировавшей это событие.                |
|    address                 |    строка    |    URL-адрес HTTP-запроса для этой операции.                                                                                |
|    Команда                    |    строка    |    HTTP-команда для этой операции.                                                                                       |

#### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated.Restarted, AppUpdated.Stopped, AppUpdated.ChangedAppSettings

```js
{
    id: 'b74ea56b-2a3f-4de5-a5d7-38e60c81cf23',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.AppUpdated',
    eventTime: '2020-01-28T18:22:30.2760952Z',
    data: {
        appEventTypeDetail: {
            action: 'Stopped'
        },
        siteName: '<site-name>',
        clientRequestId: '64a5e0aa-7cee-4ff1-9093-b9197b820014',
        correlationRequestId: '25bb36a5-8f6c-4f04-b615-e9a0ee045756',
        requestId: 'f2e8eb3f-b190-42de-b99e-6acefe587374',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop',
        verb: 'POST'
    },
    topic: '/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Объект данных имеет следующие свойства:

|    Свойство                |    Тип      |    Описание                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    объект    |    Сведения о действии в приложении.                                                                                       |
|    action                  |    строка    |    Тип действия операции.                                                                                   |
|    name                    |    строка    |    Имя веб-сайта с этим событием.                                                                          |
|    clientRequestId         |    строка    |    Идентификатор запроса клиента, созданный службой приложений для операции API сайта, активировавшей это событие.         |
|    correlationRequestId    |    строка    |    Идентификатор запроса корреляции, созданный службой приложений для операции API сайта, активировавшей это событие.    |
|    requestId               |    строка    |    Идентификатор запроса, созданный службой приложений для операции API сайта, активировавшей это событие.                |
|    address                 |    строка    |    URL-адрес HTTP-запроса для этой операции.                                                                                |
|    Команда                    |    строка    |    HTTP-команда для этой операции.                                                                                       |

#### <a name="serverfarmsappserviceplanupdated"></a>Serverfarms.AppServicePlanUpdated

```js
{
   id: "56501672-9150-40e1-893a-18420c7fdbf7",
   subject: "/Microsoft.Web/serverfarms/<plan-name>",
   eventType: "Microsoft.Web.AppServicePlanUpdated",
   eventTime: "2020-01-28T18:22:23.5516004Z",
   data: {
        serverFarmEventTypeDetail: {
            stampKind: "Public",
            action: "Updated",
            status: "Started"
        },
        serverFarmId: "0",
        sku: {
            name: "P1v2",
            tier: "PremiumV2",
            size: "P1v2",
            family: "Pv2",
            capacity: 1
        },
        clientRequestId: "8f880321-a991-45c7-b743-6ff63fe4c004",
        correlationRequestId: "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        requestId: "b973a8e6-6949-4783-b44c-ac778be831bb",
        address: "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        verb: "PUT"
   },
   topic: "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
   dataVersion: "1",
   metaDataVersion: "1"
}
```

Объект данных имеет следующие свойства:

|    Свойство                         |    Тип      |    Описание                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appServicePlanEventTypeDetail    |    объект    |    Сведения о действии для плана службы приложений.                                                                          |
|    stampKind                        |    строка    |    Тип среды, в которой находится план службы приложений.                                                                     |
|    action                           |    строка    |    Тип действия для плана службы приложений.                                                                            |
|    status                           |    строка    |    Состояние операции для плана службы приложений.                                                                   |
|    sku                              |    объект    |    Номер SKU плана службы приложений.                                                                                       |
|    name                             |    строка    |    Имя плана службы приложений.                                                                                      |
|    Уровень                             |    строка    |    Уровень плана службы приложений.                                                                                      |
|    Размер                             |    строка    |    Размер плана службы приложений.                                                                                      |
|    Семейство                           |    строка    |    Семейство плана службы приложений.                                                                                        |
|    Capacity                         |    строка    |    Емкость плана службы приложений.                                                                                      |
|    action                           |    строка    |    Тип действия операции.                                                                                   |
|    name                             |    строка    |    Имя веб-сайта с этим событием.                                                                          |
|    clientRequestId                  |    строка    |    Идентификатор запроса клиента, созданный службой приложений для операции API сайта, активировавшей это событие.         |
|    correlationRequestId             |    строка    |    Идентификатор запроса корреляции, созданный службой приложений для операции API сайта, активировавшей это событие.    |
|    requestId                        |    строка    |    Идентификатор запроса, созданный службой приложений для операции API сайта, активировавшей это событие.                |
|    address                          |    строка    |    URL-адрес HTTP-запроса для этой операции.                                                                                |
|    Команда                             |    строка    |    HTTP-команда для этой операции.                                                                                       |
