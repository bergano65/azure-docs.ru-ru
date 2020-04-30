---
title: Устранение неполадок общих ресурсов в службе автоматизации Azure
description: Узнайте, как устранять неполадки и устранять проблемы с общими ресурсами службы автоматизации Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733572"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Устранение неполадок общих ресурсов в службе автоматизации Azure

В этой статье рассматриваются решения проблем, с которыми можно столкнуться при использовании [общих ресурсов](../automation-intro.md#shared-resources) в службе автоматизации Azure.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

## <a name="modules"></a>Модули

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Сценарий: во время импорта модуль зависает

#### <a name="issue"></a>Проблема

Модуль зависает в состоянии импорта при импорте или обновлении модулей службы автоматизации Azure.

#### <a name="cause"></a>Причина

Поскольку импорт модулей PowerShell является сложным процессом в многошаговом режиме, модуль может импортироваться неправильно и может быть задержан в переходном состоянии. Дополнительные сведения о процессе импорта см. в разделе [Импорт модуля PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Решение

Чтобы устранить эту проблему, необходимо удалить модуль, который находится в состоянии импорта, с помощью командлета [Remove-азаутоматионмодуле](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) . Затем можно повторить импорт модуля.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Сценарий: модули AzureRM задерживаются во время импорта после попытки обновления

#### <a name="issue"></a>Проблема

После попытки обновления модулей AzureRM в вашей учетной записи остается баннер со следующим сообщением:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Причина

Существует известная ошибка при обновлении модулей AzureRM в учетной записи службы автоматизации, которая находится в группе ресурсов с числовым именем, начинающимся с 0.

#### <a name="resolution"></a>Решение

Чтобы обновить модули AzureRM в учетной записи службы автоматизации, учетная запись должна находиться в группе ресурсов с буквенно-цифровым именем. Группы ресурсов с числовыми именами, начинающимися с 0, не могут обновить модули AzureRM в данный момент.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Сценарий: не удается импортировать модуль или после импорта не выполняются командлеты

#### <a name="issue"></a>Проблема

Модуль не может импортироваться, или он успешно импортирован, но командлеты не извлекаются.

#### <a name="cause"></a>Причина

Ниже приведены наиболее распространенные причины, из-за которых модуль не может успешно завершить импорт в службу автоматизации Azure.

* Структура не соответствует структуре, которая необходима для автоматизации.
* Модуль зависит от другого модуля, который не был развернут в учетной записи службы автоматизации.
* В папке отсутствуют зависимости модуля.
* Командлет [New-азаутоматионмодуле](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) используется для отправки модуля, но вы не предоставили полный путь к хранилищу или не загрузили модуль с помощью общедоступного URL-адреса.

#### <a name="resolution"></a>Решение

Используйте любое из этих решений для устранения проблемы.

* Убедитесь, что модуль соответствует формату: ModuleName. zip-> ModuleName или номер версии-> (ModuleName. psm1, ModuleName. PSD1).
* Откройте файл **PSD1** и проверьте, есть ли у модуля зависимости. Если зависимости есть, отправьте эти модули в учетную запись службы автоматизации.
* Убедитесь, что все **DLL** -файлы, на которые имеются ссылки, находятся в папке Module.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Сценарий: Приостановка Упдате-азуремодуле. ps1 при обновлении модулей

#### <a name="issue"></a>Проблема

При использовании модуля Runbook [упдате-азуремодуле. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) для обновления модулей Azure процесс обновления модуля приостанавливается.

#### <a name="cause"></a>Причина

Значение по умолчанию, определяющее, сколько модулей обновляется одновременно, равно 10 при использовании **упдате-азуремодуле. ps1**. Процесс обновления подвержен ошибкам, если одновременно обновляются слишком много модулей.

#### <a name="resolution"></a>Решение

Обычно в одной учетной записи службы автоматизации требуются все модули AzureRM и AZ. Рекомендуется импортировать только необходимые модули.

> [!NOTE]
> Избегайте импорта всего `Az.Automation` модуля или `AzureRM.Automation` , который импортирует все автономные модули.

Если процесс обновления приостанавливается, добавьте `SimultaneousModuleImportJobCount` параметр в скрипт **упдате-азуремодулес. ps1** и укажите меньшее значение по умолчанию, равное 10. При реализации этой логики рекомендуется начинать со значения 3 или 5. `SimultaneousModuleImportJobCount`— Это параметр системного модуля Runbook **Update-аутоматионазуремодулесфораккаунт** , который используется для обновления модулей Azure. При внесении этой корректировки процесс обновления выполняется дольше, но более высока вероятность его завершения. В следующем примере показан параметр и его место в runbook:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Учетная запись запуска от имени

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Сценарий: не удается создать или обновить учетную запись запуска от имени

#### <a name="issue"></a>Проблема

При попытке создать или обновить учетную запись запуска от имени, появляется сообщение об ошибке, подобное приведенному ниже.

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Причина

У вас нет разрешений, необходимых для создания или обновления учетной записи запуска от имени, или ресурс заблокирован на уровне группы ресурсов.

#### <a name="resolution"></a>Решение

Чтобы создать или обновить учетную запись запуска от имени, необходимо иметь соответствующие [разрешения](../manage-runas-account.md#permissions) для различных ресурсов, используемых учетной записью запуска от имени. 

Если причиной проблемы является блокировка, убедитесь, что блокировка может быть удалена. Затем перейдите к ресурсу, который заблокирован в портал Azure, щелкните блокировку правой кнопкой мыши и выберите команду **Удалить**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Сценарий. при выполнении Runbook появляется сообщение об ошибке "не удается найти точку входа с именем" Жетперадаптеринфо "в библиотеке DLL" иплпапи. dll ""

#### <a name="issue"></a>Проблема

При выполнении модуля Runbook появляется следующее исключение:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Причина

Скорее всего, эта ошибка вызвана неправильно настроенной [учетной записью запуска от имени](../manage-runas-account.md).

#### <a name="resolution"></a>Решение

Убедитесь, что учетная запись запуска от имени настроена правильно. Затем убедитесь, что у вас есть правильный код в модуле Runbook для проверки подлинности в Azure. В следующем примере показан фрагмент кода для проверки подлинности в Azure в модуле Runbook с помощью учетной записи запуска от имени.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Дальнейшие шаги

Если вы не видите проблему выше или не можете устранить проблему, воспользуйтесь одним из следующих каналов для получения дополнительной поддержки:

* Получите ответы от экспертов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport)помощью официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **получить поддержку**.
