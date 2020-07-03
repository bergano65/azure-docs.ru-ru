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
ms.openlocfilehash: 0f003bc268af6b7f8bd6b046ae84734dbefeac28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75442460"
---
# <a name="generate-shared-access-signature"></a>Создание подписанного URL-адреса

В этой статье на примере политики службы управления API Azure показано, как создать [подписанный URL-адрес](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) с помощью выражений и перенаправить запрос в службу хранилища Azure с использованием политики rewrite-uri. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

