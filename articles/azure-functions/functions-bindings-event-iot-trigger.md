---
title: Привязки центра Интернета вещей Azure для функций Azure
description: Узнайте, как реагировать на события, отправляемые в поток событий центра Интернета вещей в функциях Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: f63fe965b3f37add8ddf9d262f1ef1dae9fff966
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77589731"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Триггер центра Интернета вещей Azure для функций Azure

В этой статье объясняется, как работать с привязками функций Azure для центра Интернета вещей. Поддержка центра Интернета вещей основана на [привязке концентраторов событий Azure](functions-bindings-event-hubs.md).

Сведения об установке и настройке см. в [этой обзорной статье](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Хотя в следующих примерах кода используется API концентратора событий, заданный синтаксис применим к функциям центра Интернета вещей.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>Дальнейшие шаги

- [Запись событий в поток событий (Выходная привязка)](./functions-bindings-event-iot-output.md)
