---
title: Пример политики управления API — добавление возможностей в серверную службу
titleSuffix: Azure API Management
description: Пример политики службы управления API Azure. Способы добавления функций в серверную службу. В статье показано, как принять имя вместо широты и долготы в API прогнозирования погоды.
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
ms.openlocfilehash: 64db4e5451425002eeaac11695ac011a96047d9b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076187"
---
# <a name="add-capabilities-to-a-backend-service"></a>Добавление функций в серверную службу

В этой статье на примере политики службы управления API Azure показано, как добавить функции в серверную службу. В статье показано, как принять имя вместо широты и долготы в API прогнозирования погоды. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-reference.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-reference.md).