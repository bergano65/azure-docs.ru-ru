---
title: Часто задаваемые вопросы по автоматизации Лазурного средства (ru) Документы Майкрософт
description: Ответы на часто задаваемые вопросы об автоматизации Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925817"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation часто задавала вопросы

Этот часто задаваемые вопросы Майкрософт представляет собой список часто задаваемых вопросов об автоматизации Azure. Если у вас есть какие-либо дополнительные вопросы о его возможностях, перейдите на дискуссионный форум и опубликуйте свои вопросы. Если вопрос задается часто, мы добавим его в эту статью, чтобы его можно было найти быстро и легко.

## <a name="update-management-solution"></a>Update Management solution (Решение для управления обновлениями)

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Можно ли предотвратить неожиданные обновления уровня ОС?

В некоторых вариантах Linux, таких как Red Hat Enterprise Linux, обновления на уровне ОС могут происходить через пакеты. Это может привести к запуску управления обновлениями, когда изменяется номер версии операционной системы. Поскольку управление обновлениями использует те же методы для обновления пакетов, которые администратор будет использовать локально на машине Linux, такое поведение является преднамеренным.

Чтобы избежать обновления версии ОС с помощью развертывания Управления обновлениями, используйте функцию **исключения.**

В Red Hat Enterprise Linux именем пакета для исключения должно быть имя redhat-release-server.x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Почему не применяются критические обновления/обновления безопасности?

При развертывании обновлений на компьютер Linux вы можете выбрать категории обновлений. Эта опция фильтрует обновления, которые применяются к машине, которые отвечают указанным критериям. Этот фильтр применяется локально на компьютере при развертывании обновления.

Поскольку обновление Management выполняет обновление в облаке, некоторые обновления могут быть помечены в управлении обновлением как имеющие влияние на безопасность, даже если местная машина не имеет этой информации. В результате, если применить критические обновления к машине Linux, могут быть обновления, которые не помечены как имеющие влияние на безопасность на эту машину, и поэтому обновления не применяются. Тем не менее, управление обновлением может по-прежнему сообщать, что машина не соответствует требованиям, поскольку она имеет дополнительную информацию о соответствующем обновлении.

Развертывание обновлений по классификации обновлений не работает на версиях RTM CentOS. Чтобы правильно развернуть обновления для CentOS, выберите все классификации, чтобы убедиться, что обновления применяются. Для SUSE, выбирая *только* **другие обновления,** как классификация может привести к некоторым обновлениям безопасности, которые также будут установлены, если обновления безопасности, связанные с zypper (менеджер пакетов) или его зависимостей требуется в первую очередь. Это поведение является ограничением zypper. В некоторых случаях может потребоваться повторное развертывание обновления. Для проверки обратитесь к журналу обновлений.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Можно ли развернуть обновления в разных клиентах Azure?

Если у вас есть машины в другом арендаторе Azure, сообщающие об управлении обновлениями, которые необходимо исправления, вам придется использовать следующие обходные пути, чтобы получить их запланировано. Вы можете использовать смдлет [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) с `-ForUpdate` коммутатором для создания расписания, а также использовать смдлет [New-AzureRmAutomationSoftwareUpdate Configuration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) и передать машины в другом арендаторе по параметру. `-NonAzureComputer` В приведенном ниже примере показано, как это сделать.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Дальнейшие действия

Если на ваш вопрос не ответили здесь, вы можете обратиться к следующему форуму для получения дополнительных вопросов и ответов.

- [Автоматизация Azure](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Для получения общей обратной связи о решении Update Management, пожалуйста, посетите [форум обратной связи](https://feedback.azure.com/forums/905242-update-management).