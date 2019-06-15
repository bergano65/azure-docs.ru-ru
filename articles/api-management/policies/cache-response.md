---
title: Пример политики службы управления API Azure. Добавление функций в серверную службу | Документация Майкрософт
description: Пример политики службы управления API Azure. Способы добавления функций в серверную службу. В статье показано, как принять имя вместо широты и долготы в API прогнозирования погоды.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 7c9edbf4b2d231453cd336521a04ba6b7714b696
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61062213"
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

