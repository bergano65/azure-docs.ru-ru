---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 87c438403934df34a02678c805d3622956654b4e
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172055"
---
В этой таблице показаны привязки, которые поддерживаются в двух основных версиях среды выполнения функций Azure:

| Type | 1.x | 2.x<sup>1</sup> | Триггер | Ввод | Output |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Хранилище BLOB-объектов](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [База данных Cosmos](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|
| [Сетка событий](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |
| [Центры событий](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [Веб-перехватчики HTTP &](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [Центр Интернета вещей](../articles/azure-functions/functions-bindings-event-iot.md)             |✔|✔|✔| |✔|
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
