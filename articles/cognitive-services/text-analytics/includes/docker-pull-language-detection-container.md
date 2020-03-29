---
title: Докер тянуть для контейнера обнаружения языка
titleSuffix: Azure Cognitive Services
description: Команда докера вытягивает для контейнера обнаружения языка
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966658"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Докер тянуть для контейнера обнаружения языка

Используйте [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) команду для загрузки изображения контейнера из реестра контейнеров Майкрософт.

Полное описание доступных тегов для контейнеров Text Analytics можно найти в контейнере [Language Detection](https://go.microsoft.com/fwlink/?linkid=2018759) на концентраторе Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
