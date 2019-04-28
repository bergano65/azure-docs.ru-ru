---
title: включение файла
description: включение файла
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 40ab53c941a7ac619ebb09d381a4ae0450f26e8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60534149"
---
`objectIdType` (или **тип идентификатора объекта**) ссылается на тип удостоверения, назначенный роли. Помимо типов `DeviceId` и `UserDefinedFunctionId`, типы идентификаторов объектов соответствуют свойствам объектов Azure Active Directory.

Следующая таблица содержит поддерживаемые типы идентификаторов объектов в Azure Digital Twins:

| type | ОПИСАНИЕ |
| --- | --- |
| UserId | Назначает роль для пользователя. |
| deviceId | Назначает роль для устройства. |
| DomainName | Назначает роль для доменного имени. Каждому пользователю с указанным именем домена предоставлены права на доступ к соответствующей роли. |
| TenantId | Назначает роль для клиента. Каждому пользователю, принадлежащему к указанному идентификатору клиента Azure Active Directory, будут предоставлены права на доступ к соответствующей роли. |
| ServicePrincipalId | Назначает роль для идентификатора объекта субъекта-службы. |
| UserDefinedFunctionId | Назначает роль для определяемой пользователем функции (UDF). |