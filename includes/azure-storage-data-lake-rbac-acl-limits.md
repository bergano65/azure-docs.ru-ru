---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 8b793a28c31df185555b9a548df52e4beeccf305
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131638"
---
| Механизм | Область |Ограничения | Поддерживаемый уровень разрешений |
|---|---|---|---|
| RBAC | Учетные записи хранения, контейнеры. <br>Назначения ролей с разными ресурсами RBAC на уровне подписки или группы ресурсов. | 2000. назначения ролей RBAC в подписке | Роли RBAC (встроенные или пользовательские) |
| СПИСКОМ| Каталог, файл |32. записи ACL (фактически 28 записей ACL) для каждого файла и каталога. Списки ACL для доступа и по умолчанию имеют собственный предел записей ACL 32. |Разрешение ACL|
