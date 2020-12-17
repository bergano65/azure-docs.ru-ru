---
title: Назначение управляемого удостоверения роли приложения с помощью PowerShell — Azure AD
description: Пошаговые инструкции по назначению управляемому удостоверению доступа к роли другого приложения с помощью PowerShell.
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8890eb76e3f9521aa5070789f969ffeb8f3e4ec6
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618929"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>Назначение управляемому удостоверению доступа к роли приложения с помощью PowerShell

Управляемые удостоверения для ресурсов Azure предоставляют службы Azure с удостоверением в Azure Active Directory. Они работают без необходимости учетных данных в коде. Службы Azure используют это удостоверение для проверки подлинности в службах, поддерживающих проверку подлинности Azure AD. Роли приложений предоставляют форму управления доступом на основе ролей и позволяют службе реализовывать правила авторизации.

Из этой статьи вы узнаете, как назначить управляемое удостоверение роли приложения, предоставляемой другим приложением с помощью Azure AD PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#managed-identity-types)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Выполнить примеры скриптов можно двумя способами:
    - используйте службу [Azure Cloud Shell](../../cloud-shell/overview.md), которую можно открыть с помощью кнопки **Попробовать** в правом верхнем углу блоков кода.
    - Выполните сценарии локально, установив последнюю версию [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

## <a name="use-azure-ad-to-assign-a-managed-identity-access-to-another-applications-app-role"></a>Назначение управляемому удостоверению доступа к роли приложения другого приложения с помощью Azure AD

1. Включите управляемое удостоверение в ресурсе Azure, [например на виртуальной машине Azure](qs-configure-powershell-windows-vm.md).

1. Найдите идентификатор объекта субъекта-службы управляемого удостоверения.

   **Для управляемого удостоверения, назначенного системой**, можно найти идентификатор объекта в портал Azure на странице **удостоверение** ресурса. Для поиска идентификатора объекта можно также использовать следующий сценарий PowerShell. Вам потребуется идентификатор ресурса, созданный на шаге 1, который доступен в портал Azure на странице **свойств** ресурса.

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **Для управляемого удостоверения, назначенного пользователем**, можно найти идентификатор объекта управляемого удостоверения на портал Azure на странице **обзора** ресурса. Для поиска идентификатора объекта можно также использовать следующий сценарий PowerShell. Вам потребуется идентификатор ресурса назначаемого пользователем управляемого удостоверения.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Создайте новую регистрацию приложения, представляющую службу, которой ваш управляемый идентификатор будет отсылать запрос. Если API или служба, которые предоставляют управляемому удостоверению, уже имеют субъект-службу в клиенте Azure AD, пропустите этот шаг. Например, если вы хотите предоставить управляемому удостоверению доступ к Microsoft Graph API, этот шаг можно пропустить.

1. Найдите идентификатор объекта субъекта-службы приложения службы. Его можно найти с помощью портал Azure. Перейдите в Azure Active Directory и откройте страницу " **корпоративные приложения** ", найдите приложение и найдите **идентификатор объекта**. Идентификатор объекта субъекта-службы также можно найти по его отображаемому имени с помощью следующего сценария PowerShell:

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > Отображаемые имена приложений не являются уникальными, поэтому следует убедиться, что получен правильный субъект-служба приложения.

1. Добавьте [роль приложения](../develop/howto-add-app-roles-in-azure-ad-apps.md) в приложение, созданное на шаге 3. Роль можно создать с помощью портал Azure или с помощью Microsoft Graph. Например, можно добавить роль приложения следующим образом:

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Назначьте роль приложения управляемому удостоверению. Для назначения роли приложения потребуются следующие сведения:
    * `managedIdentityObjectId`— Идентификатор объекта субъекта-службы управляемого удостоверения, который вы нашли на шаге 2.
    * `serverApplicationObjectId`— Идентификатор объекта субъекта-службы серверного приложения, который вы нашли на шаге 4.
    * `appRoleId`: идентификатор роли приложения, предоставляемой серверным приложением, созданным на шаге 5. в этом примере идентификатор роли приложения — `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` .
   
   Выполните следующий сценарий PowerShell, чтобы добавить назначение роли:

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverApplicationObjectId
    ```

## <a name="next-steps"></a>Дальнейшие действия

- [Что такое управляемые удостоверения для ресурсов Azure?](overview.md)
- Дополнительные сведения см. в статье [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью PowerShell](qs-configure-powershell-windows-vm.md).
