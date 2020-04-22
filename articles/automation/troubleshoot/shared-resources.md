---
title: Устранение проблем совместно ещё лидужены с ресурсами в Автоматизации Azure
description: Узнайте, как устранить неполадки и устранить проблемы с общими ресурсами Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733572"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Устранение проблем совместно ещё лидужены с ресурсами в Автоматизации Azure

В этой статье рассматриваются решения проблем, с которыми можно столкнуться при использовании [общих ресурсов](../automation-intro.md#shared-resources) в Azure Automation.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](../automation-update-azure-modules.md)

## <a name="modules"></a>Модули

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Сценарий: Модуль застрял во время импорта

#### <a name="issue"></a>Проблемы

Модуль застревает в состоянии импорта при импорте или обновлении модулей Azure Automation.

#### <a name="cause"></a>Причина:

Поскольку импорт модулей PowerShell является сложным многоступенчатым процессом, модуль может не импортироваться правильно и может быть застрять в переходном состоянии. Чтобы узнать больше о процессе импорта, [см.](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)

#### <a name="resolution"></a>Решение

Чтобы решить эту проблему, необходимо удалить модуль, который застрял в состоянии импорта, с помощью cmdlet [Remove-AzAutomationModule.](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) Затем можно повторить импорт модуля.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Сценарий: Модули AzureRM застряли во время импорта после попытки обновления

#### <a name="issue"></a>Проблемы

Баннер со следующим сообщением остается в вашей учетной записи после попытки обновления модулей AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Причина:

Существует известная проблема с обновлением модулей AzureRM в учетной записи Автоматизации, которая находится в группе ресурсов с числовым именем, начинающимся с 0.

#### <a name="resolution"></a>Решение

Чтобы обновить модули AzureRM в учетной записи Автоматизации, учетная запись должна находиться в группе ресурсов с буквенным именем. Группы ресурсов с числовыми именами, начинающимися с 0, в настоящее время не могут обновлять модули AzureRM.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Сценарий: не удается импортировать модуль или после импорта не выполняются командлеты

#### <a name="issue"></a>Проблемы

Модуль не импортируется или успешно импортируется, но не извлекаются смдлеты.

#### <a name="cause"></a>Причина:

Ниже приведены наиболее распространенные причины, из-за которых модуль не может успешно завершить импорт в службу автоматизации Azure.

* Структура не соответствует структуре, в которую нуждается автоматизация.
* Модуль зависит от другого модуля, который не был развернут в учетной записи службы автоматизации.
* В папке отсутствуют зависимости модуля.
* [Cmdlet New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) используется для загрузки модуля, и вы не дали полный путь хранения или не загрузили модуль с помощью общедоступного URL-

#### <a name="resolution"></a>Решение

Используйте любое из этих решений для устранения проблемы.

* Убедитесь, что модуль следует формату: ModuleName.zip -> ModuleName или Номер версии -> (ModuleName.psm1, ModuleName.psd1).
* Откройте файл **.psd1** и просмотрите, есть ли у модуля какие-либо зависимости. Если зависимости есть, отправьте эти модули в учетную запись службы автоматизации.
* Убедитесь, что все ссылки на файлы **.dll** присутствуют в папке модуля.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Сценарий: Обновление-AzureModule.ps1 приостанавливается при обновлении модулей

#### <a name="issue"></a>Проблемы

При использовании runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) для обновления модулей Azure процесс обновления модуля приостанавливается.

#### <a name="cause"></a>Причина:

Настройка по умолчанию для определения количества модулей обновляется одновременно 10 при использовании **Update-AzureModule.ps1.** Процесс обновления подвержен ошибкам, если одновременно обновляются слишком много модулей.

#### <a name="resolution"></a>Решение

Не принято, чтобы все модули AzureRM или Az требовались в одной учетной записи Автоматизации. Рекомендуется импортировать только определенные модули, которые вам нужны.

> [!NOTE]
> Избегайте импорта `Az.Automation` всего `AzureRM.Automation` модуля, который импортирует все содержащиеся модули.

Если процесс обновления приостанавливается, добавьте `SimultaneousModuleImportJobCount` параметр в сценарий **Update-AzureModules.ps1** и предоставьте более низкое значение, чем значение по умолчанию 10. При реализации этой логики рекомендуется начинать со значения 3 или 5. `SimultaneousModuleImportJobCount`является параметром запуска **системы Update-AutomationModulesForAccount,** которая используется для обновления модулей Azure. Если вы сделаете эту корректировку, процесс обновления работает дольше, но имеет больше шансов на завершение. В следующем примере показан параметр и его место в runbook:

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Сценарий: Вы не можете создать или обновить учетную запись Run As

#### <a name="issue"></a>Проблемы

При попытке создать или обновить учетную запись запуска от имени, появляется сообщение об ошибке, подобное приведенному ниже.

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Причина:

У вас нет разрешений, необходимых для создания или обновления учетной записи запуска от имени, или ресурс заблокирован на уровне группы ресурсов.

#### <a name="resolution"></a>Решение

Чтобы создать или обновить учетную запись Run As, необходимо иметь соответствующие разрешения на различные [ресурсы,](../manage-runas-account.md#permissions) используемые учетной записью Run As. 

Если проблема устранена из-за блокировки, убедитесь, что блокировка может быть удалена. Затем перейдите на ресурс, заблокированный на портале Azure, нажмите кнопку блокировки и **нажмите «Удалить».**

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Сценарий: Вы получаете ошибку "Не удается найти точку входа под названием 'GetPerAdapterInfo' в DLL 'iplpapi.dll'" при выполнения runbook

#### <a name="issue"></a>Проблемы

При выполнении runbook вы получаете следующее исключение:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Причина:

Эта ошибка, скорее всего, вызвана неправильно настроенной [учетной записью Run As.](../manage-runas-account.md)

#### <a name="resolution"></a>Решение

Убедитесь, что ваша учетная запись Run As настроена должным образом. Затем убедитесь, что в runbook есть правильный код для проверки подлинности с помощью Azure. В следующем примере показан фрагмент кода для проверки подлинности В журнале Azure с помощью учетной записи Run As.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Следующие шаги

Если вы не видите проблему выше или не можете решить вашу проблему, попробуйте один из следующих каналов для дополнительной поддержки:

* Получите ответы от экспертов Azure через [форумы Azure.](https://azure.microsoft.com/support/forums/)
* Связаться [@AzureSupport](https://twitter.com/azuresupport)с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**
