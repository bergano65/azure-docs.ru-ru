---
title: Повторная попытка выполнения задач после ошибки
description: Проверьте наличие ошибок и повторите попытку.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-batch
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/15/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 94ed936e619461a2dbf7ec837c2d80e21c01c88e
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919997"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Обнаружение и обработка ошибок пакетной службы

При работе с API службы RESTFUL важно помнить о наличии ошибок. Нередко возникают ошибки при выполнении пакетных заданий.

## <a name="common-errors"></a>Распространенные ошибки 

- Сбои в работе сети — это запросы, которые никогда не достигли пакета, или пакетный ответ не был достигнут на клиенте вовремя.
- Внутренние ошибки сервера — это стандартный HTTP-ответ для кода состояния 5xx.
- Регулирование может вызвать такие ошибки, как 429 или 503 код состояния HTTP с заголовком Retry-After.
- 4xx ошибки, которые включают такие ошибки, как уже существует и InvalidOperation. Это означает, что ресурс находится в неправильном состоянии для перехода между состояниями.

Подробные сведения о различных типах кодов ошибок и определенных кодах ошибок см. в разделе [состояние пакета и коды ошибок](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).

## <a name="when-to-retry"></a>Время повтора

API-интерфейсы пакетной службы выводят уведомление при сбое. Их можно повторить, и все они включают в себя глобальный обработчик повторных попыток для этой цели. Лучше использовать этот встроенный механизм.

После сбоя следует подождать некоторое время (несколько секунд между повторными попытками) перед повторной попыткой. При слишком частом или слишком быстром повторении обработчик повторных попыток выполняет регулирование.

### <a name="for-more-information"></a>Дополнительные сведения  

Ссылки на API- [интерфейсы и инструменты пакетной службы см](batch-apis-tools.md) . в справочнике по API. Например, в API .NET имеется [класс ретриполиципровидер]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) , в котором должна быть указана обязательная политика повтора. 

Подробные сведения о каждом API и их политиках повтора см. в статье [состояние пакета и коды ошибок](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
