---
title: Вопросы и ответы по службе автоматизации Azure | Документация Майкрософт
description: Ответы на часто задаваемые вопросы о службе автоматизации Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405962"
---
# <a name="azure-automation-frequently-asked-questions"></a>Часто задаваемые вопросы о службе автоматизации Azure

В этой статье часто задаваемые вопросы о службе автоматизации Azure. Если у вас есть дополнительные вопросы о возможностях, перейдите на дискуссионный форум и опубликуйте свои вопросы. Если вопрос задается часто, мы добавим его в эту статью, чтобы его можно было найти быстро и легко.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="update-management-solution"></a>Update Management solution (Решение для управления обновлениями)

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Можно ли предотвратить непредвиденные обновления на уровне ОС?

В некоторых вариантах Linux, таких как Red Hat Enterprise Linux, обновление на уровне ОС может происходить через пакеты. Это может привести к Управление обновлениямим запусков, в которых изменяется номер версии ОС. Поскольку Управление обновлениями использует те же методы для обновления пакетов, которые администратор использует локально на компьютере Linux, это поведение преднамерено.

Чтобы избежать обновления версии ОС с помощью Управление обновлениями развертываний, используйте функцию **исключения** .

В Red Hat Enterprise Linux имя пакета для исключения — `redhat-release-server.x86_64`.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Почему не применяются критические обновления и исправления безопасности?

При развертывании обновлений на компьютер Linux вы можете выбрать категории обновлений. Этот параметр фильтрует обновления, соответствующие указанным критериям. Этот фильтр применяется локально на компьютере при развертывании обновления.

Поскольку Управление обновлениями выполняет обогащение обновлений в облаке, можно помечать некоторые обновления в Управление обновлениями, что влияет на безопасность, даже если на локальном компьютере нет этих сведений. При применении критических обновлений на компьютере Linux могут возникать обновления, не помеченные как имеющие влияние на систему безопасности на этом компьютере и поэтому не применяются. Однако Управление обновлениями может сообщить о том, что компьютер не соответствует требованиям, так как он содержит дополнительные сведения о соответствующем обновлении.

Развертывание обновлений по классификации обновлений не работает в RTM версий CentOS. Чтобы правильно развернуть обновления для CentOS, выберите все классификации, чтобы убедиться, что обновления применены. Для SUSE, выбрав только **другие обновления** , так как классификация может привести к установке некоторых дополнительных обновлений безопасности, если обновления безопасности, связанные с zypper (диспетчером пакетов) или его зависимостями, требуются первыми. Это поведение является ограничением zypper. В некоторых случаях может потребоваться перезапустить развертывание обновления, а затем проверить развертывание с помощью журнала обновлений.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Можно ли развертывать обновления между клиентами Azure?

Если у вас есть компьютеры, для которых требуется исправление в другом клиенте Azure Reporting, чтобы Управление обновлениями, необходимо использовать приведенное ниже решение, чтобы получить расписание. Для создания расписания можно использовать командлет [New-азаутоматионсчедуле](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) с `ForUpdateConfiguration` указанным параметром. Можно использовать командлет [New-азаутоматионсофтвареупдатеконфигуратион](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) и передать компьютеры в другом клиенте в `NonAzureComputer` параметр. В приведенном ниже примере показано, как это сделать.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Дальнейшие шаги

Если вы не ответили на этот вопрос, вы можете обратиться к следующим источникам для получения дополнительных вопросов и ответов.

- [Служба автоматизации Azure](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [Форум отзывов и предложений](https://feedback.azure.com/forums/905242-update-management)
