---
title: Краткое руководство. Использование клиентской библиотеки вызова служб связи Azure
titleSuffix: An Azure Communication Services quickstart
description: Сведения о службах связи, вызывающих возможности клиентской библиотеки.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 9a70cfe3c9ced2d22fcb50224d991e34c516a04c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940067"
---
# <a name="quickstart-use-the-communication-services-calling-client-library"></a>Краткое руководство. Использование клиентской библиотеки вызова служб связи

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Начало работы со Службами коммуникации Azure с помощью клиентской библиотеки голосовых вызовов Служб коммуникации для добавления в приложение функции голосовых вызовов и видеовызовов. 

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/calling-sdk-js.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/calling-sdk-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/calling-sdk-ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку на Службы коммуникации, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней. См. сведения об [очистке ресурсов](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях:

- Ознакомьтесь с нашим [главным примером функции вызовов](../../samples/calling-hero-sample.md).
- Узнайте больше о [принципе работы функции вызовов](../../concepts/voice-video-calling/about-call-types.md).