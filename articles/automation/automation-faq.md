---
title: Часто задаваемые вопросы по автоматизации Лазурного средства (ru) Документы Майкрософт
description: Ответы на часто задаваемые вопросы об автоматизации Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405962"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation часто задавала вопросы

Этот часто задаваемые вопросы Майкрософт представляет собой список часто задаваемых вопросов об автоматизации Azure. Если у вас есть какие-либо дополнительные вопросы о его возможностях, перейдите на дискуссионный форум и опубликуйте свои вопросы. Если вопрос задается часто, мы добавим его в эту статью, чтобы его можно было найти быстро и легко.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="update-management-solution"></a>Update Management solution (Решение для управления обновлениями)

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Можно ли предотвратить неожиданные обновления уровня ОС?

В некоторых вариантах Linux, таких как Red Hat Enterprise Linux, обновления на уровне ОС могут происходить через пакеты. Это может привести к запуску update Management, в которых изменяется число версий ОС. Поскольку управление обновлениями использует те же методы для обновления пакетов, которые администратор использует локально на машине Linux, такое поведение является преднамеренным.

Чтобы избежать обновления версии ОС с помощью развертывания Управления обновлениями, используйте функцию **исключения.**

В Red Hat Enterprise Linux, название `redhat-release-server.x86_64`пакета, чтобы исключить это .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Почему не применяются критические обновления/обновления безопасности?

При развертывании обновлений на компьютер Linux вы можете выбрать категории обновлений. Этот параметр фильтрует обновления, отвечающие указанным критериям. Этот фильтр применяется локально на компьютере при развертывании обновления.

Поскольку обновление Management выполняет обновление в облаке, вы можете отметить некоторые обновления в управлении обновлением как имеющие влияние на безопасность, даже если местная машина не имеет этой информации. Если применить критические обновления к машине Linux, могут быть обновления, которые не помечены как имеющие влияние на безопасность на эту машину и, следовательно, не применяются. Тем не менее, управление обновлением может по-прежнему сообщать, что машина не соответствует требованиям, поскольку она имеет дополнительную информацию о соответствующем обновлении.

Развертывание обновлений по классификации обновлений не работает на версиях RTM CentOS. Чтобы правильно развернуть обновления для CentOS, выберите все классификации, чтобы убедиться, что обновления применяются. Для SUSE, выбрав только **другие обновления,** как классификация может привести к некоторым дополнительным обновлениям безопасности, которые будут установлены, если обновления безопасности, связанные с zypper (менеджер пакетов) или его зависимостей требуется в первую очередь. Это поведение является ограничением zypper. В некоторых случаях может потребоваться повторное выполнение развертывания обновлений, а затем проверка развертывания через журнал обновления.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Можно ли развернуть обновления в разных клиентах Azure?

Если у вас есть машины, которые нуждаются в исправлении в другой компании, сообщающей об этом арендатору Azure для управления обновлениями, необходимо использовать следующий обходной путь, чтобы заставить их запланировали. Для создания расписания можно использовать cmdlet `ForUpdateConfiguration` [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) с указанным параметром. Вы можете использовать смдлет [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) и передать машины `NonAzureComputer` в другом арендаторе по параметру. В приведенном ниже примере показано, как это сделать.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Дальнейшие действия

Если на ваш вопрос не ответят здесь, вы можете обратиться к следующим источникам для получения дополнительных вопросов и ответов.

- [Служба автоматизации Azure](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [Форум отзывов и предложений](https://feedback.azure.com/forums/905242-update-management)
