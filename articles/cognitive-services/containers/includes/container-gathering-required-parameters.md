---
title: Идет сбор обязательных параметров
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Параметры для всех контейнеров Cognitive Services "
ms.service: cognitive-services
ms.topic: include
ms.date: 7/24/2019
ms.author: dapine
ms.openlocfilehash: 636a41fde345a08db1549e53626522962f9cf74f
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488769"
---
## <a name="gathering-required-parameters"></a>Идет сбор обязательных параметров

Есть три основных параметра для всех обязательных контейнеров Cognitive Services ". Лицензионное соглашение должно быть установлено созначением `accept`. Кроме того, требуются URL-адрес конечной точки и ключ API.

### <a name="endpoint-uri-endpointuri"></a>URI конечной точки`{Endpoint_URI}`

Значение URI **конечной точки** доступно на странице *обзора* портал Azure соответствующего ресурса службы. Перейдите на страницу *обзора* , наведите указатель мыши на конечную точку `Copy to clipboard` , и появится <span class="docon docon-edit-copy x-hidden-focus"></span> значок. Копирование и использование при необходимости.

![Сбор URI конечной точки для последующего использования](../media/overview-endpoint-uri.png)

### <a name="keys-apikey"></a>Ключ`{API_Key}`

Этот ключ используется для запуска контейнера и доступен на странице ключей портал Azure соответствующего ресурса службы. Перейдите на страницу *ключи* и щелкните `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> значок.

![Получите один из двух ключей для последующего использования](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Эти ключи подписки используются для доступа к API-интерфейсу для работы со службой. Не предоставляйте общий доступ к ключам. Храните их безопасно, например, с помощью Azure Key Vault. Мы также советуем регулярно создавать эти ключи. Для вызова API требуется только один ключ. При повторном создании первого ключа можно использовать второй ключ для постоянного доступа к службе.