---
title: Повторная попытка задач после ошибки
description: Проверьте наличие ошибок и повторите их.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919997"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Обнаружение и обработка ошибок службы пакетов

Важно помнить, чтобы проверить на наличие ошибок при работе с API службы REST. Ошибки возникают нередко при запуске пакетных заданий.

## <a name="common-errors"></a>Распространенные ошибки 

- Сбои в работе сети - это запросы, которые никогда не достигали пакета или ответа пакета не дошли до клиента вовремя.
- Внутренние ошибки сервера - это стандартный 5xx статусный код HTTP ответ.
- Регулирование может привести к ошибкам, таким как 429 или 503 статусный код HTTP ответы с retry-after заголовок.
- Ошибки 4xx, которые включают такие ошибки, как AlreadyExists и InvalidOperation. Это означает, что ресурс находится не в правильном состоянии для перехода состояния.

Для получения подробной информации о различных типах [Batch Status and Error Codes](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)кодов ошибок и конкретных кодах ошибок см.

## <a name="when-to-retry"></a>Когда повторить

AIS пакетной связи уведомит вас о сбое. Все они могут быть повторены, и все они включают в себя глобальный обработчик повтора для этой цели. Лучше всего использовать этот встроенный механизм.

После сбоя, вы должны подождать немного (несколько секунд между повторами) перед повторной попыткой. Если вы делаете повторную попытку слишком часто или слишком быстро, обработчик повторной попытки задушит.

### <a name="for-more-information"></a>Дополнительные сведения  

[Пакетные API и инструменты](batch-apis-tools.md) ссылки на справочную информацию API. Например, aPI .NET имеет [класс RetryPolicyProvider,]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) где должна быть указана необходимая политика повтора. 

Для получения подробной информации о каждом API и их политике повторной попытки по умолчанию прочитайте [коды доступа к статусу и ошибкам.](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)
