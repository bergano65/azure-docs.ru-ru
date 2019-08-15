---
title: включение файла
description: включение файла
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 94baeb3d459b700cc95d88fb82f995957640aab6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012263"
---
`objectIdType` (или **тип идентификатора объекта**) ссылается на тип удостоверения, назначенный роли. Помимо типов `DeviceId` и `UserDefinedFunctionId`, типы идентификаторов объектов соответствуют свойствам объектов Azure Active Directory.

Следующая таблица содержит поддерживаемые типы идентификаторов объектов в Azure Digital Twins:

| Тип | Описание |
| --- | --- |
| Идентификатор пользователя | Назначает роль для пользователя. |
| Идентификатор устройства | Назначает роль для устройства. |
| DomainName | Назначает роль для доменного имени. Каждому пользователю с указанным именем домена предоставлены права на доступ к соответствующей роли. |
| TenantId | Назначает роль для клиента. Каждому пользователю, принадлежащему к указанному идентификатору клиента Azure Active Directory, будут предоставлены права на доступ к соответствующей роли. |
| ServicePrincipalId | Назначает роль для идентификатора объекта субъекта-службы. |
| UserDefinedFunctionId | Назначает роль для определяемой пользователем функции (UDF). |