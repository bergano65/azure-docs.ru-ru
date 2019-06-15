---
title: Виртуальный рабочий стол Windows клиента и узла создания пула - Azure
description: Для выявления и устранения клиента и узла пула проблем во время установки в среде виртуального рабочего стола Windows клиента.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 88e843c410a750387ecf58497dec79586e2a59d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523330"
---
# <a name="tenant-and-host-pool-creation"></a>Создание пула узлов и клиента

В этой статье рассматриваются проблемы во время начальной настройки клиента виртуального рабочего стола Windows и инфраструктуры связанных сеансов узлов пула.

## <a name="provide-feedback"></a>Отзывы

В период предоставления предварительной версии Виртуального рабочего стола Windows мы не принимаем запросы в службу поддержки. Посетите [техническое сообщество Виртуального рабочего стола Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), чтобы обсудить службу "Виртуальный рабочий стол Windows" с группой разработчиков и активными членами сообщества.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Получение изображения нескольких сеансов Windows 10 Корпоративная

Чтобы использовать изображение нескольких сеансов Windows 10 Корпоративная, перейдите в Azure Marketplace, выберите **приступить к работе** > **Microsoft Windows 10** > и [Windows 10 Корпоративная для Предварительная версия виртуальных рабочих столов, версия 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Снимок экрана выбора виртуальные рабочие столы в предварительной версии 1809 версии Windows 10 Корпоративная.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Создание клиента виртуального рабочего стола Windows

В этом разделе рассматриваются возможные проблемы при создании клиента виртуального рабочего стола Windows.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Ошибка: Пользователь не авторизован для запроса к службе управления

![Снимок экрана PowerShell окно, в котором пользователь не имеет разрешения на запросы к службе управления.](media/UserNotAuthorizedNewTenant.png)

Пример необработанная ошибка:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Причина.** Пользователь, который выполнил вход не была назначена роль TenantCreator в Azure Active Directory.

**Исправление:** Следуйте инструкциям в [назначение роли приложения TenantCreator пользователю в клиенте Azure Active Directory](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant). После выполнения инструкций, вы получите TenantCreator ролью пользователя.

![Снимок экрана из TenantCreator ролью.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Создание сеанса виртуальный рабочий стол Windows размещения виртуальных машин

Узел сеансов виртуальных машин можно создать несколькими способами, однако команды удаленного рабочего стола служб/Windows виртуальному рабочему столу поддерживают только проблемы, связанные с шаблоном Azure Resource Manager для подготовки виртуальной Машины. Шаблон Azure Resource Manager доступен в [Azure Marketplace](https://azuremarketplace.microsoft.com/) и [GitHub](https://github.com/).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Проблемы с помощью Windows виртуальный рабочий стол — подготовка предложения Azure Marketplace пула узла

Рабочий стол Windows виртуальной — Подготовка шаблон пула узла доступен из Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Ошибка: Если вы используете ссылку из GitHub, сообщение «создать бесплатную учетную запись» появится

![Снимок экрана, чтобы создать бесплатную учетную запись.](media/be615904ace9832754f0669de28abd94.png)

**Причина 1**. Нет активных подписок на учетную запись для входа в Azure или учетная запись, используемая не имеет разрешений на просмотр подписок.

**Исправление 1:** Войдите, используя учетную запись, имеет доступ с правами участника (как минимум) к подписке, где будет развертываться узла сеансов виртуальных машин.

**Причина 2.** Используемая подписка является частью клиента поставщика облачных служб Майкрософт (CSP).

**Исправление 2:** Перейдите в расположение GitHub для **Создание и Подготовка нового пул узлов виртуального рабочего стола Windows** и сделайте следующее:

1. Щелкните правой кнопкой мыши **развертывание в Azure** и выберите **Копировать ссылку адрес**.
2. Откройте **Блокнот** и вставьте ссылку.
3. Перед символом "#" Вставьте имя CSP клиента окончания клиента.
4. Откройте новую ссылку в браузере и на портале Azure будет загрузить шаблон.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Шаблон Azure Resource Manager и PowerShell Desired State Configuration (DSC) ошибок

Следуйте этим инструкциям для устранения неудачных развертываний шаблонов Azure Resource Manager и PowerShell DSC.

1. Проверьте наличие ошибок в развертывания с помощью [Просмотр операций развертывания с помощью Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
2. При возникновении ошибки в развертывании, проверьте наличие ошибок в журнал действие с использованием [Просмотр журналов действий для аудита действий с ресурсами](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
3. После определения ошибки использовать сообщение об ошибке и ресурсы в [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) для решения данной проблемы.
4. Удалите все ресурсы, созданные в ходе предыдущего развертывания и повторно развертывать шаблон повторных попыток.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Ошибка: Произошел сбой развертывания,...<hostname>/joindomain

![Снимок экрана не удалось выполнить развертывание.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Пример необработанная ошибка:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Причина 1**. Указаны неверные учетные данные для присоединения к домену виртуальные машины.

**Исправление 1:** Отображается ошибка «Неверные учетные данные», для виртуальных машин не присоединены к домену в [конфигурацию виртуальной Машины узла сеансов](troubleshoot-vm-configuration.md).

**Причина 2.** Доменное имя не разрешается.

**Исправление 2:** Отображается ошибка «не разрешается доменное имя», для виртуальных машин не присоединены к домену в [конфигурацию виртуальной Машины узла сеансов](troubleshoot-vm-configuration.md).

### <a name="error-vmextensionprovisioningerror"></a>Ошибка: VMExtensionProvisioningError

![Снимок экрана из Your не удалось выполнить развертывание с окончательным состоянием подготовки не удалось.](media/7aaf15615309c18a984673be73ac969a.png)

**Причина 1**. Временная ошибка в среде виртуального рабочего стола Windows.

**Причина 2.** Временная проблема с подключением.

**Исправление:** Убедитесь, что в среде виртуального рабочего стола Windows работает нормально, необходимо выполнить вход с помощью PowerShell. Завершите регистрацию виртуальных Машин вручную в [создать пул узлов с помощью PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Ошибка: Указанное имя пользователя администратора не разрешено

![Снимок экрана развертывания, сбой в которой не допускается администратора, указанного.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Пример необработанная ошибка:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Причина.** Пароль содержит запрещенное подстроки (администратора, администратора, корневой).

**Исправление:** Обновление имени пользователя или использовать разные пользователи.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Ошибка: ВМ сообщила о сбое при обработке расширения

![Снимок экрана: операция ресурса завершилась с окончательным состоянием подготовки в Your не удалось выполнить развертывание.](media/49c4a1836a55d91cd65125cf227f411f.png)

Пример необработанная ошибка:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Причина.** Расширение PowerShell DSC не удалось получить административный доступ на виртуальной Машине.

**Исправление:** Подтвердите имя пользователя и пароль права администратора на виртуальной машине и снова запустите шаблон Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Ошибка: DeploymentFailed — конфигурацию DSC PowerShell FirstSessionHost завершена с ошибками

![Снимок экрана развертывания завершаться конфигурацию DSC PowerShell FirstSessionHost завершена с ошибками.](media/64870370bcbe1286906f34cf0a8646ab.png)

Пример необработанная ошибка:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Причина.** Расширение PowerShell DSC не удалось получить административный доступ на виртуальной Машине.

**Исправление:** Убедитесь, что имя пользователя и пароль, предоставленные права администратора на виртуальной машине и снова запустите шаблон Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Ошибка: DeploymentFailed – InvalidResourceReference

Пример необработанная ошибка:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Причина.** Для некоторых ресурсов, создаваемой с помощью шаблона используется часть имени группы ресурсов. Из-за именем, соответствующим существующих ресурсов шаблон может выбрать имеющийся ресурс из другой группы.

**Исправление:** При выполнении шаблона Azure Resource Manager для развертывания узла сеансов виртуальных машин, сделать первые два символа уникальным для имени группы ресурсов вашей подписки.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Ошибка: DeploymentFailed – InvalidResourceReference

Пример необработанная ошибка:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Причина.** Эта ошибка — так как сетевой КАРТЫ, созданной с помощью шаблона Azure Resource Manager имеет имя, совпадающее с именем другой сетевой Адаптер уже в виртуальной сети.

**Исправление:** Используйте префикс другой узел.

### <a name="error-deploymentfailed--error-downloading"></a>Ошибка: DeploymentFailed — ошибка при загрузке

Пример необработанная ошибка:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Причина.** Эта ошибка связана статический маршрут, правила брандмауэра или группы безопасности сети, блокировки скачивания ZIP-файл, привязаны к шаблона Azure Resource Manager.

**Исправление:** Удалите блокировки статический маршрут, правило брандмауэра или группе безопасности сети. При необходимости откройте json-файл шаблона Azure Resource Manager в текстовом редакторе, перейти ссылку ZIP-файл и загрузить ресурс недопустима.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Ошибка: Пользователь не авторизован для запроса к службе управления

Пример необработанная ошибка:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Причина.** Указанный виртуальный рабочий стол Windows администратора клиента не имеет допустимое назначение ролей.

**Исправление:** Пользователя, создавшего виртуального рабочего стола Windows клиента необходимо войти в PowerShell виртуального рабочего стола для Windows и назначьте попытка пользователя назначение ролей. Если вы используете параметры шаблона GitHub Azure Resource Manager, выполните следующие действия, с помощью команд PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Ошибка: Пользователю требуется многофакторной идентификации Azure (MFA)

![Снимок экрана развертывания не удалось из-за отсутствия многофакторной проверки подлинности (MFA)](media/MFARequiredError.png)

Пример необработанная ошибка:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Причина.** Указанный виртуальный рабочий стол Windows администратора клиента требует многофакторной проверки подлинности Azure (MFA) для входа.

**Исправление:** Создайте субъект-службу и назначить ему роль для вашего клиента виртуального рабочего стола Windows, выполнив действия, описанные в [руководства: Создание субъектов-служб и назначения ролей с помощью PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell). Убедившись, что вы можете войти в к виртуальному рабочему столу Windows с субъектом-службой, перезапустите предложения Azure Marketplace или шаблон GitHub Azure Resource Manager, в зависимости от того, какой метод вы используете. Следуйте инструкциям ниже, чтобы вводить правильные параметры для метода.

Если у вас предложения Azure Marketplace, укажите значения для следующих параметров для надлежащей проверки подлинности к виртуальному рабочему столу Windows.

- Виртуальный рабочий стол Windows клиента служб удаленных рабочих СТОЛОВ владельца: Субъект-служба
- Идентификатор приложения: Созданный код приложения субъект-служба
- Пароль и подтверждение пароля: Секрет пароля, который был создан для субъекта-службы
- Идентификатор клиента Azure AD: Созданный идентификатор клиента Azure AD субъекта-службы.

Если вы используете шаблон ARM GitHub, укажите значения для следующих параметров для надлежащей проверки подлинности к виртуальному рабочему столу Windows.

- Клиент администратора имя участника-пользователя (UPN) или идентификатор приложения: Созданный код приложения субъект-служба
- Пароль администратора клиента: Секрет пароля, который был создан для субъекта-службы
- IsServicePrincipal: **true**
- AadTenantId: Созданный идентификатор клиента Azure AD субъекта-службы.

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об устранении неполадок виртуального рабочего стола Windows и отслеживает эскалации, см. в разделе [Устранение неполадок, Обзор, отзывы и поддержка](troubleshoot-set-up-overview.md).
- Чтобы устранить неполадки при настройке виртуальной машины (VM) в виртуальный рабочий стол Windows, см. в разделе [конфигурацию виртуальной машины узла сеансов](troubleshoot-vm-configuration.md).
- Чтобы устранить неполадки с помощью клиентских подключений виртуального рабочего стола Windows, см. в разделе [клиента к удаленному рабочему столу](troubleshoot-client-connection.md).
- Чтобы устранить неполадки при использовании PowerShell с помощью виртуального рабочего стола Windows, см. в разделе [виртуального рабочего стола PowerShell Windows](troubleshoot-powershell.md).
- Дополнительные сведения о предварительной версии службы, см. в разделе [Windows Desktop предварительной среде](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Сведения об устранении неполадок см. в статье [Tutorial: Устраняйте неполадки развертывания шаблонов Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Сведения о действиях аудита см. в статье [Операции аудита с помощью Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Дополнительные сведения об определении ошибок во время развертывания см. в статье [Просмотр операций развертывания с помощью портала Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).