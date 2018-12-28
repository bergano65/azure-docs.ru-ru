---
title: Настройка модели Person в Индексаторе видео в Azure
titlesuffix: Azure Media Services
description: В этой статье приведены общие сведения о том, что такое модель Person в Индексаторе видео и как ее настраивать.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 073cff22f17f496c2ff85cfbf716751dfea1e03e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283240"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Настройка модели Person в Индексаторе видео


Индексатор видео поддерживает функцию определения лиц и распознавания знаменитостей для видеосодержимого. Функция распознавания знаменитостей охватывает около 1 000 000 лиц на основе часто запрашиваемых источников данных, таких как IMDB, Википедия и влиятельные лица LinkedIn. Лица, которые не распознаются с помощью функции распознавания знаменитостей, также определяются, но их имя не указывается. После передачи видео в Индексатор видео и получения результатов вы можете вернуться и ввести имена для лиц, которые не удалось распознать. После того как вы введете имя для определенного лица, это лицо и назначенное имя будут добавлены в модель Person вашей учетной записи. Индексатор видео затем сможет распознать это лицо в будущих и предыдущих видео.

Вы можете использовать веб-сайт или программный интерфейс Индексатора видео для изменения обнаруженных в видео лиц в вашей учетной записи, как описано в следующих разделах.

- [Customize a Person model with the Video Indexer API](customize-person-model-with-api.md) (Настройка модели Person с помощью API Индексатора видео)
- [Customize a Person model with the Video Indexer website](customize-person-model-with-website.md) (Настройка модели Person с помощью веб-сайта Индексатора видео)
