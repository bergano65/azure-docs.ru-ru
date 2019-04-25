---
title: Пример политики службы управления API Azure. Реализация шаблона X-CSRF | Документация Майкрософт
description: Пример политики службы управления API Azure. Реализация шаблона X-CSRF, который используется во многих API. Этот пример относится только к шлюзу SAP.
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
ms.openlocfilehash: 2f4d26702443ef3113dad98cde1d13b292fe657a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306705"
---
# <a name="implement-x-csrf-pattern"></a>Реализация шаблона X-CSRF

В этой статье на примере политики службы управления API Azure показано, как реализовать шаблон X-CSRF, который используется во многих интерфейсах API. Этот пример относится только к шлюзу SAP. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

