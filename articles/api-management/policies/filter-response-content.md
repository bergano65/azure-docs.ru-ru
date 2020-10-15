---
title: Пример политики службы управления API Azure. Фильтрация содержимого ответа | Документация Майкрософт
description: Пример политики службы управления API Azure. Фильтрация элементов данных из полезных данных ответа по продукту, связанному с запросом.
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
ms.openlocfilehash: 1f2794c2d72dd460f0b3edf5fb7ec4035746c6e4
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078448"
---
# <a name="filter-response-content"></a>Фильтрация содержимого ответа

В этой статье на примере политики службы управления API Azure показано, как фильтровать элементы данных из полезных данных ответа по продукту, связанному с запросом. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-reference.md).

## <a name="policy"></a>Политика

Вставьте код в блок **outbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-reference.md).