---
author: baanders
description: включить файл для требования роли в программе установки Azure Digital двойников
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 2f10a6915a3edf673316ae9151b6052442678ef9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832363"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Необходимые условия: требования к разрешениям

Чтобы выполнить все действия, описанные в этой статье, необходимо классифицировать в подписке Azure как владелец. 

Вы можете проверить уровень разрешений, выполнив следующую команду в Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

> [!NOTE]
> Если эта команда возвращает ошибку, сообщающую, что CLI **не может найти пользователя или субъект-службу в базе данных Graph**:
>
> Используйте *идентификатор объекта* пользователя вместо электронной почты в оставшейся части этой статьи. Это может произойти для пользователей в личных [учетных записях Майкрософт (MSAS)](https://account.microsoft.com/account). 
>
> Используйте [страницу портал Azure Azure Active Directory пользователей](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) , чтобы выбрать учетную запись пользователя и открыть сведения о ней. Скопируйте идентификатор *ObjectID*пользователя:
>
> :::image type="content" source="../articles/digital-twins/media/includes/user-id.png" alt-text="Представление страницы пользователя в портал Azure выделение идентификатора GUID в поле &quot;идентификатор объекта&quot;" lightbox="../articles/digital-twins/media/includes/user-id.png":::
>
> Затем повторите команду списка назначений ролей, используя *идентификатор объекта* пользователя вместо сообщения электронной почты.

После выполнения команды Список назначений ролей, если вы являетесь владельцем, `roleDefinitionName` значением в выходных данных будет *owner*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Представление страницы пользователя в портал Azure выделение идентификатора GUID в поле &quot;идентификатор объекта&quot;":::

Если вы обнаружите, что значение является *участником* или что-либо, кроме *owner*, можно перейти одним из следующих способов.
* Обратитесь к владельцу подписки и запросите владельца, чтобы выполнить действия, описанные в этой статье от вашего имени.
* Обратитесь к владельцу подписки или к кому-либо с ролью администратора доступа пользователей в подписке и попросите его повысить владельца подписки, чтобы у вас были разрешения на продолжение работы. Зависит ли это от вашей организации и вашей роли в ней.