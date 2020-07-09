---
title: Извлечение DOCKER для контейнера работоспособности
titleSuffix: Azure Cognitive Services
description: Команда DOCKER Pull для Анализ текста контейнере работоспособности
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4c33952ed56399b94a7db1088cb60919a4f88137
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108985"
---
Заполните и отправьте [форму запроса Cognitive Services контейнеров](https://aka.ms/cognitivegate) , чтобы запросить доступ к контейнеру.

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

Используйте команду DOCKER login с учетными данными, предоставленными в электронной почте для подключения к закрытому реестру контейнеров для Cognitive Services контейнеров.

```bash
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Используйте [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) команду, чтобы скачать этот образ контейнера из закрытого реестра контейнеров.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
