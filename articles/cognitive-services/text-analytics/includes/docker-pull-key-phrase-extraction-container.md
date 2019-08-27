---
title: Извлечение DOCKER для контейнера извлечение ключевых фраз
titleSuffix: Azure Cognitive Services
description: Команда DOCKER Pull для контейнера извлечение ключевых фраз
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: e71db5455a3d6726cd6567d568a13fcdcf9cb520
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051302"
---
## <a name="pull-the-key-phrase-extraction-container"></a>Извлечение контейнера извлечение ключевых фраз

Образы контейнеров для Анализ текста доступны в реестре контейнеров Microsoft.

| Контейнер | Реестр контейнеров, имя репозитория или образа |
|-----------|------------|
| Извлечение ключевых фраз | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Используйте команду, чтобы скачать образ контейнера из реестра контейнеров Майкрософт.

Полное описание доступных тегов для Анализ текста контейнеров см. в разделе контейнер [Извлечение ключевых фраз](https://go.microsoft.com/fwlink/?linkid=2018757) в центре DOCKER.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Извлечение DOCKER для контейнера извлечение ключевых фраз

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
