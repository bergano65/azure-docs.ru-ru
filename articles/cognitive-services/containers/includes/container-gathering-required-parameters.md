---
title: Сбор необходимых параметров
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Параметры для всех контейнеров Когнитивных Услуг
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73465949"
---
## <a name="gathering-required-parameters"></a>Сбор необходимых параметров

Для всех необходимых контейнеров Cognitive Services требуется три основных параметра. Лицензионное соглашение конечного `accept`пользователя (EULA) должно присутствовать со значением . Кроме того, необходимы URL-адрес endpoint и API Key.

### <a name="endpoint-uri-endpoint_uri"></a>Конечная точка URI`{ENDPOINT_URI}`

Значение **Endpoint** URI доступно на странице портала Azure *Обзор* соответствующего ресурса Когнитивной Службы. Перейдите на страницу *Обзор,* нависнете над конечной точкой `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> и появится значок. Копировать и использовать там, где это необходимо.

![Соберите конечную точку uri для последующего использования](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Ключи`{API_KEY}`

Этот ключ используется для запуска контейнера и доступен на странице ключей портала Azure соответствующего ресурса Когнитивной службы. Перейдите на страницу *Ключей* и нажмите на `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> значок.

![Получите один из двух ключей для последующего использования](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Эти ключи подписки используются для доступа к API когнитивной службы. Не делитесь ключами. Храните их надежно, например, используя Azure Key Vault. Мы также рекомендуем регулярно восстанавливать эти ключи. Для вызова API необходим только один ключ. При регенерации первого ключа можно использовать второй ключ для постоянного доступа к службе.