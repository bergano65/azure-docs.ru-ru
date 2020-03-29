---
title: Докер тянуть для контейнера анализа настроений
titleSuffix: Azure Cognitive Services
description: Докер тянуть команды для анализа настроений контейнера
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966683"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Докер тянуть для контейнера анализа настроений

Используйте [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) команду для загрузки изображения контейнера из реестра контейнеров Майкрософт.

Полное описание доступных тегов для контейнеров Text Analytics можно найти в контейнере [анализа настроений](https://go.microsoft.com/fwlink/?linkid=2018654) в концентраторе Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
