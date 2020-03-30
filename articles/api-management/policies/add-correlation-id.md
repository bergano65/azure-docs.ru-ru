---
title: Пример политики управления API - Добавить заголовок, содержащий идентификатор корреляции
titleSuffix: Azure API Management
description: Пример политик управления API Azure. Добавление заголовка с идентификатором корреляции во входящий запрос.
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
ms.openlocfilehash: 79910aa60602a80cbe79c4ce9899f6ff73fbfde9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422256"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Добавление заголовка, который содержит идентификатор корреляции

В этой статье на примере политики службы управления API Azure показано, как добавить заголовок, который содержит идентификатор корреляции для входящего запроса. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

