---
title: Создание пула размещения консолей для арендаторов Windows Virtual Desktop - Azure
description: Как устранить неполадки и решить проблемы с арендаторами и пулом во время настройки среды для арендаторов Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 36b15b41279edc60d337a7ba70abe2ca64d4bc7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371602"
---
# <a name="tenant-and-host-pool-creation"></a>Создание пула узлов и клиента

В этой статье рассматриваются проблемы во время первоначальной настройки арендатора Windows Virtual Desktop и соответствующей инфраструктуры пула пула сеансов.

## <a name="provide-feedback"></a>Отзывы

Посетите [техническое сообщество Виртуального рабочего стола Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), чтобы обсудить службу "Виртуальный рабочий стол Windows" с группой разработчиков и активными членами сообщества.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Приобретение многосессионного изображения Windows 10 Enterprise

Чтобы использовать многосессионное изображение Windows 10 Enterprise, перейдите на Azure Marketplace, выберите **Get Started** > **Microsoft Windows 10** > и [Windows 10 Предприятие для виртуальных настольных компьютеров, версия 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Скриншот выбора Windows 10 Предприятие для виртуальных настольных компьютеров, версия 1809.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Создание наемного устройства виртуального рабочего стола Windows

В этом разделе рассматриваются потенциальные проблемы при создании наемного компьютера Windows Virtual Desktop.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Ошибка: Пользователь не имеет права на запрос службы управления

![Скриншот окна PowerShell, в котором пользователь не уполномочен задавать запрос службы управления.](media/UserNotAuthorizedNewTenant.png)

Пример необработанной ошибки:

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

**Причина:** Пользователю, вслушавшемуся в договор, не была назначена роль TenantCreator в их active-каталоге Azure.

**Исправление:** Следуйте инструкциям в [присваивать роль приложения TenantCreator пользователю в вашем арендаторе Azure Active Directory.](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role) После выполнения инструкций, вы будете иметь пользователя, назначенного на роль TenantCreator.

![Скриншот назначенной роли TenantCreator.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Создание виртуальных настольных компьютеров Windows

Виртуальные виртуальные выпуски сессий могут создаваться несколькими способами, но команда Windows Virtual Desktop поддерживает только вопросы подготовки VM, связанные с предложением [Azure Marketplace.](https://azuremarketplace.microsoft.com/) Для получения дополнительной информации [см. Проблемы с помощью Windows Virtual Desktop - Предоставление пула хостинга Azure Marketplace.](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering)

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Проблемы с использованием Виртуального рабочего стола Windows - Предоставление пула хостинга Azure Marketplace, предлагая

Виртуальный рабочий стол Windows - Предоставление шаблона пула хоста доступен на Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Ошибка: При использовании ссылки с GitHub появляется сообщение "Создать бесплатную учетную запись"

![Скриншот для создания бесплатной учетной записи.](media/be615904ace9832754f0669de28abd94.png)

**Причина 1:** В учетной записи, используемой для ввоза в Azure, нет активных подписок, или используется учетная запись, не имеет разрешений на просмотр подписок.

**Исправление 1:** Войдя в систему с учетной записью, которая имеет доступ к подписке, где будут развернуты VMs-мих-излза.

**Причина 2:** Используемая подписка является частью арендатора поставщика облачных услуг Майкрософт (CSP).

**Исправление 2:** Перейдите к местоположению GitHub для **создания и предоставления нового пула windows Virtual Desktop** и следуйте следующим инструкциям:

1. Нажмите на **кнопку «Развертывание в Azure»** и выберите адрес **ссылки Copy.**
2. Откройте **блокнот** и вставьте ссылку.
3. Перед тем, как символ q, вставьте имя клиента-клиента CSP.
4. Откройте новую ссылку в браузере, и портал Azure загрузит шаблон.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Ошибка: Вы получаете ошибку "развертывание шаблона не является действительным"

![Скриншот "шаблон развертывания ... недействительна" ошибка](media/troubleshooting-marketplace-validation-error-generic.png)

Прежде чем предпринимать конкретные действия, необходимо проверить журнал действий, чтобы увидеть подробную ошибку для неудавшейся проверки развертывания.

Для просмотра ошибки в журнале действий:

1. Выйдите из текущего предложения развертывания Azure Marketplace.
2. В верхней панели поиска ищите и выберите **журнал активности.**
3. Найдите действие под названием **Validate Deployment,** которое имеет статус **Неудавшегося,** и выберите действие.
   ![Скриншот индивидуальной деятельности «Проверка развертывания» со статусом «Неудавшийся»](media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Выберите JSON, затем прокрутите вниз к нижней части экрана, пока не увидите поле "statusMessage".
   ![Скриншот неудачной деятельности с красным ящиком вокруг свойства statusMessage текста JSON.](media/troubleshooting-marketplace-validation-error-json-boxed.png)

Если шаблон операции переходит к лимиту квоты, вы можете сделать одну из следующих вещей, чтобы исправить это:

 - Выполнить Azure Marketplace с параметрами, которые вы использовали в первый раз, но на этот раз использовать меньше VMs и VM ядер.
 - Откройте ссылку, которая просматривается в поле **statusMessage,** в браузере для отправки запроса на увеличение квоты для подписки Azure для указанного VM SKU.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Шаблон управления ресурсами Azure и ошибки конфигурации состояния PowerShell (DSC)

Следуйте этим инструкциям, чтобы устранить неудачные развертывания шаблонов Azure Resource Manager и PowerShell DSC.

1. Просмотрите ошибки в развертывании с помощью [операций развертывания View с помощью менеджера ресурсов Azure.](../azure-resource-manager/resource-manager-deployment-operations.md)
2. Если в развертывании нет ошибок, проверь ошибки в журнале действий с помощью [журналов активности View для проверки действий на ресурсах.](../azure-resource-manager/resource-group-audit.md)
3. Как только ошибка выявлена, используйте сообщение об ошибке и ресурсы в [общих ошибках развертывания Azure С менеджером ресурсов Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md) для устранения проблемы.
4. Удалите все ресурсы, созданные во время предыдущего развертывания, и повторите попытку развертывания шаблона снова.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Ошибка: Ваше развертывание\<не удалось .... hostname>/joindomain

![Скриншот развертывания провалился.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Пример необработанной ошибки:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Причина 1:** Учетные данные, предусмотренные для присоединения ВМ к домену, неверны.

**Исправление 1:** Просм. ошибку "Неправильные учетные данные" для VMs не соединены с доменом в [конфигурации Хостохозяина Сеанса.](troubleshoot-vm-configuration.md)

**Причина 2:** Доменное имя не решается.

**Исправление 2:** Увидеть [Ошибка: Доменное имя не разрешается](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) в [конфигурации VM-хозяек сеанса.](troubleshoot-vm-configuration.md)

**Причина 3:** Конфигурация виртуальной сети (VNET) DNS **настроена на default.**

Чтобы исправить это, сделайте следующие вещи:

1. Откройте портал Azure и перейдите на вкладку **Виртуальные сети.**
2. Найдите VNET, а затем выберите **DNS-серверы.**
3. Меню DNS-серверов должно отображаться в правой части экрана. В этом меню выберите **Custom**.
4. Убедитесь, что DNS-серверы, перечисленные в пользовательских, соответствуют контроллеру домена или домену Active Directory. Если вы не видите свой DNS-сервер, вы можете добавить его, введя его значение в поле **сервера Add DNS.**

### <a name="error-your-deployment-failedunauthorized"></a>Ошибка: Развертывание не удалось...

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Причина:** Используемая подписка — это тип, который не может получить доступ к требуемым функциям в регионе, где клиент пытается развернуть. Например, подписки MSDN, Free или Education могут отображать эту ошибку.

**Исправление:** Измените тип подписки или область на область, которая может получить доступ к требуемым функциям.

### <a name="error-vmextensionprovisioningerror"></a>Ошибка: VMExtensionОбеспечениеОшибка

![Скриншот вашего развертывания не удалось с состоянием подготовки терминала не удалось.](media/7aaf15615309c18a984673be73ac969a.png)

**Причина 1:** Переходная ошибка с средой Windows Virtual Desktop.

**Причина 2:** Переходная ошибка с подключением.

**Исправление:** Подтвердите Windows Virtual Desktop среду является здоровым, воспроизводном с помощью PowerShell. Завершите регистрацию VM вручную в [создании пула хоста с PowerShell.](create-host-pools-powershell.md)

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Ошибка: Указанное имя пользователя Admin не допускается

![Скриншот развертывания не удался, в котором указан админ, не допускается.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Пример необработанной ошибки:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Причина:** Предоставленный пароль содержит запрещенные подстроки (администратор, администратор, корень).

**Исправление:** Обновление имени пользователя или использование различных пользователей.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Ошибка: VM сообщил о сбое при расширении обработки

![Скриншот операции ресурса, завершенной с состоянием подготовки терминала в развертывании.](media/49c4a1836a55d91cd65125cf227f411f.png)

Пример необработанной ошибки:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
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

**Причина:** Расширение PowerShell DSC не смогло получить доступ к админ-аминтам на VM.

**Исправление:** Подтверждение имени пользователя и пароля имеют административный доступ к виртуальной машине и снова запустите шаблон Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Ошибка: РазвертываниеFailed - PowerShell DSC Конфигурация 'FirstSessionHost' завершена с ошибкой (ы)

![Скриншот развертывания неудачу с PowerShell DSC Конфигурация 'FirstSessionHost' завершена с ошибкой (ы).](media/64870370bcbe1286906f34cf0a8646ab.png)

Пример необработанной ошибки:

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

**Причина:** Расширение PowerShell DSC не смогло получить доступ к админ-аминтам на VM.

**Исправление:** Подтверждение имени пользователя и при условии пароля имеют административный доступ к виртуальной машине и снова запустите шаблон Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Ошибка: РазвертываниеНеудалось - НедействительноеСсылка Ресурсов

Пример необработанной ошибки:

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

**Причина:** Часть имени группы ресурсов используется для определенных ресурсов, создаваемых шаблоном. Из-за того, что имя соответствует существующим ресурсам, шаблон может выбрать существующий ресурс из другой группы.

**Исправление:** При запуске шаблона Azure Resource Manager для развертывания vMs-миноносцев-хост-сессий сделайте первые два символа уникальными для названия группы ресурсов подписки.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Ошибка: РазвертываниеНеудалось - НедействительноеСсылка Ресурсов

Пример необработанной ошибки:

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

**Причина:** Эта ошибка связана с тем, что nic, созданный с помощью шаблона Azure Resource Manager, имеет то же имя, что и другой NIC, уже в VNET.

**Исправление:** Используйте другую префикс хоста.

### <a name="error-deploymentfailed--error-downloading"></a>Ошибка: РазвертываниеНе- Ошибка загрузки

Пример необработанной ошибки:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Причина:** Эта ошибка вызвана статическим маршрутом, правилом брандмауэра или NSG, блокирующим загрузку файла застежки-молнии, привязанного к шаблону менеджера ресурсов Azure.

**Исправление:** Удалите блокирующий статический маршрут, правило брандмауэра или NSG. Дополнительно откройте шаблон шаблона Json менеджера ресурсов Azure в текстовом редакторе, переместите ссылку на почтовый файл и загрузите ресурс в разрешенное местоположение.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Ошибка: Пользователь не имеет права на запрос службы управления

Пример необработанной ошибки:

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

**Причина:** Указанный админ-наемщик Windows Virtual Desktop не имеет действительного назначения ролей.

**Исправление:** Пользователь, создавший windows Virtual Desktop, должен войти в Windows Virtual Desktop PowerShell и назначить пользователю ролевую уступку. Если вы используете параметры шаблона шаблона gitHub Azure Resource Manager, следуйте этим инструкциям, используя команды PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Ошибка: Пользователю требуется многофакторная аутентификация Azure (MFA)

![Скриншот развертывания не удалось из-за отсутствия многофакторной аутентификации (MFA)](media/MFARequiredError.png)

Пример необработанной ошибки:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Причина:** Указанный админ-арендатор Windows Virtual Desktop требует, чтобы Azure Multi-Factor Authentication (MFA) входе.

**Исправление:** Создайте директор службы и присвоите ему роль для вашего клиента Windows Virtual Desktop, выяснив следующие шаги в [учебном центре: Создание основ службы и ролевые задания с PowerShell.](create-service-principal-role-powershell.md) После проверки того, что вы можете войти в Windows Virtual Desktop с помощью основного сервиса, перезапустите предложение Azure Marketplace или шаблон менеджера ресурсов GitHub Azure, в зависимости от того, какой метод вы используете. Следуйте инструкциям ниже, чтобы ввести правильные параметры для вашего метода.

Если вы работаете с предложением Azure Marketplace, предоставьте значения для следующих параметров для правильной аутентификации на виртуальном рабочем столе Windows:

- Windows Виртуальный настольный арендатор RDS Владелец: Основной сервис
- Идентификатор приложения: Идентификация приложения нового директора службы, который вы создали
- Пароль/Подтверждение Пароля: Секрет пароля, который вы сгенерировали для директора службы
- Идентификатор арендатора Azure AD: Идентификатор арендатора Azure AD основного сервиса, который вы создали

Если вы работаете с шаблоном GitHub Azure Resource Manager, предоставьте значения для следующих параметров для правильной аутентификации windows Virtual Desktop:

- Основное имя пользователя Tenant Admin (UPN) или идентификатор приложения: идентификация приложения нового директора службы, который вы создали
- Арендатор Админ Пароль: Секрет пароля вы создали для основного обслуживания
- IsServicePrincipal: **правда**
- AadTenantId: Идентификатор арендатора Azure AD директора службы, который вы создали

## <a name="next-steps"></a>Дальнейшие действия

- Для обзора устранения неполадок Windows Virtual Desktop и треков эскалации [см.](troubleshoot-set-up-overview.md)
- Для устранения неполадок при настройке виртуальной машины (VM) в Windows Virtual Desktop [см.](troubleshoot-vm-configuration.md)
- Чтобы устранить неполадки с подключением клиентов Windows Virtual Desktop, [см.](troubleshoot-service-connection.md)
- Для устранения неполадок с клиентами Remote Desktop просмейте [о устранении проблем с клиентом Remote Desktop](troubleshoot-client.md)
- Для устранения неполадок при использовании [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)PowerShell с Windows Virtual Desktop см.
- Чтобы узнать больше об услуге, [см.](environment-setup.md)
- Чтобы пройти через учебник по устранению неполадок, [см.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
- Сведения о действиях аудита см. в статье [Операции аудита с помощью Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Дополнительные сведения об определении ошибок во время развертывания см. в статье [Просмотр операций развертывания с помощью портала Azure](../azure-resource-manager/templates/deployment-history.md).
