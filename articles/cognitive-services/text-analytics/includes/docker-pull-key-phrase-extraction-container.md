---
title: Докер тянуть для ключевых фраз извлечения контейнера
titleSuffix: Azure Cognitive Services
description: Докер тянуть команды для ключевых фраз извлечения контейнера
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877123"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Докер тянуть для ключевых фраз извлечения контейнера

Используйте [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) команду для загрузки изображения контейнера из реестра контейнеров Майкрософт.

Полное описание доступных тегов для контейнеров Text Analytics можно найти в контейнере [Key Phrase Extraction](https://go.microsoft.com/fwlink/?linkid=2018757) на концентраторе Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
