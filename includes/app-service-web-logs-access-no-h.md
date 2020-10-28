---
title: включить файл
description: включить файл
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 7803ac9009af1657e7f162d656898492a694e28f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743555"
---
Чтобы получить доступ к журналам консоли, созданным в коде приложения в Службе приложений, включите ведение журнала диагностики, выполнив следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Возможные значения для `--level`: `Error`, `Warning`, `Info` и `Verbose`. Каждый последующий уровень включает предыдущий уровень. Например: `Error` включает только сообщения об ошибках, а `Verbose` включает все сообщения.

Включив ведение журнала диагностики, выполните следующую команду, чтобы просмотреть поток данных журнала:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Если журналы консоли не отображаются, проверьте еще раз через 30 секунд.

> [!NOTE]
> Вы также можете проверить файлы журнала в браузере на странице `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Чтобы остановить потоковую передачу журналов, нажмите клавиши `Ctrl`+`C`.
