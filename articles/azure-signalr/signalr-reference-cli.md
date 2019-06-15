---
title: Примеры Azure CLI. Служба Azure SignalR
description: Примеры Azure CLI. Служба Azure SignalR
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1fbe96d827bcf6bb39a6cf7a4e6811174b862d59
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61071082"
---
# <a name="azure-cli-reference"></a>Справочник по Azure CLI

В следующей таблице содержатся ссылки на скрипты Bash для службы Azure SignalR, созданные с помощью Azure CLI.

| | |
|-|-|
|**Создание**||
| [Создание службы SignalR и группы ресурсов](scripts/signalr-cli-create-service.md) | Создает ресурс службы Azure SignalR со случайным именем в новой группе ресурсов.  |
|**Интеграция**||
| [Создание службы SignalR и веб-приложения, настроенного для использования SignalR](scripts/signalr-cli-create-with-app-service.md) | Создает ресурс службы Azure SignalR со случайным именем в новой группе ресурсов. Также добавляет новый план веб-приложения и службы приложений для размещения веб-приложения ASP.NET Core, которое использует службу SignalR. Веб-приложение настраивается с использованием параметра приложения для подключения к новому ресурсу службы SignalR. |
| [Создание службы SignalR и веб-приложения, настроенного для использования SignalR и OAuth GitHub](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Создает ресурс службы Azure SignalR со случайным именем в новой группе ресурсов. Также добавляет новое веб-приложение Azure и план для размещения веб-приложения ASP.NET Core, которое использует службу SignalR размещения. Веб-приложение настраивается с использованием параметров для определения строки подключения нового ресурса службы SignalR и секретов клиента для включения поддержки [аутентификации GitHub](https://developer.github.com/v3/guides/basics-of-authentication/), как описано в [руководстве по аутентификации](signalr-concept-authenticate-oauth.md). Также веб-приложение настраивается для использования локального репозитория Git в качестве источника развертывания. |
| | |
