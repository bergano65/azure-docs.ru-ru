---
title: Пример политики службы управления API Azure. Добавление заголовка перенаправления | Документация Майкрософт
description: Пример политики службы управления API Azure. Способы добавления заголовка перенаправления во входящий запрос для обеспечения создания серверным API правильных URL-адресов.
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
ms.openlocfilehash: b857d1780e9734ce891ce2c0ce4bedf50dfe13e9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859500"
---
# <a name="add-a-forwarded-header"></a>Добавление заголовка перенаправления

В этой статье на примере политики службы управления API Azure показано, как добавить заголовок перенаправления во входящий запрос, чтобы обеспечить создание серверным API правильных URL-адресов. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="code"></a>Код

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).
