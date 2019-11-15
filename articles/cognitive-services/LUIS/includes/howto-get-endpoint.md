---
title: включение файла
description: включение файла
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 39bfa5b6800c65112850faa8842b915ceedb9312
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648068"
---
В разделе **Управление** (в меню справа вверху) на странице **Ресурсы Azure** (меню слева) скопируйте URL-адрес из поля **Пример запроса** и откройте этот адрес на новой вкладке браузера. 

URL-адрес конечной точки имеет представленный ниже формат, где APP-ID и KEY-ID заменяются реальными значениями идентификатора приложения и ключа конечной точки.

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```