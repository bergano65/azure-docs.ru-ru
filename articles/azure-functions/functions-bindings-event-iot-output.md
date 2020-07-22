---
title: Выходная привязка центра Интернета вещей Azure для функций Azure
description: Узнайте, как записывать сообщения в потоки центров Интернета вещей Azure с помощью функций Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79277431"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Выходная привязка центра Интернета вещей Azure для функций Azure

В этой статье объясняется, как работать с выходными привязками функций Azure для центра Интернета вещей. Поддержка центра Интернета вещей основана на [привязке концентраторов событий Azure](functions-bindings-event-hubs.md).

Сведения об установке и настройке см. в [этой обзорной статье](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Хотя в следующих примерах кода используется API концентратора событий, заданный синтаксис применим к функциям центра Интернета вещей.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>Дальнейшие шаги

- [Реагирование на события, отправленные в поток событий концентратора событий (триггер)](./functions-bindings-event-iot-trigger.md)
