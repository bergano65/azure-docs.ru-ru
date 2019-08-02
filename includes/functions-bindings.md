---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0134d20f4b5d2816fa38bfb7377154463acd4781
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669796"
---
В этой таблице показаны привязки, которые поддерживаются в двух основных версиях среды выполнения функций Azure:

| Тип | 1.x | 2.x<sup>1</sup> | Триггер | Ввод | Вывод |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Хранилище BLOB-объектов](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [База данных Cosmos](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|
| [Сетка событий](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |
| [Центры событий](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [Веб-перехватчики HTTP &](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [Таблицы Excel<br/>Microsoft Graph](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Файлы OneDrive<br/>Microsoft Graph](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Электронная почта Outlook<br/>Microsoft Graph](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [События<br/>Microsoft Graph](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Маркеры безопасности<br/>Microsoft Graph](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Мобильные приложения](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|
| [Центры уведомлений](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Хранилище очередей](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Служебная шина](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|
| [SignalR](../articles/azure-functions/functions-bindings-signalr-service.md)             | |✔| |✔|✔|
| [Хранилище таблиц](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|
| [Таймер](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|

<sup>1</sup> В версии 2.х все привязки, кроме HTTP и Timer, должны быть зарегистрированы. Ознакомьтесь с разделом [Регистрация расширений привязки](../articles/azure-functions/functions-bindings-register.md).
