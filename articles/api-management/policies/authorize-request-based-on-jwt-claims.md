---
title: Пример политики управления API — авторизация доступа на основе утверждений JWT
titleSuffix: Azure API Management
description: Пример политики управления API Azure. Авторизации доступа к определенным методам HTTP API на основе утверждений JWT.
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
ms.openlocfilehash: 1b84854688fbdcc017b16698dfebfe54a7210110
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75422178"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Авторизация доступа на основе утверждений JWT

В этой статье на примере политики службы управления API Azure показано, как авторизовать доступ к определенным методам HTTP API на основе утверждений JWT. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Дальнейшие шаги

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

