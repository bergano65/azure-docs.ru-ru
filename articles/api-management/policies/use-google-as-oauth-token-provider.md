---
title: Пример политики службы управления API Azure. Авторизации доступа с помощью токена OAuth Google | Документация Майкрософт
description: Пример политики службы управления API Azure. Авторизация доступа к конечным точкам, используя Google в качестве поставщика токена OAuth.
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
ms.openlocfilehash: 7acc9071008937cd85c628878b385f1f53707e53
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071928"
---
# <a name="authorize-access-using-google-oauth-token"></a>Авторизации доступа с помощью токена OAuth Google

В этой статье на примере политики службы управления API Azure показано, как авторизовать доступ к конечным точкам, используя Google в качестве поставщика токена OAuth. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Следующие шаги

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

