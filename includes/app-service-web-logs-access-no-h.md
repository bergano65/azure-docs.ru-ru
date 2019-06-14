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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66137362"
---
Можно получить доступ к журналам консоли, которые были созданы в контейнере. Сначала выполните приведенную ниже команду в Cloud Shell для включения ведения журнала контейнера.

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

После включения ведения журнала контейнера, выполните следующую команду, чтобы просмотреть поток данных журнала.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Если журналы консоли не отображаются, проверьте еще раз через 30 секунд.

> [!NOTE]
> Вы также можете проверить файлы журнала в браузере на странице `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Чтобы остановить потоковую передачу журналов, нажмите клавиши `Ctrl`+`C`.
