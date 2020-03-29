---
title: Докер тянуть для ключевых фраз извлечения контейнера
titleSuffix: Azure Cognitive Services
description: Докер тянуть команды для ключевых фраз извлечения контейнера
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966694"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Докер тянуть для ключевых фраз извлечения контейнера

Используйте [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) команду для загрузки изображения контейнера из реестра контейнеров Майкрософт.

Полное описание доступных тегов для контейнеров Text Analytics можно найти в контейнере [Key Phrase Extraction](https://go.microsoft.com/fwlink/?linkid=2018757) на концентраторе Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
