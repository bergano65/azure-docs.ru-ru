---
title: Извлечение DOCKER для контейнера распознавание языка
titleSuffix: Azure Cognitive Services
description: Команда DOCKER Pull для контейнера распознавание языка
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3444d6267c09056697abf8339cc688a606b6dc17
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877103"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Извлечение DOCKER для контейнера распознавание языка

Используйте [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) команду, чтобы скачать образ контейнера из реестра контейнеров Майкрософт.

Полное описание доступных тегов для Анализ текста контейнеров см. в разделе контейнер [распознавание языка](https://go.microsoft.com/fwlink/?linkid=2018759) в центре DOCKER.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
