---
title: Пример политики управления API — авторизация запроса с помощью внешнего средства авторизации
titleSuffix: Azure API Management
description: Пример политики службы управления API демонстрирует, как авторизация запросов выполняется с использованием внешнего авторизатора, инкапсулирующего пользовательскую или устаревшую логику аутентификации или авторизации.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 99bf1068042eb7ab0c43e2a683ca7116d2e426f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75442499"
---
# <a name="authorize-requests-using-external-authorizer"></a>Авторизация запросов с помощью внешнего авторизатора

В этой статье показан пример политики службы управления API Azure, который демонстрирует, как защитить доступ к API с помощью внешнего авторизатора, инкапсулирующего пользовательскую логику аутентификации или авторизации. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики ограничения доступа в службе управления API](../api-management-access-restriction-policies.md)
+ [Примеры политик](../policy-samples.md).
