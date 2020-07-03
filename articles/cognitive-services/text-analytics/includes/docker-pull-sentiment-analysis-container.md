---
title: Извлечение DOCKER для контейнера анализ тональности
titleSuffix: Azure Cognitive Services
description: Команда DOCKER Pull для контейнера анализ тональности
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a3db0e2ffdd4a75f02634ca2227c3c41416d4f65
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588418"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Извлечение DOCKER для контейнера анализ тональности v3

Контейнер тональности Analysis Container версии 3 доступен на нескольких языках. Чтобы скачать контейнер для английского языка, используйте следующую команду. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en
```

Чтобы загрузить контейнер для другого языка, замените `en` на один из кодов языка, приведенных ниже. 

| Контейнер Анализ текста | Код языка |
|--|--|
| Английский | `en` |
| Испанский | `es` |
| Французский | `fr` |
| Итальянский | `it` |
| Немецкий | `de` |
| Китайский (упрощенное письмо) | `zh` |
| Китайский (традиционное письмо) | `zht` |
| Японский | `ja` |
| Португальский | `pt` |
| Нидерландский | `nl` |

Полное описание доступных тегов для контейнеров Анализ текста см. в разделе [DOCKER Hub](https://go.microsoft.com/fwlink/?linkid=2018654).