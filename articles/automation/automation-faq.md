---
title: Вопросы и ответы по службе автоматизации Azure | Документация Майкрософт
description: Ответы на часто задаваемые вопросы о службе автоматизации Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925817"
---
# <a name="azure-automation-frequently-asked-questions"></a>Часто задаваемые вопросы о службе автоматизации Azure

В этой статье часто задаваемые вопросы о службе автоматизации Azure. Если у вас есть дополнительные вопросы о возможностях, перейдите на дискуссионный форум и опубликуйте свои вопросы. Если вопрос задается часто, мы добавим его в эту статью, чтобы его можно было найти быстро и легко.

## <a name="update-management-solution"></a>Управление обновлениями решение

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Можно ли предотвратить непредвиденные обновления на уровне ОС?

В некоторых вариантах Linux, таких как Red Hat Enterprise Linux, обновление на уровне ОС может происходить через пакеты. Это может привести к запуску управления обновлениями, когда изменяется номер версии операционной системы. Поскольку Управление обновлениями использует те же методы для обновления пакетов, которые администратор локально использует на компьютере Linux, это поведение является намеренным.

Чтобы избежать обновления версии ОС с помощью Управление обновлениями развертываний, используйте функцию **исключения** .

В Red Hat Enterprise Linux именем пакета для исключения должно быть имя redhat-release-server.x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Почему не применяются критические обновления и исправления безопасности?

При развертывании обновлений на компьютер Linux вы можете выбрать категории обновлений. Этот параметр фильтрует обновления, примененные к компьютеру, который соответствует указанным критериям. Этот фильтр применяется локально на компьютере при развертывании обновления.

Поскольку Управление обновлениями выполняет обогащение обновлений в облаке, некоторые обновления могут помечаться в Управление обновлениями, что влияет на безопасность, даже если на локальном компьютере нет этих сведений. В результате, если вы применяете критические обновления к компьютеру Linux, могут возникать обновления, не помеченные как имеющие влияние на систему безопасности на этом компьютере, поэтому обновления не применяются. Тем не менее Управление обновлениями может сообщить о том, что компьютер не соответствует требованиям, так как он содержит дополнительные сведения о соответствующем обновлении.

Развертывание обновлений по классификации обновлений не работает в RTM версий CentOS. Чтобы правильно развернуть обновления для CentOS, выберите все классификации, чтобы убедиться, что обновления применены. Для SUSE, выбрав *только* **другие обновления** в качестве классификации, можно также установить некоторые обновления безопасности, если обновления безопасности, связанные с zypper (диспетчером пакетов) или его зависимостями, требуются первыми. Это поведение является ограничением zypper. В некоторых случаях может потребоваться перезапустить развертывание обновлений. Для проверки обратитесь к журналу обновлений.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Можно ли развертывать обновления между клиентами Azure?

Если у вас есть компьютеры в другом клиенте Azure, которые подготавливает отчеты к Управление обновлениями, которые необходимо исправить, для их планирования необходимо использовать следующее решение. Вы можете использовать командлет [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) с параметром `-ForUpdate`, чтобы создать расписание, и использовать командлет [New-азурермаутоматионсофтвареупдатеконфигуратион](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) и передать компьютеры в другом клиенте в параметр `-NonAzureComputer`. В приведенном ниже примере показано, как это сделать.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Следующие шаги

Если вы не ответили на этот вопрос, вы можете обратиться к следующему Форуму для получения дополнительных вопросов и ответов.

- [Служба автоматизации Azure](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Для получения общих отзывов о Управление обновлениями решении посетите [Форум обратной связи](https://feedback.azure.com/forums/905242-update-management).