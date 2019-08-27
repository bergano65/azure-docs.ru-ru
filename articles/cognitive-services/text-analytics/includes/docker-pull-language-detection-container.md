---
title: Извлечение DOCKER для контейнера распознавание языка
titleSuffix: Azure Cognitive Services
description: Команда DOCKER Pull для контейнера распознавание языка
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: da6f04f1042d1a02178f345c5fe67387ae0a7d0f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051274"
---
## <a name="pull-the-language-detection-container"></a>Извлечение контейнера распознавание языка

Образы контейнеров для Анализ текста доступны в реестре контейнеров Microsoft.

| Контейнер | Реестр контейнеров, имя репозитория или образа |
|-----------|------------|
| Распознавание языка | `mcr.microsoft.com/azure-cognitive-services/language` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Используйте команду, чтобы скачать образ контейнера из реестра контейнеров Майкрософт.

Полное описание доступных тегов для Анализ текста контейнеров см. в разделе контейнер [распознавание языка](https://go.microsoft.com/fwlink/?linkid=2018759) в центре DOCKER.


### <a name="docker-pull-for-the-language-detection-container"></a>Извлечение DOCKER для контейнера распознавание языка

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
