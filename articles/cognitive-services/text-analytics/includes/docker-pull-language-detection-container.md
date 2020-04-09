---
title: Докер тянуть для контейнера обнаружения языка
titleSuffix: Azure Cognitive Services
description: Команда докера вытягивает для контейнера обнаружения языка
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3444d6267c09056697abf8339cc688a606b6dc17
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877103"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Докер тянуть для контейнера обнаружения языка

Используйте [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) команду для загрузки изображения контейнера из реестра контейнеров Майкрософт.

Полное описание доступных тегов для контейнеров Text Analytics можно найти в контейнере [Language Detection](https://go.microsoft.com/fwlink/?linkid=2018759) на концентраторе Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
