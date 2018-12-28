---
title: Пример политики службы управления API Azure. Использование OAuth2 для авторизации между шлюзом и серверной частью | Документация Майкрософт
description: Пример политики службы управления API Azure. Использование OAuth2 для авторизации между шлюзом и серверной частью. В статье показано, как получить маркер доступа из AAD и перенаправить его в серверную часть.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 519233cb9e77bf48f67d869a54af771c17c7827e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874579"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Использование OAuth2 для авторизации между шлюзом и серверной частью

В этой статье на примере политики службы управления API Azure показано, как использовать OAuth2 для авторизации между шлюзом и серверной частью. В статье показано, как получить маркер доступа из AAD и перенаправить его в серверную часть. 

См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

Следующий сценарий использует свойства, которые отображаются в {{property}}. Чтобы узнать о свойствах и их использовании в политиках управления API, ознакомьтесь с [этим](../api-management-howto-properties.md) разделом.
 
## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Дополнительная информация

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

