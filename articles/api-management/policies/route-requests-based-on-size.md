---
title: Пример политики управления API — запрос маршрута на основе размера текста сообщения
titleSuffix: Azure API Management
description: Пример политики службы управления API Azure. Маршрутизация запроса на основе размера его текста.
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
ms.openlocfilehash: 8cee2c13386a076f0321619754468cfc1e9fb31c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75442426"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Маршрутизация запроса на основе размера его текста

В этой статье на примере политики службы управления API Azure показано, как настроить маршрутизацию запроса на основе размера его текста. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Дальнейшие шаги

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

