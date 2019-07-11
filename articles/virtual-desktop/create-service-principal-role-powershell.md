---
title: Создание субъектов-служб и назначений ролей в предварительной версии Виртуального рабочего стола Windows с помощью PowerShell — Azure
description: Из этой статьи вы узнаете, как создать субъекты-службы и назначить роли с помощью PowerShell в предварительной версии Виртуального рабочего стола Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 44c823653ecbad1c4dd1fd35b676c8a6d8bd1620
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206662"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Руководство по Создание субъектов-служб и назначений ролей с использованием PowerShell

Субъекты-службы — это удостоверения, которые вы можете создать в Azure Active Directory для назначения ролей и разрешений с определенной целью. В предварительной версии Виртуального рабочего стола Windows вы можете создать субъект-службу для решения следующих задач:

- автоматизация определенных задач управления Виртуального рабочего стола Windows.
- использование в качестве учетных данных вместо пользователей Многофакторной идентификации при использовании какого-либо из шаблонов Resource Manager в Виртуальном рабочем столе Windows.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создайте субъект-службу в Azure Active Directory.
> * создание назначения роли в Виртуальном рабочем столе Windows.
> * вход в Виртуальный рабочий стол Windows с помощью субъекта-службы.

## <a name="prerequisites"></a>Предварительные требования

Перед созданием субъектов-служб и назначений ролей вам необходимо будет сделать следующее:

1. Установите модуль Azure AD. Чтобы установить модуль, запустите PowerShell как администратор и запустите следующий командлет:

    ```powershell
    Install-Module AzureAD
    ```

2. Запустите указанные ниже командлеты, заменив значения в кавычках значениями для своего сеанса.

    ```powershell
    $myTenantName = "<my-tenant-name>"
    ```

3. Вам нужно выполнить все инструкции в этой статье в рамках одного сеанса PowerShell. Если вы закроете оно и продолжите позже, операция, возможно, не будет выполнена.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Создание субъекта-службы в Azure Active Directory

После того как вы выполните все предварительные требования в сеансе PowerShell, запустите указанные ниже командлеты PowerShell, чтобы создать мультитенатный субъект-службу в Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Создание назначения роли в предварительной версии Виртуального рабочего стола Windows

Теперь, создав субъект-службу, вы можете воспользоваться им для входа в Виртуальный рабочий стол Windows. Обязательно войдите с учетной записью, у которой есть разрешения на создание назначений ролей.

Сначала [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) для использования в сеансе PowerShell (если вы еще это не сделали).

Запустите указанные ниже командлеты PowerShell, чтобы подключиться к Виртуальному рабочему столу Windows и создать назначение роли для субъекта-службы.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Вход с помощью субъекта-службы

После создания назначения ролей для субъекта-службы удостоверьтесь, что субъект-служба может войти в Виртуальный рабочий стол Windows, запустив следующий командлет:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Войдя, убедитесь, что все работает правильно, протестировав несколько командлетов PowerShell в Виртуальном рабочем столе Windows с использованием субъекта-службы.

## <a name="view-your-credentials-in-powershell"></a>Просмотр учетных данных в PowerShell

Прежде чем завершить сеанс PowerShell, просмотрите свои учетные данные и запишите их для дальнейшего использования. Особенно это касается пароля, так как вы не сможете получить его после закрытия этого сеанса PowerShell.

Ниже приведены учетные данные, которые вам нужно записать, и командлеты, которые необходимо запустить, чтобы получить их.

- Пароль:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Идентификатор клиента:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Идентификатор приложения:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="next-steps"></a>Дополнительная информация

После создания субъекта-службы и назначения ему роли в клиенте Виртуального рабочего стола Windows его можно использовать для создания пула узлов. Дополнительные сведения см. в руководстве по созданию пула узлов в Виртуальном рабочем столе Windows.

 > [!div class="nextstepaction"]
 > [Руководство по созданию пула узлов в Виртуальном рабочем столе Windows](./create-host-pools-azure-marketplace.md)
