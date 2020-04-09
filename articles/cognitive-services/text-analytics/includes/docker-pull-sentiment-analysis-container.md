---
title: Докер тянуть для контейнера анализа настроений
titleSuffix: Azure Cognitive Services
description: Докер тянуть команды для анализа настроений контейнера
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877063"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Докер тянуть для контейнера анализа настроений

Используйте [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) команду для загрузки изображения контейнера из реестра контейнеров Майкрософт.

Полное описание доступных тегов для контейнеров Text Analytics можно найти в контейнере [анализа настроений](https://go.microsoft.com/fwlink/?linkid=2018654) в концентраторе Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
