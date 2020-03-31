---
title: Обновление модулей Azure в службе автоматизации Azure
description: В этой статье описывается, как можно обновить общие модули Azure PowerShell, предоставленные по умолчанию в службе автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3d7eaae452f307b350c111452b819576cf7f17e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420487"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Как обновить модули Azure PowerShell в службе автоматизации Azure

Для обновления модулей Azure в учетной записи автоматизации необходимо использовать [runbook модулей Обновления Azure,](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)который доступен в виде открытого исходного кода. Чтобы приступить к использованию модуля runbook **Update-AutomationAzureModulesForAccount** для обновления модулей Azure, скачайте его из [этого репозитория](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) на сайте GitHub. После этого вы сможете импортировать его в свою учетную запись службы автоматизации или выполнить в качестве скрипта. Чтобы узнать, как импортировать книгу в вашем аккаунте автоматизации, [см.](manage-runbooks.md#import-a-runbook)

Наиболее распространенные модули AzureRM PowerShell предоставляются по умолчанию в каждой учетной записи автоматизации. Команда Azure регулярно обновляет модули Azure, поэтому для обновления учетных записей [«Обновление-AutomationAzureModulesForAccount»](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) для обновления модулей в учетных записях автоматизации необходимо использовать runbook Update-Automation.

Так как модули регулярно обновляются специалистами группы продуктов, то изменения могут затронуть и входящие в них командлеты. Данное действие может негативно повлиять на работу модулей Runbook. Это зависит от типа изменения. Например, возможно переименование параметра или полное прекращение поддержки командлета.

Чтобы избежать негативного влияния на модули Runbook и автоматизируемые ими процессы, выполните предварительное тестирование и проверку. Если у вас нет выделенной учетной записи службы автоматизации, предназначенной для этой цели, то рекомендуется ее создать, чтобы протестировать различные сценарии в процессе разработки модулей Runbook. Тестирование должно включать повторяющиеся изменения, такие как обновление модулей PowerShell.

Если вы разрабатываете скрипты локально, при тестировании рекомендуется хранить локально те же версии модуля, которые доступны в вашей учетной записи службы автоматизации. Так вы гарантированно получите те же результаты. После проверки результатов и применения необходимых изменений можно перейти к процессу переноса их в рабочую среду.

> [!NOTE]
> Новая учетная запись службы автоматизации может не содержать последние модули.

> [!NOTE]
> Вы не сможете удалить глобальные модули - модули, которые автоматизация предоставляет из коробки.

## <a name="considerations"></a>Рекомендации

Ниже приведены некоторые рекомендации по использованию этого процесса для обновления модулей Azure.

* Этот runbook поддерживает обновление модулей **Azure** и **AzureRm** по умолчанию. Этот runbook поддерживает обновление модулей **Az,** а также. Просмотрите [runbook модулей Обновления Azure,](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) чтобы `Az` узнать больше об обновлении модулей с помощью этого runbook. Есть дополнительные важные факторы, которые необходимо `Az` учитывать при использовании модулей в вашем аккаунте автоматизации, чтобы узнать больше, см. [Using Az modules in your Automation Account](az-modules.md)

* Перед запуском этого модуля runbook убедитесь, что для вашей учетной записи службы автоматизации созданы [учетные данные для учетной записи запуска от имени Azure](manage-runas-account.md).

* Вы можете использовать этот код в качестве обычного скрипта PowerShell вместо runbook: просто войдите в Azure, используя команду [Connect-AzureRmAccount,](/powershell/module/azurerm.profile/connect-azurermaccount) а затем перейдите `-Login $false` к скрипту.

* Если вы хотите выполнять этот модуль runbook в национальных облаках, используйте параметр `AzureRmEnvironment`, чтобы передать в модуль runbook нужную среду.  Приемлемые значения: **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** и **AzureUSGovernment**. Эти значения можно получить с помощью командлета `Get-AzureRmEnvironment | select Name`. Если не передать значение этому параметру, модуль Runbook по умолчанию будет использовать общедоступное облако Azure **AzureCloud**.

* Если нужно использовать конкретную версию модуля Azure PowerShell вместо последней доступной в коллекции PowerShell, передайте эти версии в необязательном параметре `ModuleVersionOverrides` модуля runbook **Update-AutomationAzureModulesForAccount**. Примеры см. в модуле runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). Модули Azure PowerShell, которые не упомянуты в параметре `ModuleVersionOverrides`, обновляются до последних версий из коллекции PowerShell. Если в параметр `ModuleVersionOverrides` не передано ничего, все модули обновляются последними версиями из коллекции PowerShell, как при нажатии кнопки **Обновить модули Azure**.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. на странице [модуля runbook с открытым кодом для обновления модулей Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).
