---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 957009ec9833df2dd0f89c3745c7f3df5a3705b4
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "59520543"
---
В следующей таблице показаны привязки, которые поддерживаются в двух основных версиях среды выполнения службы "Функции Azure".

| type | 1.x | 2.x<sup>1</sup> | Триггер | Входные данные | Выходные данные |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Хранилище BLOB-объектов](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [База данных Cosmos](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|
| [Сетка событий Azure](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |
| [Центры событий;](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [HTTP и веб-перехватчик](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [Microsoft Graph<br/>Таблицы Excel](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Microsoft Graph<br/>Файлы OneDrive](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft Graph<br/>Электронная почта Outlook](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Microsoft Graph<br/>События](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft Graph<br/>Токены проверки подлинности](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Мобильные приложения](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|
| [Центры уведомлений](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Хранилище очередей](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Служебная шина Azure](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|
| [SignalR](../articles/azure-functions/functions-bindings-signalr-service.md)             | |✔| |✔|✔|
| [Хранилище таблиц](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|
| [Таймер](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|

<sup>1</sup> В версии 2.х все привязки, кроме HTTP и Timer, должны быть зарегистрированы. Ознакомьтесь с разделом [Регистрация расширений привязки](../articles/azure-functions/functions-bindings-register.md).
