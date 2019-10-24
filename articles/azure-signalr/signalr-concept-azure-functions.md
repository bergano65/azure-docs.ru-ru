---
title: Создавайте приложения в режиме реального времени с помощью функций Azure и службы SignalR Azure.
description: Общие сведения об использовании службы Azure SignalR в бессерверных приложениях.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 6a71647d452597c84e0df1beb6e9c6c3932d171b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639924"
---
# <a name="build-real-time-apps-with-azure-functions-and-azure-signalr-service"></a>Создавайте приложения в режиме реального времени с помощью функций Azure и службы SignalR Azure.

Так как службы Azure SignalR и "Функции Azure" являются полностью управляемыми высокомасштабируемыми службами, которые позволяют сосредоточиться на создании приложений вместо управления инфраструктурой, часто используют обе службы, чтобы обеспечить обмен данными в реальном времени в [беcсерверной](https://azure.microsoft.com/solutions/serverless/) среде.

> [!NOTE]
> Узнайте, как использовать SignalR и функции Azure вместе в интерактивном учебнике [Включение автоматического обновления в веб-приложении с помощью функций Azure и службы SignalR](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr).

## <a name="integrate-real-time-communications-with-azure-services"></a>Интеграция служб Azure в режиме реального времени

Функции Azure позволяют писать код на [нескольких языках](../azure-functions/supported-languages.md), включая JavaScript, Python, C#и Java, которые срабатывают при каждом возникновении событий в облаке. Примеры этих событий.

* Запросы HTTP и веб-перехватчика.
* Периодические таймеры.
* События из служб Azure, такие как:
    - Сетка событий
    - Концентраторы событий
    - Служебная шина
    - канал изменений Cosmos DB;
    - хранилище: большие двоичные объекты и очереди;
    - соединители Logic Apps, например Salesforce и SQL Server.

Используя службу "Функции Azure" для интеграции этих событий с помощью службы Azure SignalR, вы можете уведомлять тысячи клиентов о каждом событии.

Некоторые распространенные сценарии беcсерверной передачи сообщений в режиме реального времени, которые можно реализовать с помощью служб "Функции Azure" и SignalR, включают следующее.

* Визуализация телеметрии устройства IoT на панели управления в реальном времени или на карте
* Обновление данных в приложении при обновлении документов в Cosmos DB
* Отправка уведомлений в приложении при создании новых заказов в Salesforce

## <a name="signalr-service-bindings-for-azure-functions"></a>Привязки службы SignalR для службы "Функции Azure"

Привязки службы SignalR для службы "Функции Azure" позволяют приложению "Функции Azure" публиковать сообщения клиентов, подключенных к службе SignalR. В ближайшее время клиенты смогут подключаться к службе с помощью пакета SDK клиента SignalR, доступного на языках .NET, JavaScript и Java, а вскоре и с большим количеством языков.

### <a name="an-example-scenario"></a>Пример сценария

Пример использования привязок службы SignalR — это использование службы "Функции Azure" для интеграции с Azure Cosmos DB и службой SignalR для отправки сообщений в режиме реального времени, когда появляются новые события в канале изменения Cosmos DB.

![Cosmos DB, служба "Функции Azure", служба SignalR](media/signalr-concept-azure-functions/signalr-cosmosdb-functions.png)

1. Изменения, сделанные в коллекции Cosmos DB
2. Событие изменения распространяется на канал изменений Cosmos DB
3. Функции Azure активированы событием изменения с помощью триггера Cosmos DB
4. Выходная привязка службы SignalR публикует сообщение службе SignalR
5. Служба SignalR публикует сообщение всем подключенным клиентам

### <a name="authentication-and-users"></a>Проверка подлинности и пользователи

Служба SignalR позволяет рассылать широковещательные сообщения для всех клиентов или только для подмножества клиентов, например тех, которые принадлежат одному пользователю. Привязки службы SignalR для службы "Функции Azure" можно объединить с Проверкой подлинности службы приложений для проверки подлинности пользователей с помощью поставщиков, например Azure Active Directory, Facebook и Twitter. Затем вы можете отправлять сообщения непосредственно этим пользователям, прошедшим проверку подлинности.

## <a name="next-steps"></a>Следующие шаги

В этой статье вы получили общие сведения об использовании службы "Функции Azure" со службой SignalR для реализации широкого спектра бессерверных сценариев обмена сообщениями в реальном времени.

Полные сведения об использовании функций Azure и службы SignalR см. в следующих ресурсах:

* [Разработка и Настройка функций Azure с помощью службы SignalR](signalr-concept-serverless-development-config.md)
* [Включение автоматического обновления в веб-приложении с помощью функций Azure и службы SignalR](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr)

Дополнительные сведения см. в следующих кратких руководствах.

* [Quickstart: Create a chat room with Azure Functions and SignalR Service using C#](signalr-quickstart-azure-functions-csharp.md) (Создание чат-комнаты с помощью служб "Функции Azure" и SignalR с использованием C#)
* [Quickstart: Create a chat room with Azure Functions and SignalR Service using JavaScript](signalr-quickstart-azure-functions-javascript.md) (Создание чат-комнаты с помощью служб "Функции Azure" и SignalR с использованием JavaScript)
