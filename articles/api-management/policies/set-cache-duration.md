---
title: Пример политики управления API. Настройка длительности кэша ответов
titleSuffix: Azure API Management
description: В примере политики управления API Azure показано, как задать длительность кэширования ответа при помощи значения maxAge в заголовке Cache-Control, который отправляется с сервера.
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
ms.openlocfilehash: 3101c5695272e8fa6b577ad313897cbc1fa29629
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75442398"
---
# <a name="set-response-cache-duration"></a>Установка длительности для кэширования ответа

В статье приведен пример политики управления API Azure, в котором показано, как задать длительность кэширования ответа при помощи значения maxAge в заголовке Cache-Control, который отправляется с сервера. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

