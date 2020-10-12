---
title: Пример политики управления API — Отправка ошибок в Stackify для ведения журнала
titleSuffix: Azure API Management
description: Пример политики службы управления API Azure. Добавление политики регистрации ошибок для отправки ошибок в Stackify и их записи в журнал.
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
ms.openlocfilehash: 6662761df005211729dffb16282b8e0a8e2a8444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442443"
---
# <a name="send-errors-to-stackify-for-logging"></a>Отправка ошибок Stackify для записи в журнал

В этой статье на примере политики службы управления API Azure показано, как добавить политику регистрации ошибок для отправки ошибок в Stackify и их записи в журнал. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **on-error**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

