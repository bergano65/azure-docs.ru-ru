---
title: Извлечение DOCKER для контейнера анализ тональности
titleSuffix: Azure Cognitive Services
description: Команда DOCKER Pull для контейнера анализ тональности
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 0954ef88f6917159156fbb73103b7b7af3283c00
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051394"
---
## <a name="pull-the-sentiment-analysis-container"></a>Извлечение контейнера анализ тональности

Образы контейнеров для Анализ текста доступны в реестре контейнеров Microsoft.

| Контейнер | Реестр контейнеров, имя репозитория или образа |
|-----------|------------|
| Анализ тональности | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Используйте команду, чтобы скачать образ контейнера из реестра контейнеров Майкрософт.

Полное описание доступных тегов для Анализ текста контейнеров см. в разделе контейнер [Анализ тональности](https://go.microsoft.com/fwlink/?linkid=2018654) в центре DOCKER.

### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Извлечение DOCKER для контейнера анализ тональности

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
