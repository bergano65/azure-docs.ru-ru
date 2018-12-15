---
title: Примеры Azure CLI. Служба Azure SignalR
description: Примеры Azure CLI. Служба Azure SignalR
author: sffamily
ms.service: signalr
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: a39f0abf4f3a8cc30d6f8f83dcad7e9069f348e4
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258786"
---
# <a name="azure-cli-samples"></a>Примеры Azure CLI

В следующей таблице содержатся ссылки на скрипты Bash для службы Azure SignalR, созданные с помощью Azure CLI.

| | |
|-|-|
|**Создание**||
| [Создание службы SignalR и группы ресурсов](scripts/signalr-cli-create-service.md) | Создает ресурс службы Azure SignalR со случайным именем в новой группе ресурсов.  |
|**Интеграция**||
| [Создание службы SignalR и веб-приложения, настроенного для использования SignalR](scripts/signalr-cli-create-with-app-service.md) | Создает ресурс службы Azure SignalR со случайным именем в новой группе ресурсов. Также добавляет новое веб-приложение и план службы приложений, чтобы разместить веб-приложение ASP.NET Core, которое использует службу SignalR. Веб-приложение настраивается с использованием параметра приложения для подключения к новому ресурсу службы SignalR. |
| [Создание службы SignalR и веб-приложения, настроенного для использования SignalR и OAuth GitHub](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Создает ресурс службы Azure SignalR со случайным именем в новой группе ресурсов. Также добавляет новое веб-приложение Azure и соответствующий план для размещения веб-приложения ASP.NET Core, которое использует службу SignalR. Веб-приложение настраивается с использованием параметров для определения строки подключения нового ресурса службы SignalR и секретов клиента для включения поддержки [аутентификации GitHub](https://developer.github.com/v3/guides/basics-of-authentication/), как описано в [руководстве по аутентификации](signalr-authenticate-oauth.md). Также веб-приложение настраивается для использования локального репозитория Git в качестве источника развертывания. |
| | |