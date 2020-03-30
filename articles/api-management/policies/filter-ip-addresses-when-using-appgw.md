---
title: Пример политики управления API - Фильтр на IP-адрес при использовании шлюза приложения
titleSuffix: Azure API Management
description: Выборка политики управления API Azure - демонстрирует, как фильтровать IP-адрес запроса при использовании шлюза приложения.
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942481"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Фильтр по IP-адресу запроса при использовании шлюза приложения

В этой статье показана выборка политики управления API Azure, которая демонстрирует, как фильтрнать IP-адрес запроса при доступе к экземпляру API Management через шлюз приложения или другого посредника. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики ограничения доступа в службе управления API](../api-management-access-restriction-policies.md)
+ [Примеры политик](../policy-samples.md).
