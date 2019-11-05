---
title: Идет сбор обязательных параметров
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Параметры для всех контейнеров Cognitive Services "
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465949"
---
## <a name="gathering-required-parameters"></a>Идет сбор обязательных параметров

Есть три основных параметра для всех обязательных контейнеров Cognitive Services ". Лицензионное соглашение (EULA) должно присутствовать в значении `accept`. Кроме того, требуются URL-адрес конечной точки и ключ API.

### <a name="endpoint-uri-endpoint_uri"></a>URI конечной точки `{ENDPOINT_URI}`

Значение URI **конечной точки** доступно на странице *обзора* портал Azure соответствующего ресурса службы. Перейдите на страницу *обзора* , наведите указатель мыши на конечную точку, <span class="docon docon-edit-copy x-hidden-focus"></span> и появится значок `Copy to clipboard`. Копирование и использование при необходимости.

![Сбор URI конечной точки для последующего использования](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Ключи `{API_KEY}`

Этот ключ используется для запуска контейнера и доступен на странице ключей портал Azure соответствующего ресурса службы. Перейдите на страницу *ключи* и щелкните значок `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> .

![Получите один из двух ключей для последующего использования](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Эти ключи подписки используются для доступа к API-интерфейсу для работы со службой. Не предоставляйте общий доступ к ключам. Храните их безопасно, например, с помощью Azure Key Vault. Мы также советуем регулярно создавать эти ключи. Для вызова API требуется только один ключ. При повторном создании первого ключа можно использовать второй ключ для постоянного доступа к службе.