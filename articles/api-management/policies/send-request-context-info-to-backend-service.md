---
title: Пример политики управления API — отправка сведений о контексте запроса в серверную службу
titleSuffix: Azure API Management
description: Пример политики для службы управления API Azure. Отправка контекстной информации запроса во внутреннюю службу.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 8cef989ca7ce8ee649c4f4aeb579c43bf8a8d6ab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75442407"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Отправка контекстной информации запроса во внутреннюю службу

В этой статье приведен пример политики для службы управления API Azure. Он демонстрирует отправку контекстной информации запроса во внутреннюю службу. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Дальнейшие шаги

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

