---
title: включить файл
description: включить файл
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84905873"
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
