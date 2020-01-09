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
ms.openlocfilehash: e286f77009a17870332ed5caf1c66c18dd21d020
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442472"
---
# <a name="add-capabilities-to-a-backend-service"></a>Добавление функций в серверную службу

В этой статье на примере политики службы управления API Azure показано, как добавить функции в серверную службу. В статье показано, как принять имя вместо широты и долготы в API прогнозирования погоды. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

