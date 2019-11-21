---
title: Устранение неполадок c общими ресурсами службы автоматизации Azure
description: Узнайте, как устранять неполадки c общими ресурсами службы автоматизации Azure
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: a2836f40b55a71e080288fce7e48275747962c16
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231535"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Устранение неполадок c общими ресурсами

В этой статье рассматриваются способы устранения неполадок, которые могут возникнуть при использовании общих ресурсов службы автоматизации Azure.

## <a name="modules"></a>Модули

### <a name="module-stuck-importing"></a>Сценарий: задержка при импорте модуля

#### <a name="issue"></a>Проблема

При импорте или обновлении модуля в службе автоматизации Azure обнаружилось, что модуль застопорился в состоянии **Импорт**.

#### <a name="cause"></a>Причина:

Импорт модулей PowerShell — это сложный многоэтапный процесс. При этом существует вероятность, что модуль не будет импортирован правильно. В таком случае импортируемый модуль может застопориться в переходном состоянии. Дополнительные сведения об этом процессе см. в статье [Importing a PowerShell Module](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process) (Импорт модуля PowerShell).

#### <a name="resolution"></a>Разрешение

Чтобы устранить эту проблему, необходимо удалить модуль, застопорившийся в состоянии **Импорт**, с помощью командлета [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule). Затем можно повторить импорт модуля.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>Scenario: AzureRM modules are stuck importing after trying to update them

#### <a name="issue"></a>Проблема

A banner with the following message stays in your account after trying to update your AzureRM modules:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Причина:

There is a known issue with updating the AzureRM modules in an Automation Account that is in a resource group with a numeric name that starts with 0.

#### <a name="resolution"></a>Разрешение

To update your Azure modules in your Automation Account, it must be in a resource group that has an alphanumeric name. Resource groups with numeric names starting with 0 are unable to update AzureRM modules at this time.

### <a name="module-fails-to-import"></a>Сценарий: не удается импортировать модуль или после импорта не выполняются командлеты

#### <a name="issue"></a>Проблема

Модулю не удается осуществить импорт, либо импорт выполнен успешно, но командлеты не извлекаются.

#### <a name="cause"></a>Причина:

Ниже приведены наиболее распространенные причины, из-за которых модуль не может успешно завершить импорт в службу автоматизации Azure.

* Структура не соответствует той, которая требуется для службы автоматизации.
* Модуль зависит от другого модуля, который не был развернут в учетной записи службы автоматизации.
* В папке отсутствуют зависимости модуля.
* Командлет `New-AzureRmAutomationModule` используется для передачи модуля, при этом полный путь для хранения не указан или модуль не загружен с помощью общедоступного URL-адреса.

#### <a name="resolution"></a>Разрешение

Эту проблему можно устранить одним из следующих способов.

* Убедитесь, что модуль имеет следующий формат: имя_модуля.Zip **->** имя_модуля или номер_версии **->** (ModuleName.psm1, ModuleName.psd1).
* Откройте файл PSD1 и проверьте, есть ли у модуля зависимости. Если зависимости есть, отправьте эти модули в учетную запись службы автоматизации.
* Убедитесь, что все указанные библиотеки DLL находятся в папке модуля.

### <a name="all-modules-suspended"></a>Scenario: Update-AzureModule.ps1 suspends when updating modules

#### <a name="issue"></a>Проблема

Процесс обновления приостанавливается при использовании runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) для обновления модулей Azure.

#### <a name="cause"></a>Причина:

Значение по умолчанию для определения количества одновременно обновляемых модулей — 10 при использовании сценария `Update-AzureModule.ps1`. Процесс обновления подвержен ошибкам, если одновременно обновляются слишком много модулей.

#### <a name="resolution"></a>Разрешение

Очень редко все модули AzureRM требуются в одной учетной записи службы автоматизации. Мы рекомендуем импортировать только необходимые модули AzureRM.

> [!NOTE]
> Следует избегать импортирования модуля **AzureRM**. При импорте модулей **AzureRM** импортируются все модули **AzureRM.\*** . Это не рекомендуется.

Если процесс обновления приостанавливается, необходимо добавить параметр `SimultaneousModuleImportJobCount` в сценарий `Update-AzureModules.ps1` и указать более низкое значение, чем значение по умолчанию, равное 10. Это рекомендуется, если вы реализуете эту логику, начиная со значения 3 или 5. `SimultaneousModuleImportJobCount` является параметром runbook системы `Update-AutomationAzureModulesForAccount`, который используется для обновления модулей Azure. Благодаря этому изменению процесс будет более продолжительным, но он будет имеет больше шансов на завершение. В следующем примере показан параметр и его место в runbook:

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

### <a name="unable-create-update"></a>Scenario: You're unable to create or update a Run As account

#### <a name="issue"></a>Проблема

При попытке создать или обновить учетную запись запуска от имени, появляется сообщение об ошибке, подобное приведенному ниже.

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Причина:

У вас нет разрешений, необходимых для создания или обновления учетной записи запуска от имени, или ресурс заблокирован на уровне группы ресурсов.

#### <a name="resolution"></a>Разрешение

Чтобы создать или обновить учетную запись запуска от имени, необходимо иметь соответствующие разрешения на различные ресурсы, используемые для запуска. Дополнительные сведения о разрешениях, необходимых для создания или обновления учетной записи запуска от имени, см. в разделе [Разрешения для запуска от имени учетной записи](../manage-runas-account.md#permissions).

Если проблема связана с блокировкой, убедитесь, что ее можно удалить. Затем перейдите к ресурсу, который заблокирован, щелкните блокировку правой кнопкой мыши и выберите **Удалить**, чтобы снять блокировку.

### <a name="iphelper"></a>Scenario: You receive the error "Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'" when executing a runbook.

#### <a name="issue"></a>Проблема

When executing a runbook you receive the following exception:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Причина:

This error is most likely caused by an incorrectly configured [Run As Account](../manage-runas-account.md).

#### <a name="resolution"></a>Разрешение

Make sure your [Run As Account](../manage-runas-account.md) is properly configured. Once it is configured correctly, ensure you have the proper code in your runbook to authenticate with Azure. The following example shows a snippet of code to authenticate to Azure in a runbook using a Run As Account.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
