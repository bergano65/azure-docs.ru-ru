---
title: Обновление модулей Azure в службе автоматизации Azure
description: В этой статье описывается, как можно обновить общие модули Azure PowerShell, предоставленные по умолчанию в службе автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 06/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 76514e620f044b78b992db2b88733e69dbabf135
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850641"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Как обновить модули Azure PowerShell в службе автоматизации Azure

Чтобы обновить модули Azure в учетной записи службы автоматизации, необходимо использовать [модуль обновления Runbook модулей Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), доступный в виде открытого кода. Чтобы приступить к использованию модуля runbook **Update-AutomationAzureModulesForAccount** для обновления модулей Azure, скачайте его из [этого репозитория](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) на сайте GitHub. После этого вы сможете импортировать его в свою учетную запись службы автоматизации или выполнить в качестве скрипта. Сведения о том, как импортировать модуль Runbook в учетную запись службы автоматизации, см. в разделе [Импорт модуля Runbook](manage-runbooks.md#import-a-runbook).

Наиболее распространенные модули AzureRM PowerShell предоставляются по умолчанию в каждой учетной записи службы автоматизации. Команда Azure регулярно обновляет модули Azure, поэтому для обновления модулей в учетных записях службы автоматизации необходимо использовать модуль Runbook [Update-аутоматионазуремодулесфораккаунт](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) .

Так как модули регулярно обновляются специалистами группы продуктов, то изменения могут затронуть и входящие в них командлеты. Данное действие может негативно повлиять на работу модулей Runbook. Это зависит от типа изменения. Например, возможно переименование параметра или полное прекращение поддержки командлета.

Чтобы избежать негативного влияния на модули Runbook и автоматизируемые ими процессы, выполните предварительное тестирование и проверку. Если у вас нет выделенной учетной записи службы автоматизации, предназначенной для этой цели, то рекомендуется ее создать, чтобы протестировать различные сценарии в процессе разработки модулей Runbook. Тестирование должно включать повторяющиеся изменения, такие как обновление модулей PowerShell.

Если вы разрабатываете скрипты локально, при тестировании рекомендуется хранить локально те же версии модуля, которые доступны в вашей учетной записи службы автоматизации. Так вы гарантированно получите те же результаты. После проверки результатов и применения необходимых изменений можно перейти к процессу переноса их в рабочую среду.

> [!NOTE]
> Новая учетная запись службы автоматизации может не содержать последние модули.

> [!NOTE]
> Вы не сможете удалять глобальные модули — модули, которые предоставляет Автоматизация.

## <a name="considerations"></a>Рекомендации

Ниже приведены некоторые рекомендации по использованию этого процесса для обновления модулей Azure.

* Этот модуль Runbook поддерживает обновление модулей **Azure** и **AzureRm** по умолчанию. Этот модуль Runbook также поддерживает обновление модулей **AZ** . Дополнительные сведения об обновлении модулей `Az` с помощью этого модуля Runbook см. в [файле readme для обновления модулей Azure Runbook](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) . При использовании модулей `Az` в учетной записи службы автоматизации необходимо учитывать дополнительные важные факторы. Дополнительные сведения см. в разделе [Использование модулей az в учетной записи службы автоматизации](az-modules.md).

* Перед запуском этого модуля runbook убедитесь, что для вашей учетной записи службы автоматизации созданы [учетные данные для учетной записи запуска от имени Azure](manage-runas-account.md).

* Этот код можно использовать как обычный сценарий PowerShell вместо модуля Runbook. просто войдите в Azure с помощью команды [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) , а затем передайте `-Login $false` скрипту.

* Если вы хотите выполнять этот модуль runbook в национальных облаках, используйте параметр `AzureRmEnvironment`, чтобы передать в модуль runbook нужную среду.  Приемлемые значения: **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** и **AzureUSGovernment**. Эти значения можно получить с помощью командлета `Get-AzureRmEnvironment | select Name`. Если не передать значение этому параметру, модуль Runbook по умолчанию будет использовать общедоступное облако Azure **AzureCloud**.

* Если нужно использовать конкретную версию модуля Azure PowerShell вместо последней доступной в коллекции PowerShell, передайте эти версии в необязательном параметре `ModuleVersionOverrides` модуля runbook **Update-AutomationAzureModulesForAccount**. Примеры см. в модуле runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). Модули Azure PowerShell, которые не упомянуты в параметре `ModuleVersionOverrides`, обновляются до последних версий из коллекции PowerShell. Если в параметр `ModuleVersionOverrides` не передано ничего, все модули обновляются последними версиями из коллекции PowerShell, как при нажатии кнопки **Обновить модули Azure**.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. на странице [модуля runbook с открытым кодом для обновления модулей Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).
