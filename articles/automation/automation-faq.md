---
title: Часто задаваемые вопросы о службе автоматизации Azure | Документация Майкрософт
description: В этой статье представлены ответы на часто задаваемые вопросы о службе автоматизации Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 76c8d09ef2ef0130ddac856a1f37f8b68d977494
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186237"
---
# <a name="azure-automation-frequently-asked-questions"></a>Часто задаваемые вопросы о службе автоматизации Azure

В этом списке корпорация Майкрософт собрала часто задаваемые вопросы о службе автоматизации Azure. Если у вас есть дополнительные вопросы о возможностях этой службы, задайте их на форуме. Если вопрос задается часто, мы добавим его в эту статью, чтобы его можно было найти быстро и легко.

## <a name="update-management"></a>Управление обновлениями

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Можно ли избежать непредвиденных обновлений на уровне ОС?

В некоторых вариациях Linux, например в Red Hat Enterprise Linux, обновления на уровне ОС могут выполняться через пакеты. Это может привести к тому, что при запуске функции "Управление обновлениями" изменяется номер версии операционной системы. Так как функция "Управление обновлениями" использует те же методы для обновления пакетов, что и администратор на локальном компьютере Linux, такое поведение считается преднамеренным.

Чтобы избежать обновления версий ОС при развертывании функции "Управление обновлениями", примените функцию **исключения**.

В Red Hat Enterprise Linux укажите для исключения пакет с именем `redhat-release-server.x86_64`.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Почему не применяются критические обновления и обновления для системы безопасности?

При развертывании обновлений на компьютер Linux вы можете выбрать категории обновлений. Это позволит отобрать те обновления, которые соответствуют указанным критериям. Этот фильтр применяется локально на компьютере при развертывании обновления.

Так как функция "Управление обновлениями" оптимизирует обновления в облаке, некоторые обновления могут быть помечены как влияющие на безопасность, а на локальном компьютере такие сведения отсутствуют. Когда вы применяете критические обновления для компьютера Linux, некоторые из них не будут на локальном компьютере помечены как влияющие на безопасность, а значит не будут применены. При этом функция "Управление обновлениями" сохранит для компьютера статус не соответствующего требованиям, так как есть дополнительные сведения о необходимых обновлениях.

Развертывание обновлений на основе их классификации не поддерживается в CentOS версий RTM. Чтобы правильно развернуть обновления для CentOS, выберите все классификации для установки всех обновлений. Если для SUSE вы выберете в качестве классификации ТОЛЬКО вариант **Другие обновления**, некоторые обновления системы безопасности все равно будут установлены, если они являются обязательными для работы диспетчера пакетов zypper или его зависимостей. Это поведение является ограничением zypper. В некоторых случаях вам придется повторно запускать развертывание обновлений, а затем проверять развертывание по журналу обновлений.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Можно ли развертывать обновления в другом клиенте Azure?

Если у вас есть в другом клиенте Azure компьютеры для обновления, которые подключены к функции "Управление обновлениями", для включения их в график обновлений вы можете применить следующее обходное решение. Выполните командлет [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) с параметром `ForUpdateConfiguration`, чтобы создать расписание. Можете также применить командлет [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0), передав ему параметр `NonAzureComputer` со списком компьютеров в другом клиенте. В приведенном ниже примере показано, как это сделать.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Дальнейшие действия

Если в этой статье нет ответа на ваш вопрос, обратитесь к следующим источникам информации.

- [Служба автоматизации Azure](/answers/topics/azure-automation.html)
- [Форум отзывов и предложений](https://feedback.azure.com/forums/905242-update-management)
