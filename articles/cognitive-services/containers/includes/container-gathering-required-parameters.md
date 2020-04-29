---
title: Идет сбор обязательных параметров
services: cognitive-services
author: aahill
manager: nitinme
description: Параметры для всех контейнеров Cognitive Services "
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80875059"
---
## <a name="gathering-required-parameters"></a>Идет сбор обязательных параметров

Есть три основных параметра для всех обязательных контейнеров Cognitive Services ". Лицензионное соглашение должно быть установлено со значением `accept`. Кроме того, требуются URL-адрес конечной точки и ключ API.

### <a name="endpoint-uri-endpoint_uri"></a>URI конечной точки`{ENDPOINT_URI}`

Значение URI **конечной точки** доступно на странице *обзора* портал Azure соответствующего ресурса службы. Перейдите на страницу *обзора* , наведите указатель мыши на конечную точку `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> , и появится значок. Копирование и использование при необходимости.

![Сбор URI конечной точки для последующего использования](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Ключ`{API_KEY}`

Этот ключ используется для запуска контейнера и доступен на странице ключей портал Azure соответствующего ресурса службы. Перейдите на страницу *ключи* и щелкните `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> значок.

![Получите один из двух ключей для последующего использования](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Эти ключи подписки используются для доступа к API-интерфейсу для работы со службой. Не предоставляйте общий доступ к ключам. Храните их безопасно, например, с помощью Azure Key Vault. Мы также советуем регулярно создавать эти ключи. Для вызова API требуется только один ключ. При повторном создании первого ключа можно использовать второй ключ для постоянного доступа к службе.