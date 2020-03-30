---
title: Примеры Azure CLI. Служба Azure SignalR
description: Следите за реальными образцами, чтобы узнать Azure CLI для службы Azure SignalR. Вы узнаете, как создать службу SignalR с большим количеством служб Azure.
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 3c28781362e60d22b0d9a4e3754daaa24e5b61ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158126"
---
# <a name="azure-cli-reference"></a>Справочник по Azure CLI

В следующей таблице содержатся ссылки на скрипты Bash для службы Azure SignalR, созданные с помощью Azure CLI.

| | |
|-|-|
|**Создать**||
| [Создание службы SignalR и группы ресурсов](scripts/signalr-cli-create-service.md) | Создает ресурс службы Azure SignalR со случайным именем в новой группе ресурсов.  |
|**Интеграция**||
| [Создание службы SignalR и веб-приложения, настроенного для использования SignalR](scripts/signalr-cli-create-with-app-service.md) | Создает ресурс службы Azure SignalR со случайным именем в новой группе ресурсов. Также добавляется новый веб-приложение и App Service план для размещения ASP.NET Core Web App, который использует службу SignalR. Веб-приложение настраивается с использованием параметра приложения для подключения к новому ресурсу службы SignalR. |
| [Создание службы SignalR и веб-приложения, настроенного для использования SignalR и OAuth GitHub](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Создает ресурс службы Azure SignalR со случайным именем в новой группе ресурсов. Также добавляется новое Web App Azure и хостинг план для размещения ASP.NET Core Web App, который использует службу SignalR. Веб-приложение настраивается с использованием параметров для определения строки подключения нового ресурса службы SignalR и секретов клиента для включения поддержки [аутентификации GitHub](https://developer.github.com/v3/guides/basics-of-authentication/), как описано в [руководстве по аутентификации](signalr-concept-authenticate-oauth.md). Также в веб-приложении настраивается локальный репозиторий Git в качестве источника развертывания. |
| | |
