---
title: Пример политики управления API — Создание подписи общего доступа
titleSuffix: Azure API Management
description: Пример политики службы управления API Azure. Создание подписанного URL-адреса с помощью выражений и перенаправление запроса в службу хранилища Azure с использованием политики rewrite-uri.
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
ms.openlocfilehash: 62a6e8c9fdec3b30bd193e9887d7e0cb7926e73e
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86243007"
---
# <a name="generate-shared-access-signature"></a>Создание подписанного URL-адреса

В этой статье на примере политики службы управления API Azure показано, как создать [подписанный URL-адрес](../../storage/common/storage-sas-overview.md) с помощью выражений и перенаправить запрос в службу хранилища Azure с использованием политики rewrite-uri. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).
