---
title: Создание пула узлов среды виртуальных рабочих столов Windows — Azure
description: Устранение неполадок и разрешение проблем с пулом клиентов и узлов во время установки среды виртуальных рабочих столов Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 65a36e21d346e08ebe09f8c4b34f6af529d2a0f0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292566"
---
# <a name="host-pool-creation"></a>Создание пула узлов

>[!IMPORTANT]
>Это содержимое применяется к виртуальному рабочему столу Windows с Azure Resource Manager объектами виртуальных рабочих столов Windows. Если вы используете Windows Virtual Desktop (классическая модель) без Azure Resource Manager объектов, см. [эту статью](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md).

В этой статье рассматриваются проблемы во время первоначальной настройки клиента виртуальных рабочих столов Windows и соответствующей инфраструктуры пула узлов сеансов.

## <a name="provide-feedback"></a>Отзывы

Посетите [техническое сообщество Виртуального рабочего стола Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), чтобы обсудить службу "Виртуальный рабочий стол Windows" с группой разработчиков и активными членами сообщества.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Получение многосеансового образа Windows 10 Корпоративная

Чтобы использовать многосеансовый образ Windows 10 Enterprise, перейдите в Azure Marketplace, выберите начало **работы**с  >  **Microsoft Windows 10** > и [Windows 10 Корпоративная многосеансовая версия 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Проблемы с использованием портал Azure для создания пулов узлов

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>Ошибка: при доступе к службе отображается сообщение "создание бесплатной учетной записи"

> [!div class="mx-imgBorder"]
> ![Изображение, показывающее, на портал Azure отображается сообщение "создание бесплатной учетной записи"](media/create-new-account.png)

**Причина**: в учетной записи, в которой вы вошли в Azure, нет активных подписок, или у учетной записи нет разрешений на просмотр подписок. 

**Исправление**. Войдите в подписку, где вы развернете виртуальные машины узла сеансов с учетной записью, которая имеет по крайней мере доступ на уровне участника.

### <a name="error-exceeding-quota-limit"></a>Ошибка: "превышен предел квоты"

Если операция превышает предельную квоту, можно выполнить одно из следующих действий. 

- Создайте новый пул узлов с теми же параметрами, но с меньшим числом виртуальных машин и ядер ВМ.

- Откройте ссылку, которая отображается в поле statusMessage в браузере, чтобы отправить запрос на увеличение квоты для подписки Azure для указанного номера SKU виртуальной машины.

## <a name="azure-resource-manager-template-errors"></a>Ошибки шаблона Azure Resource Manager

Выполните эти инструкции, чтобы устранить неудачные развертывания шаблонов Azure Resource Manager и PowerShell DSC.

1. Просмотрите ошибки в развертывании, используя [Просмотр операций развертывания с Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-operations.md).
2. Если в развертывании нет ошибок, просмотрите ошибки в журнале действий, используя [Просмотр журналов действий для аудита действий с ресурсами](../azure-resource-manager/resource-group-audit.md).
3. После определения ошибки используйте сообщение об ошибке и ресурсы в статье [Устранение распространенных ошибок развертывания Azure с Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md) для решения проблемы.
4. Удалите все ресурсы, созданные во время предыдущего развертывания, и повторите попытку развертывания шаблона.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Ошибка: сбой развертывания... \<hostname> /жоиндомаин

> [!div class="mx-imgBorder"]
> ![Снимок экрана сбоя развертывания.](media/failure-joindomain.png)

Пример необработанной ошибки:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Причина 1:** Указаны неправильные учетные данные для присоединения виртуальных машин к домену.

**Исправление 1:** См. ошибку "неверные учетные данные" для виртуальных машин не присоединены к домену в [конфигурации виртуальной машины узла сеансов](troubleshoot-vm-configuration.md).

**Причина 2:** Доменное имя не разрешается.

**Исправление 2:** См. [ошибку: доменное имя не разрешается](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) в [конфигурации виртуальной машины узла сеансов](troubleshoot-vm-configuration.md).

**Причина 3:** Для конфигурации DNS виртуальной сети задано значение **по умолчанию**.

Чтобы устранить эту проблему, выполните следующие действия.

1. Откройте портал Azure и перейдите на вкладку **виртуальные сети** .
2. Найдите виртуальную сеть, а затем выберите **DNS-серверы**.
3. Меню DNS-серверы должно отображаться в правой части экрана. В этом меню выберите **Пользовательский**.
4. Убедитесь, что DNS-серверы, указанные в поле Пользовательский, соответствуют вашему контроллеру домена или домену Active Directory. Если DNS-сервер не отображается, его можно добавить, введя его значение в поле **добавить DNS-сервер** .

### <a name="error-your-deployment-failedunauthorized"></a>Ошибка: сбой развертывания. ..\Унаусоризед

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Причина:** Используемая Подписка — это тип, который не может получить доступ к необходимым функциям в регионе, в котором клиент пытается выполнить развертывание. Например, подписки MSDN, Free или образовательных учреждений могут показывать эту ошибку.

**Исправление:** Измените тип подписки или регион на другой, который может получить доступ к необходимым функциям.

### <a name="error-vmextensionprovisioningerror"></a>Ошибка: Вмекстенсионпровисионинжеррор

> [!div class="mx-imgBorder"]
> ![Снимок экрана развертывания: не удалось выполнить состояние подготовки терминала.](media/failure-vmextensionprovisioning.png)

**Причина 1:** Временная ошибка в среде виртуальных рабочих столов Windows.

**Причина 2:** Временная ошибка с подключением.

**Исправление:** Подтвердите работоспособность среды виртуальных рабочих столов Windows, выполнив вход с помощью PowerShell. Завершите регистрацию виртуальной машины вручную в окне [Создание пула узлов с помощью PowerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Ошибка: указанное имя администратора не разрешено

> [!div class="mx-imgBorder"]
> ![Снимок экрана развертывания, в котором указанное административное сообщение не разрешено.](media/failure-username.png)

Пример необработанной ошибки:

```Error
 { …{ "provisioningOperation": 
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId": 
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message": 
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**Причина:** Предоставленный пароль содержит запрещенные подстроки (администратор, администратор, корень).

**Исправление:** Обновите имя пользователя или используйте других пользователей.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Ошибка: виртуальная машина сообщила об ошибке при обработке расширения

> [!div class="mx-imgBorder"]
> ![Снимок экрана: операция с ресурсом завершена с состоянием подготовки терминала в развертывании.](media/failure-processing.png)

Пример необработанной ошибки:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code": 
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**Причина:** Модулю PowerShell DSC не удалось получить доступ к администратору на виртуальной машине.

**Исправление:** Убедитесь, что имя пользователя и пароль имеют административный доступ к виртуальной машине, и снова запустите шаблон Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Ошибка: DeploymentFailed — конфигурация PowerShell DSC "Фирстсессионхост" завершена с ошибками

> [!div class="mx-imgBorder"]
> ![Снимок экрана развертывания с конфигурацией PowerShell DSC "Фирстсессионхост" завершился с ошибками.](media/failure-dsc.png)

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

**Причина:** Модулю PowerShell DSC не удалось получить доступ к администратору на виртуальной машине.

**Исправление:** Убедитесь, что указанные имя пользователя и пароль имеют административный доступ к виртуальной машине, и снова запустите шаблон Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Ошибка: DeploymentFailed – Инвалидресаурцереференце

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

**Причина:** Часть имени группы ресурсов используется для определенных ресурсов, создаваемых шаблоном. Из-за имени, соответствующего существующим ресурсам, шаблон может выбрать существующий ресурс из другой группы.

**Исправление:** При запуске шаблона Azure Resource Manager для развертывания виртуальных машин узла сеансов сделайте первые два символа уникальными для имени группы ресурсов подписки.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Ошибка: DeploymentFailed – Инвалидресаурцереференце

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

**Причина:** Эта ошибка вызвана тем, что сетевая карта, созданная с помощью шаблона Azure Resource Manager, имеет то же имя, что и другая сетевая карта, уже подключенная к ВИРТУАЛЬНОЙ

**Исправление:** Используйте другой префикс узла.

### <a name="error-deploymentfailed--error-downloading"></a>Ошибка: DeploymentFailed — ошибка при скачивании

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

**Причина:** Эта ошибка возникает из-за статического маршрута, правила брандмауэра или NSG блокировки загрузки ZIP-файла, привязанного к шаблону Azure Resource Manager.

**Исправление:** Удалите заблокированный статический маршрут, правило брандмауэра или NSG. При необходимости откройте JSON-файл шаблона Azure Resource Manager в текстовом редакторе, перейдите по ссылке к ZIP-файлу и скачайте ресурс в разрешенное расположение.

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об устранении неполадок с Виртуальным рабочим столом Windows и о путях эскалации см. в статье [Общие сведения об устранении неисправностей, отзывы и поддержка](troubleshoot-set-up-overview.md).
- Инструкции по устранению неполадок при настройке виртуальной машины через Виртуальный рабочий стол Windows см. в статье [Конфигурация виртуальной машины узла сеанса](troubleshoot-vm-configuration.md).
- Сведения об устранении неполадок подключения клиентов к виртуальным рабочим столам Windows см. в статье [подключения к службе виртуальных рабочих столов Windows](troubleshoot-service-connection.md).
- Сведения об устранении неполадок, связанных с удаленный рабочий стол клиентами, см. [в разделе Устранение неполадок клиента удаленный рабочий стол](troubleshoot-client.md)
- Инструкции по устранению неполадок при использовании PowerShell через Виртуальный рабочий стол Windows см. в статье [Виртуальный рабочий стол Windows — PowerShell](troubleshoot-powershell.md).
- Дополнительные сведения о службе см. в разделе [Среда виртуальных рабочих столов Windows](environment-setup.md).
- Сведения об устранении неполадок см. в статье [Tutorial: Troubleshoot Resource Manager template deployments](../azure-resource-manager/templates/template-tutorial-troubleshoot.md) (Руководство. Устранение неполадок развертывания шаблонов Resource Manager)
- Сведения о действиях аудита см. в статье [Операции аудита с помощью Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Дополнительные сведения об определении ошибок во время развертывания см. в статье [Просмотр операций развертывания с помощью портала Azure](../azure-resource-manager/templates/deployment-history.md).
