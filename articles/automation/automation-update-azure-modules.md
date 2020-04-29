---
title: Обновление модулей Azure PowerShell в службе автоматизации Azure
description: В этой статье описывается, как можно обновить общие модули Azure PowerShell, предоставленные по умолчанию в службе автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: bb73b3d644e96f9596f887faaf62eb15f01956ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81769670"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>Обновление модулей Azure PowerShell в службе автоматизации Azure

Чтобы обновить модули Azure в учетной записи службы автоматизации, необходимо использовать [модуль обновления Runbook модулей Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), доступный в виде открытого кода. Чтобы приступить к использованию модуля runbook **Update-AutomationAzureModulesForAccount** для обновления модулей Azure, скачайте его из [этого репозитория](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) на сайте GitHub. Затем его можно импортировать в учетную запись службы автоматизации или запустить в качестве скрипта. Сведения о том, как импортировать модуль Runbook в учетную запись службы автоматизации, см. в разделе [Импорт модуля Runbook](manage-runbooks.md#importing-a-runbook).

Наиболее распространенные модули PowerShell предоставляются по умолчанию в каждой учетной записи службы автоматизации. Группа разработчиков Azure регулярно обновляет модули Azure. Таким образом, чтобы обновлять модули в учетных записях службы автоматизации, следует использовать модуль Runbook [Update-аутоматионазуремодулесфораккаунт](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) .

Так как модули регулярно обновляются специалистами группы продуктов, то изменения могут затронуть и входящие в них командлеты. Эти изменения, например, Переименование параметра или нерекомендуемый командлет полностью, могут негативно повлиять на модули Runbook.

Чтобы избежать негативного влияния на модули Runbook и автоматизируемые ими процессы, выполните предварительное тестирование и проверку. Если у вас нет выделенной учетной записи службы автоматизации, предназначенной для этой цели, то рекомендуется ее создать, чтобы протестировать различные сценарии в процессе разработки модулей Runbook. Это тестирование должно включать итеративные изменения, например обновление модулей PowerShell.

Если скрипты разрабатываются локально, то при тестировании рекомендуется иметь те же версии модулей, что и в учетной записи службы автоматизации, чтобы гарантировать получение тех же результатов. После проверки результатов и применения необходимых изменений можно перейти к процессу переноса их в рабочую среду.

> [!NOTE]
> Новая учетная запись службы автоматизации может не содержать последние модули.

> [!NOTE]
> Вы не сможете удалять глобальные модули, которые являются модулями, предоставляющими автоматизацию.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). 

## <a name="considerations"></a>Рекомендации

Ниже приведены некоторые рекомендации, которые следует учитывать при использовании этой статьи для обновления модулей Azure.

* Модуль Runbook, описанный в этой статье, поддерживает обновление модулей Azure, AzureRM и AZ по умолчанию. Дополнительные сведения об обновлении модулей AZ. Automation с помощью этого модуля см. в [файле readme для обновления модулей Azure в модуле](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) Runbook. При использовании модулей az в учетной записи службы автоматизации необходимо учитывать дополнительные важные факторы. Дополнительные сведения см. в статье [Управление модулями в службе автоматизации Azure](shared-resources/modules.md).

* Перед запуском этого модуля runbook убедитесь, что для вашей учетной записи службы автоматизации созданы [учетные данные для учетной записи запуска от имени Azure](manage-runas-account.md).

* Этот код можно использовать как обычный сценарий PowerShell вместо модуля Runbook. просто войдите в Azure с помощью командлета [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) , а затем передайте `-Login $false` его в скрипт.

* Если вы хотите выполнять этот модуль runbook в национальных облаках, используйте параметр `AzEnvironment`, чтобы передать в модуль runbook нужную среду.  Допустимые значения: AzureCloud (общедоступное облако Azure), AzureChinaCloud, Азурежерманклауд и AzureUSGovernment. Эти значения можно получить с помощью `Get-AzEnvironment | select Name`. Если вы не передаете значение этому командлету, по умолчанию Runbook будет AzureCloud.

* Если вы хотите использовать определенную версию модуля Azure PowerShell вместо последнего модуля, доступного в коллекция PowerShell, передайте эти версии в необязательный `ModuleVersionOverrides` параметр модуля Runbook **Update-аутоматионазуремодулесфораккаунт** . Примеры см. в модуле runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). Модули Azure PowerShell, которые не упомянуты в параметре `ModuleVersionOverrides`, обновляются до последних версий из коллекции PowerShell. Если в параметр `ModuleVersionOverrides` не передано ничего, все модули обновляются последними версиями из коллекции PowerShell, как при нажатии кнопки **Обновить модули Azure**.

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения см. на странице [модуля runbook с открытым кодом для обновления модулей Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).
