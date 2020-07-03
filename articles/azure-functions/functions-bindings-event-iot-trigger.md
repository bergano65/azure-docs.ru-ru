---
title: Привязки центра Интернета вещей Azure для функций Azure
description: Узнайте, как реагировать на события, отправляемые в поток событий центра Интернета вещей в функциях Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: f63fe965b3f37add8ddf9d262f1ef1dae9fff966
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77589731"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Триггер центра Интернета вещей Azure для функций Azure

В этой статье объясняется, как работать с привязками функций Azure для центра Интернета вещей. Поддержка центра Интернета вещей основана на [привязке концентраторов событий Azure](functions-bindings-event-hubs.md).

Дополнительные сведения об установке и сведениях о конфигурации см. в [обзоре](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Хотя в следующих примерах кода используется API концентратора событий, заданный синтаксис применим к функциям центра Интернета вещей.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>Дальнейшие шаги

- [Запись событий в поток событий (Выходная привязка)](./functions-bindings-event-iot-output.md)
