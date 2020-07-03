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
ms.openlocfilehash: 2a98e2a97a9154d9e256a4662bb292896c6d1e77
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588802"
---
В разделе **Управление** (в меню справа вверху) на странице **Ресурсы Azure** (меню слева) скопируйте URL-адрес в поле **Пример запроса** и перейдите по этому адресу в новой вкладке браузера.

URL-адрес конечной точки имеет следующий формат (с пользовательскими поддоменами), где APP-ID и KEY-ID заменяются реальными значениями идентификатора приложения и ключа конечной точки.

```console
https://YOUR-CUSTOM-SUBDMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```