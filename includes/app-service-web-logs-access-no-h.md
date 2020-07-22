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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905873"
---
Чтобы получить доступ к журналам консоли, созданным внутри кода приложения в службе приложений, включите ведение журнала диагностики, выполнив следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Возможные значения для `--level` : `Error` , `Warning` , `Info` и `Verbose` . Каждый последующий уровень включает предыдущий уровень. Например: `Error` включает только сообщения об ошибках и `Verbose` включает все сообщения.

После включения ведения журнала диагностики выполните следующую команду, чтобы просмотреть поток журнала:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Если журналы консоли не отображаются, проверьте еще раз через 30 секунд.

> [!NOTE]
> Вы также можете проверить файлы журнала в браузере на странице `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Чтобы остановить потоковую передачу журналов, нажмите клавиши `Ctrl`+`C`.
