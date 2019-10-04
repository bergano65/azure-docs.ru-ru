---
title: включение файла
description: включение файла
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 12d4278171d43fdaf8613a1c91bdbffc269adc56
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949052"
---
`objectIdType` (или **тип идентификатора объекта**) ссылается на тип удостоверения, назначенный роли. Помимо типов `DeviceId` и `UserDefinedFunctionId`, типы идентификаторов объектов соответствуют свойствам объектов Azure Active Directory.

Следующая таблица содержит поддерживаемые типы идентификаторов объектов в Azure Digital Twins:

| Type | Описание |
| --- | --- |
| Идентификатор пользователя | Назначает роль для пользователя. |
| Идентификатор устройства | Назначает роль для устройства. |
| DomainName | Назначает роль для доменного имени. Каждому пользователю с указанным именем домена предоставлены права на доступ к соответствующей роли. |
| TenantId | Назначает роль для клиента. Каждому пользователю, принадлежащему к указанному идентификатору клиента Azure Active Directory, будут предоставлены права на доступ к соответствующей роли. |
| ServicePrincipalId | Назначает роль для идентификатора объекта субъекта-службы. |
| UserDefinedFunctionId | Назначает роль для определяемой пользователем функции (UDF). |