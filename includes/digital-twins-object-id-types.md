---
title: включение файла
description: включение файла
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/20/2019
ms.custom: include file
ms.openlocfilehash: e46041a33c12b3fcb40e5a04de11108471e59855
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307217"
---
`objectIdType` (или **тип идентификатора объекта**) ссылается на тип удостоверения, назначенный роли. Помимо типов `DeviceId` и `UserDefinedFunctionId`, типы идентификаторов объектов соответствуют свойствам объектов Azure Active Directory.

Следующая таблица содержит поддерживаемые типы идентификаторов объектов в Azure Digital Twins:

| введите | ОПИСАНИЕ |
| --- | --- |
| UserId | Назначает роль для пользователя. |
| deviceId | Назначает роль для устройства. |
| DomainName | Назначает роль для доменного имени. Каждому пользователю с указанным именем домена предоставлены права на доступ к соответствующей роли. |
| TenantId | Назначает роль для клиента. Каждому пользователю, принадлежащему к указанному идентификатору клиента Azure Active Directory, будут предоставлены права на доступ к соответствующей роли. |
| ServicePrincipalId | Назначает роль для идентификатора объекта субъекта-службы. |
| UserDefinedFunctionId | Назначает роль для определяемой пользователем функции (UDF). |