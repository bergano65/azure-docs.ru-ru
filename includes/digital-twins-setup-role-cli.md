---
author: baanders
description: включить файл для требования роли в программе установки Azure Digital двойников
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 1714fac465531a2d3a5829e7080132510ccd1ec4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407476"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Необходимые условия: требования к разрешениям

Чтобы выполнить все действия, описанные в этой статье, необходимо классифицировать в подписке Azure как владелец. 

Вы можете проверить уровень разрешений, выполнив следующую команду в Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Если вы являетесь владельцем, `roleDefinitionName` значением в выходных данных будет *owner*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Окно Cloud Shell, в котором отображаются выходные данные команды AZ Role назначение List":::

Если вы обнаружите, что значение является *участником* или что-либо, кроме *owner*, можно перейти одним из следующих способов.
* Обратитесь к владельцу подписки и запросите владельца, чтобы выполнить действия, описанные в этой статье от вашего имени.
* Обратитесь к владельцу подписки или к кому-либо с ролью администратора доступа пользователей в подписке и попросите его повысить владельца подписки, чтобы у вас были разрешения на продолжение работы. Зависит ли это от вашей организации и вашей роли в ней.