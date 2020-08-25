---
title: включить файл
description: включить файл
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 01d102de24185eba789e041d8534d6fdce8c260b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2020
ms.locfileid: "87375536"
---
В разделе **Управление** (в меню справа вверху) на странице **Ресурсы Azure** (меню слева) скопируйте URL-адрес в поле **Пример запроса** и перейдите по этому адресу в новой вкладке браузера.

URL-адрес конечной точки имеет следующий формат (с пользовательскими поддоменами), где APP-ID и KEY-ID заменяются реальными значениями идентификатора приложения и ключа конечной точки.

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```