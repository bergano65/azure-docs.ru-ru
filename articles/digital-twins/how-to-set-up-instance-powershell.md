---
title: Настройка экземпляра и проверки подлинности (PowerShell)
titleSuffix: Azure Digital Twins
description: См. раздел Настройка экземпляра службы Digital двойников с помощью Azure PowerShell
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65495da13bc6c9ed91c1ecbd587c16c949136d73
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98040694"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Настройка экземпляра и проверки подлинности Azure Digital двойников (PowerShell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

В этой статье описаны действия по **настройке нового экземпляра Azure Digital двойников**, включая создание экземпляра и настройку проверки подлинности. По завершении работы с этой статьей у вас будет готовый экземпляр Azure Digital двойников для начала программирования.

Эта версия этой статьи выполняет эти действия вручную, по одной с помощью Azure PowerShell.

* Чтобы выполнить эти действия вручную с помощью портал Azure, см. версию портала этой статьи: инструкции по [*настройке экземпляра и проверки подлинности (портал)*](how-to-set-up-instance-portal.md).
* Чтобы выполнить автоматическую установку с помощью примера скрипта развертывания, см. статью [*как настроить экземпляр и проверку подлинности (в скрипте)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>Подготовка среды

1. Если вы используете Azure PowerShell локально:
   1. [Установите модуль Az PowerShell.](/powershell/azure/install-az-ps)
   1. Подключитесь к учетной записи Azure с помощью командлета [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).
1. Если вы используете Azure Cloud Shell:
   1. Дополнительные сведения см. в статье [Общие сведения об Azure Cloud Shell](../cloud-shell/overview.md).
   1. На панели значков Cloud Shell убедитесь, что Cloud Shell настроена для запуска версии PowerShell.

      :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Окно Cloud Shell, в котором отображается выбор версии PowerShell":::

1. Если вы используете несколько подписок Azure, выберите ту, за ресурсы в которой будут выставляться счета. Выберите требуемую подписку с помощью командлета [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Если вы впервые используете Azure Digital двойников с этой подпиской, необходимо зарегистрировать поставщик ресурсов **Microsoft. дигиталтвинс** .

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

> [!IMPORTANT]
> Пока модуль PowerShell **AZ. дигиталтвинс** находится на этапе предварительной версии, его необходимо установить отдельно с помощью `Install-Module` командлета. Как только этот модуль PowerShell станет общедоступным, он будет включен в один из будущих выпусков модуля Az PowerShell и по умолчанию встроен в Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.DigitalTwins
```

## <a name="create-the-azure-digital-twins-instance"></a>Создание экземпляра Digital двойников для Azure

В этом разделе вы **создадите новый экземпляр Azure Digital двойников** с помощью Azure PowerShell.
Необходимо указать:

* [Группа ресурсов Azure](../azure-resource-manager/management/overview.md). Если у вас еще нет группы ресурсов, можно создать ее с помощью командлета [New-азресаурцеграуп](/powershell/module/az.resources/new-azresourcegroup) .

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* Регион для развертывания. Чтобы узнать, какие регионы поддерживают Azure Digital двойников, посетите страницу [*продукты Azure, доступные по регионам*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Имя экземпляра. Имя нового экземпляра должно быть уникальным в пределах региона для вашей подписки. Если в вашей подписке есть другой экземпляр Azure Digital двойников в регионе, где уже используется указанное имя, вам будет предложено выбрать другое имя.

Чтобы создать экземпляр, используйте значения, приведенные в следующем примере:

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>Проверка успешности и получение важных значений

Если экземпляр успешно создан, результат будет выглядеть примерно так, как показано ниже, содержащий сведения о созданном ресурсе.

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

> [!TIP]
> Эти свойства и все свойства экземпляра можно просмотреть в любое время, запустив и направляется `Get-AzDigitalTwinsInstance` в `Select-Object -Property *` .

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

Обратите внимание на имя **узла**, **имя** и **ResourceGroup** двойников экземпляра Azure Digital. Это важные значения, которые могут потребоваться при продолжении работы с вашим экземпляром Azure Digital двойников для настройки проверки подлинности и связанных ресурсов Azure. Если другие пользователи будут программироваться на экземпляре, вы должны использовать эти значения совместно с ними.

Теперь у вас есть экземпляр Azure Digital двойников, готовый к работе. Далее вы получите соответствующие разрешения пользователя Azure для управления им.

## <a name="set-up-user-access-permissions"></a>Настройка разрешений доступа пользователей

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Определите **ObjectID** для учетной записи Azure AD пользователя, которому должна быть назначена роль, с помощью командлета [Get-азадусер](/powershell/module/az.resources/get-azaduser) .

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

Чтобы назначить роль, используйте следующую команду. Он должен быть запущен пользователем с [достаточными разрешениями](#prerequisites-permission-requirements) в подписке Azure. Команда требует передачи **ObjectID** для учетной записи Azure AD пользователя, которому должна быть назначена роль. Кроме того, необходимо использовать один и тот же идентификатор подписки, имя группы ресурсов и имя экземпляра Azure Digital двойников, которые вы выбрали ранее.

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-id-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

Результат этой команды выводит сведения о созданном назначении роли.

### <a name="verify-success"></a>Проверка успешного выполнения

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Теперь у вас есть экземпляр Azure Digital двойников, который готов к работе и ему назначены разрешения для управления им.

## <a name="next-steps"></a>Дальнейшие действия

См. раздел как подключить клиентское приложение к экземпляру с помощью кода проверки подлинности:
* [*Пошаговое руководство. Написание кода проверки подлинности приложения*](how-to-authenticate-client.md)
