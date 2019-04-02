---
title: Создание субъектов-служб и назначений ролей в предварительной версии Виртуального рабочего стола Windows с помощью PowerShell — Azure
description: Из этой статьи вы узнаете, как создать субъекты-службы и назначить роли с помощью PowerShell в предварительной версии Виртуального рабочего стола Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 1bbe89484d72a21c4432d452d4ddae83ea2d2553
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400028"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell"></a>Руководство по Создание субъектов-служб и назначений ролей с помощью PowerShell

Субъекты-службы — это удостоверения, которые вы можете создать в Azure Active Directory для назначения ролей и разрешений с определенной целью. В предварительной версии Виртуального рабочего стола Windows вы можете создать субъект-службу для решения следующих задач:

- автоматизация определенных задач управления Виртуального рабочего стола Windows;
- использование в качестве учетных данных вместо пользователей многофакторной идентификации при использовании какого-либо из шаблонов Resource Manager в Виртуальном рабочем столе Windows.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание субъекта-службы в Azure Active Directory
> * создание назначения роли в Виртуальном рабочем столе Windows;
> * вход в Виртуальный рабочий стол Windows с помощью субъекта-службы.

## <a name="prerequisites"></a>Предварительные требования

Перед созданием субъектов-служб и назначений ролей вам необходимо сделать следующее:

1. Установите модуль Azure AD. Чтобы установить модуль, запустите PowerShell как администратор и запустите следующий командлет:

    ```powershell
    Install-Module AzureAD
    ```

2. Запустите указанные ниже командлеты, заменив значения в кавычках значениями для своего сеанса.

    ```powershell
    $myTenantGroupName = "<my-tenant-group-name>"
    $myTenantName = "<my-tenant-name>"
    ```

3. Вам нужно выполнить все инструкции в этой статье в рамках одного сеанса PowerShell. Если вы закроете оно и продолжите позже, операция, возможно, не будет выполнена.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Создание субъекта-службы в Azure Active Directory

Когда вы выполните все предварительные требования в сеансе PowerShell, запустите указанные ниже командлеты PowerShell, чтобы создать мультитенатный субъект-службу в Azure.

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
Set-RdsContext -TenantGroupName $myTenantGroupName
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantGroupName $myTenantGroupName -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Вход с помощью субъекта-службы

Когда вы создадите назначение роли для субъекта-службы, удостоверьтесь, что субъект-служба может войти в Виртуальный рабочий стол Windows, запустив следующий командлет:

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

Из этого руководства вы узнали, как создать субъект-службу и использовать его для входа в Виртуальный рабочий стол Windows. Чтобы узнать больше о том, как можно выполнять вход в Виртуальный рабочий стол Windows, перейдите к руководствам по подключению к Виртуальному рабочему столу Windows.

- [Connect to the Remote Desktop client on Windows 7 and Windows 10](connect-windows-7-and-10.md) (Подключение к клиенту удаленного рабочего стола в Windows 7 и Windows 10)
- [Connect to the Windows Virtual Desktop Preview web client](connect-web.md) (Подключение к веб-клиенту предварительной версии Виртуального рабочего стола Windows)
