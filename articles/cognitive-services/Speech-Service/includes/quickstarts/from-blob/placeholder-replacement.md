---
title: Краткое руководство. Распознавание речи из файлов, находящихся в хранилище BLOB-объектов, в службе "Речь"
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: cb5b14bd5585c85947ef0dafeae1fb41cb8a4a5c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "80887412"
---
Необходимо заменить следующие значения:

 - `YourSubscriptionKey`: найдено на странице портала Azure **Ключи** для ресурса "Речь".
 - `YourServiceRegion`: найдено на странице портала Azure **Обзор** для ресурса "Речь".
 - `YourFileUrl`: находится в папке **службы BLOB-объектов / Контейнеров** на портале Azure для ресурса учетной записи хранения.
     - Выберите соответствующий контейнер
     - Выберите необходимый большой двоичный объект
     - Скопируйте **URL-адрес** на странице **Свойства**
