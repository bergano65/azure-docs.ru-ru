---
title: включить файл
description: включить файл
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.openlocfilehash: 6d1ca85c59f03ae0d008342f71597f4d3ca5d97a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545788"
---
В разделе **Управление** (в меню справа вверху) на странице **Ресурсы Azure** (меню слева) скопируйте URL-адрес в поле **Пример запроса** и перейдите по этому адресу в новой вкладке браузера.

URL-адрес конечной точки имеет следующий формат (с пользовательскими поддоменами), где APP-ID и KEY-ID заменяются реальными значениями идентификатора приложения и ключа конечной точки.

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```