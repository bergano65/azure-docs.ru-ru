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
ms.openlocfilehash: 549ecf940c53267db6b3caa576c4c24db414337a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906006"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Извлечение DOCKER для контейнера анализ тональности v3

Контейнер тональности Analysis Container версии 3 доступен на нескольких языках. Чтобы скачать контейнер для английского языка, используйте следующую команду. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
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