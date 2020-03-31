---
title: Устранение неполадок c общими ресурсами службы автоматизации Azure
description: Узнайте, как устранить неполадки и устранить проблемы с общими ресурсами Azure Automation, поддерживающими руны.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278328"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Устранение неполадок c общими ресурсами

В этой статье рассматриваются способы устранения неполадок, которые могут возникнуть при использовании общих ресурсов службы автоматизации Azure.

## <a name="modules"></a>Модули

### <a name="scenario-a-module-is-stuck-importing"></a><a name="module-stuck-importing"></a>Сценарий: задержка при импорте модуля

#### <a name="issue"></a>Проблема

При импорте или обновлении модуля в службе автоматизации Azure обнаружилось, что модуль застопорился в состоянии **Импорт**.

#### <a name="cause"></a>Причина

Импорт модулей PowerShell — это сложный многоэтапный процесс. При этом существует вероятность, что модуль не будет импортирован правильно. В таком случае импортируемый модуль может застопориться в переходном состоянии. Дополнительные сведения об этом процессе см. в статье [Importing a PowerShell Module](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process) (Импорт модуля PowerShell).

#### <a name="resolution"></a>Решение

Чтобы устранить эту проблему, необходимо удалить модуль, застопорившийся в состоянии **Импорт**, с помощью командлета [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule). Затем можно повторить импорт модуля.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-importing-after-trying-to-update-them"></a><a name="update-azure-modules-importing"></a>Сценарий: Модули AzureRM застряли в импорте после попытки их обновления

#### <a name="issue"></a>Проблема

Баннер со следующим сообщением остается в вашей учетной записи после попытки обновления модулей AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Причина

Существует известная проблема с обновлением модулей AzureRM в учетной записи автоматизации, которая находится в группе ресурсов с числовым именем, который начинается с 0.

#### <a name="resolution"></a>Решение

Чтобы обновить модули Azure в учетной записи автоматизации, он должен быть в группе ресурсов с буквенно-цифровым именем. Группы ресурсов с числовыми именами, начинающимися с 0, в настоящее время не могут обновлять модули AzureRM.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Сценарий: не удается импортировать модуль или после импорта не выполняются командлеты

#### <a name="issue"></a>Проблема

Модулю не удается осуществить импорт, либо импорт выполнен успешно, но командлеты не извлекаются.

#### <a name="cause"></a>Причина

Ниже приведены наиболее распространенные причины, из-за которых модуль не может успешно завершить импорт в службу автоматизации Azure.

* Структура не соответствует той, которая требуется для службы автоматизации.
* Модуль зависит от другого модуля, который не был развернут в учетной записи службы автоматизации.
* В папке отсутствуют зависимости модуля.
* Командлет `New-AzureRmAutomationModule` используется для передачи модуля, при этом полный путь для хранения не указан или модуль не загружен с помощью общедоступного URL-адреса.

#### <a name="resolution"></a>Решение

Эту проблему можно устранить одним из следующих способов.

* Убедитесь, что модуль следует следующему формату: ModuleName.sip **->** ModuleName или номер **->** версии (ModuleName.psm1, ModuleName.psd1)
* Откройте файл PSD1 и проверьте, есть ли у модуля зависимости. Если зависимости есть, отправьте эти модули в учетную запись службы автоматизации.
* Убедитесь, что все указанные библиотеки DLL находятся в папке модуля.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Сценарий: Обновление-AzureModule.ps1 приостанавливается при обновлении модулей

#### <a name="issue"></a>Проблема

Процесс обновления приостанавливается при использовании runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) для обновления модулей Azure.

#### <a name="cause"></a>Причина

Значение по умолчанию для определения количества одновременно обновляемых модулей — 10 при использовании сценария `Update-AzureModule.ps1`. Процесс обновления подвержен ошибкам, если одновременно обновляются слишком много модулей.

#### <a name="resolution"></a>Решение

Очень редко все модули AzureRM требуются в одной учетной записи службы автоматизации. Мы рекомендуем импортировать только необходимые модули AzureRM.

> [!NOTE]
> Следует избегать импортирования модуля **AzureRM**. При импорте модулей **AzureRM** импортируются все модули **AzureRM.\***. Это не рекомендуется.

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Сценарий: Вы не можете создать или обновить учетную запись Run As

#### <a name="issue"></a>Проблема

При попытке создать или обновить учетную запись запуска от имени, появляется сообщение об ошибке, подобное приведенному ниже.

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Причина

У вас нет разрешений, необходимых для создания или обновления учетной записи запуска от имени, или ресурс заблокирован на уровне группы ресурсов.

#### <a name="resolution"></a>Решение

Чтобы создать или обновить учетную запись запуска от имени, необходимо иметь соответствующие разрешения на различные ресурсы, используемые для запуска. Дополнительные сведения о разрешениях, необходимых для создания или обновления учетной записи запуска от имени, см. в разделе [Разрешения для запуска от имени учетной записи](../manage-runas-account.md#permissions).

Если проблема связана с блокировкой, убедитесь, что ее можно удалить. Затем перейдите к ресурсу, который заблокирован, щелкните блокировку правой кнопкой мыши и выберите **Удалить**, чтобы снять блокировку.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Сценарий: Вы получаете ошибку "Не удается найти точку входа под названием 'GetPerAdapterInfo' в DLL 'iplpapi.dll'" при выполнения runbook.

#### <a name="issue"></a>Проблема

При выполнении runbook вы получаете следующее исключение:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Причина

Эта ошибка, скорее всего, вызвана неправильно настроенной [учетной записью Run As.](../manage-runas-account.md)

#### <a name="resolution"></a>Решение

Убедитесь, что ваша [учетная запись Run As](../manage-runas-account.md) правильно настроена. После правильной настройки убедитесь, что в runbook есть соответствующий код для проверки подлинности с помощью Azure. В следующем примере показан фрагмент кода для проверки подлинности В журнале Azure с помощью учетной записи Run As.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Связь [@AzureSupport](https://twitter.com/azuresupport) с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**
