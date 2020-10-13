---
title: Пример политики управления API — фильтрация по IP-адресу при использовании шлюза приложений
titleSuffix: Azure API Management
description: Пример политики службы управления API Azure. здесь показано, как выполнить фильтрацию по IP-адресу запроса при использовании шлюза приложений.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "75942481"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Фильтровать по IP-адресу запроса при использовании шлюза приложений

В этой статье показан пример политики службы управления API Azure, демонстрирующий фильтрацию по IP-адресу запроса при доступе к экземпляру управления API через шлюз приложений или другую промежуточную подпрограмму. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики ограничения доступа в службе управления API](../api-management-access-restriction-policies.md)
+ [Примеры политик](../policy-samples.md).
