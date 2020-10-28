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
ms.openlocfilehash: e2c5794e5ce6e23b60bff513562f69c9333d6e34
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743805"
---
Можно получить доступ к журналам консоли, которые были созданы в контейнере.

Сначала включите ведение журнала контейнера, выполнив следующую команду:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Замените `<app-name>` и `<resource-group-name>` именами, подходящими для вашего веб-приложения.

После включения ведения журнала контейнера, выполните следующую команду, чтобы просмотреть поток данных журнала.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Если журналы консоли не отображаются, проверьте еще раз через 30 секунд.

Чтобы остановить потоковую передачу журналов, нажмите клавиши **CTRL**+**C** .

Вы также можете проверить файлы журнала в браузере на странице `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
