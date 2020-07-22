---
title: включить файл
description: включить файл
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: a6e6c89c34723fb9e11b0c7e4ab8c9bedb8aa9ca
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959059"
---
В разделе **Управление** (в меню справа вверху) на странице **Ресурсы Azure** (меню слева) скопируйте URL-адрес в поле **Пример запроса** и перейдите по этому адресу в новой вкладке браузера.

URL-адрес конечной точки имеет следующий формат (с пользовательскими поддоменами), где APP-ID и KEY-ID заменяются реальными значениями идентификатора приложения и ключа конечной точки.

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```