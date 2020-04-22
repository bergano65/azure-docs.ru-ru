---
title: Обновление модулей Azure PowerShell в автоматизации Azure
description: В этой статье описывается, как можно обновить общие модули Azure PowerShell, предоставленные по умолчанию в службе автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: bb73b3d644e96f9596f887faaf62eb15f01956ab
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769670"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>Обновление модулей Azure PowerShell в автоматизации Azure

Для обновления модулей Azure в учетной записи Automation необходимо использовать [runbook модулей Обновления Azure,](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)который доступен в виде открытого исходного кода. Чтобы приступить к использованию модуля runbook **Update-AutomationAzureModulesForAccount** для обновления модулей Azure, скачайте его из [этого репозитория](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) на сайте GitHub. Затем вы можете импортировать его в свою учетную запись Автоматизация или запустить его в качестве сценария. Чтобы узнать, как импортировать книгу в учетной записи Автоматизация, [см.](manage-runbooks.md#importing-a-runbook)

Наиболее распространенные модули PowerShell предоставляются по умолчанию в каждой учетной записи Автоматизации. Группа разработчиков Azure регулярно обновляет модули Azure. Поэтому, чтобы сохранить модули в ваших учетных записях автоматизации в актуальном состоянии, следует использовать [runbook Update-AutomationAzureModulesForAccount.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)

Так как модули регулярно обновляются специалистами группы продуктов, то изменения могут затронуть и входящие в них командлеты. Эти изменения, например, переименование параметра или полное обезвреживание cmdlet, могут негативно сказаться на ваших runbooks.

Чтобы избежать негативного влияния на модули Runbook и автоматизируемые ими процессы, выполните предварительное тестирование и проверку. Если у вас нет выделенной учетной записи службы автоматизации, предназначенной для этой цели, то рекомендуется ее создать, чтобы протестировать различные сценарии в процессе разработки модулей Runbook. Это тестирование должно включать итеративные изменения, такие как обновление модулей PowerShell.

Если вы разрабатываете свои сценарии локально, рекомендуется иметь те же версии модулей локально, что у вас есть в вашей учетной записи автоматизации при тестировании, чтобы убедиться, что вы получите те же результаты. После проверки результатов и применения необходимых изменений можно перейти к процессу переноса их в рабочую среду.

> [!NOTE]
> Новая учетная запись службы автоматизации может не содержать последние модули.

> [!NOTE]
> Вы не сможете удалить глобальные модули, которые являются модулями, которые автоматизация предоставляет из коробки.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) 

## <a name="considerations"></a>Особенности

Ниже приведены некоторые соображения, которые следует учитывать при использовании этой статьи для обновления модулей Azure:

* В книге runbook, описанной в этой статье, поддерживается обновление модулей Azure, AzureRM и Az по умолчанию. Просмотрите [runbook модулей Обновления Azure,](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) чтобы узнать больше об обновлении модулей Az.Automation с помощью этого runbook. Есть дополнительные важные факторы, которые необходимо учитывать при использовании модулей Az в вашей учетной записи Автоматизация. Чтобы узнать больше, смотрите [модули Управления в Azure Automation](shared-resources/modules.md).

* Перед запуском этого модуля runbook убедитесь, что для вашей учетной записи службы автоматизации созданы [учетные данные для учетной записи запуска от имени Azure](manage-runas-account.md).

* Вы можете использовать этот код в качестве обычного скрипта PowerShell вместо runbook: просто войдите в Azure с помощью cmdlet [Connect-AzAccount,](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) а затем перейдите `-Login $false` к скрипту.

* Если вы хотите выполнять этот модуль runbook в национальных облаках, используйте параметр `AzEnvironment`, чтобы передать в модуль runbook нужную среду.  Приемлемыми значениями являются AzureCloud (облачное облако Azure), AzureChinaCloud, AzureGermanCloud и AzureUSGovernment. Эти значения могут быть `Get-AzEnvironment | select Name`извлечены с помощью . Если вы не передаете значение этому cmdlet, книга выполнения по умолчанию по умолчанию перекладывается на AzureCloud.

* Если вы хотите использовать конкретную версию модуля Azure PowerShell вместо последнего модуля, доступного в галерее PowerShell, передайте эти версии дополнительному `ModuleVersionOverrides` параметру runbook **Update-AutomationAzureModulesForAccount.** Примеры см. в модуле runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). Модули Azure PowerShell, которые не упомянуты в параметре `ModuleVersionOverrides`, обновляются до последних версий из коллекции PowerShell. Если в параметр `ModuleVersionOverrides` не передано ничего, все модули обновляются последними версиями из коллекции PowerShell, как при нажатии кнопки **Обновить модули Azure**.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения см. на странице [модуля runbook с открытым кодом для обновления модулей Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).
