---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551222"
---
Доступны журналы консоли, создаваемые в контейнере. Во-первых включите ведение журнала контейнера, выполнив следующую команду в Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

После включения ведения журнала контейнера, выполните следующую команду, чтобы просмотреть поток данных журнала.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Если журналы консоли не отображаются, проверьте еще раз через 30 секунд.

> [!NOTE]
> Вы также можете проверить файлы журнала из браузера на `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Чтобы остановить, потоковая передача журналов в любое время, введите `Ctrl` + `C`.
