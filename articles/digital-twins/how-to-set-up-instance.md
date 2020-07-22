---
title: Создание экземпляра Azure Digital Twins
titleSuffix: Azure Digital Twins
description: См. раздел Настройка экземпляра службы Digital двойников.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fecacbd2c7c6549a1321367157bb179321779ca9
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206507"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Настроить экземпляр Azure Digital Twins.

В этой статье описаны основные шаги по настройке нового экземпляра Digital двойников для Azure. Это включает создание экземпляра и назначение ему разрешений [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) для самого экземпляра.

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Настроить экземпляр Azure Digital Twins.

Далее предстоит создать новую группу ресурсов Azure для использования в этом пошаговом окне. Затем можно **создать новый экземпляр Azure Digital двойников** внутри этой группы ресурсов. 

Вам также потребуется указать имя для экземпляра и выбрать регион для развертывания. Чтобы узнать, какие регионы поддерживают Azure Digital двойников, посетите страницу [продукты Azure, доступные по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

>[!NOTE]
> Имя нового экземпляра должно быть уникальным в пределах региона (т. е. Если другой экземпляр Azure Digital двойников в этом регионе уже использует выбранное имя, вам потребуется выбрать другое имя).

Создайте группу ресурсов и экземпляр с помощью следующих команд:

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

Результат этих команд выглядит примерно так: выводить сведения о созданных ресурсах.

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="командное окно с успешным созданием группы ресурсов и экземпляра Azure Digital двойников":::

Обратите внимание на имя *узла*, *имени*и группы Azure Digital *двойников из выходных* данных. Это все ключевые значения, которые могут потребоваться при продолжении работы с вашим экземпляром Azure Digital двойников для настройки проверки подлинности и связанных ресурсов Azure.

> [!TIP]
> Эти свойства и все свойства экземпляра можно просмотреть в любое время, запустив `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

### <a name="assign-azure-active-directory-permissions"></a>Назначение разрешений Azure Active Directory

Azure Digital двойников использует [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) для управления доступом на основе РОЛЕЙ (RBAC). Это означает, что перед тем, как вы сможете выполнять вызовы плоскости данных в ваш экземпляр Azure Digital двойников, сначала необходимо назначить себе роль с этими разрешениями.

Чтобы использовать Azure Digital двойников с клиентским приложением, вам также нужно убедиться, что клиентское приложение может пройти проверку подлинности в Azure Digital двойников. Это делается путем настройки регистрации приложения Azure Active Directory (AAD), о которой можно ознакомиться в статье [как проверить подлинность клиентского приложения](how-to-authenticate-client.md).

#### <a name="assign-yourself-a-role"></a>Назначение роли

Создайте назначение ролей для себя в экземпляре Azure Digital двойников, используя электронное письмо, связанное с клиентом AAD в подписке Azure. 

Чтобы сделать это, необходимо классифицировать в подписке Azure как владелец. Это можно проверить, выполнив `az role assignment list --assignee <your-Azure-email>` команду и убедившись в выходе, что значение *Роледефинитионнаме* является *owner*. Если вы обнаружите, что это значение является *участником* или что-либо, кроме *owner*, обратитесь к администратору подписки с помощью Power, чтобы предоставить разрешения в подписке. Они могут либо повысить уровень роли во всей подписке, чтобы можно было выполнить следующую команду, либо владелец может выполнить следующую команду от вашего имени, чтобы настроить разрешения Azure Digital двойников.

Чтобы назначить разрешения пользователя "владелец" в экземпляре Digital двойников Azure, используйте следующую команду (она должна выполняться владельцем подписки Azure):

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Результат этой команды выводит сведения о созданном назначении роли.

> [!TIP]
> Если вместо этого вы получаете ошибку *400: BadRequest* , выполните следующую команду, чтобы получить *ObjectID* для пользователя:
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> Затем повторите команду назначения роли, используя *идентификатор объекта* пользователя вместо сообщения электронной почты.

Теперь у вас есть экземпляр Azure Digital двойников, готовый к работе, и разрешения на управление им.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как настроить и проверить подлинность клиентского приложения для работы с вашим экземпляром.
* [Пошаговое руководство. Проверка подлинности клиентского приложения](how-to-authenticate-client.md)
