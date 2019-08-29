---
title: Пример политики службы управления API Azure. Отправка ошибок в Stackify для записи в журнал | Документация Майкрософт
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
ms.openlocfilehash: 82aab34a9815f080fe8abb8c1d8b6de66866806e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067582"
---
# <a name="send-errors-to-stackify-for-logging"></a>Отправка ошибок Stackify для записи в журнал

В этой статье на примере политики службы управления API Azure показано, как добавить политику регистрации ошибок для отправки ошибок в Stackify и их записи в журнал. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **on-error**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Следующие шаги

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

