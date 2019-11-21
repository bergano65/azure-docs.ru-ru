---
title: Коллекции модулей Runbook и других модулей для службы автоматизации Azure
description: Вы можете устанавливать и использовать в своей среде службы автоматизации Azure модули Runbook и другие модули, созданные корпорацией Майкрософт и сообществом.  В этой статье описаны способы доступа к этим ресурсам и процесс добавления ваших модулей Runbook в коллекцию.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 86eebf33f870780871e4c873936e491772c73b63
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231610"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Коллекции модулей Runbook и других модулей для службы автоматизации Azure

Вы можете не создавать собственные модули Runbook и другие модули в службе автоматизации Azure, а воспользоваться сценариями, уже созданными корпорацией Майкрософт и сообществом.

You can get PowerShell runbooks and [modules](#modules-in-powershell-gallery) from the PowerShell Gallery and [Python runbooks](#python-runbooks) from the Script Center Gallery. You can also contribute to the community by sharing scenarios that you develop, see Adding a runbook to the gallery

## <a name="runbooks-in-powershell-gallery"></a>Runbooks in PowerShell Gallery

The [PowerShell Gallery](https://www.powershellgallery.com/packages) provides a variety of runbooks from Microsoft and the community that you can import into Azure Automation. To use one, download a runbook from the gallery, or you can directly import runbooks from the gallery, or from your Automation Account in the Azure portal.

You can only import directly from the PowerShell Gallery using the Azure portal. You cannot perform this function using PowerShell.

> [!NOTE]
> You should validate the contents of any runbooks that you get from the PowerShell Gallery and use extreme caution in installing and running them in a production environment.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>To import a PowerShell runbook from the Runbook Gallery with the Azure portal

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. В разделе **Автоматизация процессов** выберите пункт **Коллекция модулей Runbook**.
3. Select **Source: PowerShell Gallery**.
4. Найдите нужный элемент коллекции и выберите его, чтобы просмотреть сведения о нем. Слева вы можете ввести дополнительные параметры для издателя и типа.

   ![Обзор коллекции](media/automation-runbook-gallery/browse-gallery.png)

5. Щелкните **Просмотреть исходный проект** , чтобы открыть соответствующий элемент в [центре сценариев TechNet](https://gallery.technet.microsoft.com/).
6. Чтобы импортировать элемент, щелкните его, а затем в окне просмотра сведений нажмите кнопку **Импорт** .

   ![Кнопка «Импорт»](media/automation-runbook-gallery/gallery-item-detail.png)

7. Также для импорта модуля Runbook вы можете изменить его имя, а затем нажать кнопку **ОК** .
8. Модуль runbook появится на вкладке **Модули runbook** учетной записи службы автоматизации.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Adding a PowerShell runbook to the gallery

Microsoft encourages you to add runbooks to the PowerShell Gallery that you think would be useful to other customers. The PowerShell Gallery accepts PowerShell modules and PowerShell scripts. You can add a runbook by [uploading it to the PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Graphical runbooks are not supported in PowerShell Gallery.

## <a name="modules-in-powershell-gallery"></a>Модули в коллекции PowerShell

Модули PowerShell содержат командлеты, которые можно использовать в модулях Runbook, а в [коллекции PowerShell](https://www.powershellgallery.com) собраны готовые модули, которые можно установить в службе автоматизации Azure. Вы можете открыть эту коллекцию на портале Azure и установить модули из нее непосредственно в службе автоматизации Azure. Вы также можете скачать модули для установки вручную.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Импорт модуля из коллекции модулей службы автоматизации с помощью портала Azure

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Выберите **Модули** в разделе **Общие ресурсы** чтобы открыть список модулей.
3. В верхней части страницы щелкните **Обзор коллекции**.

   ![Коллекция модулей](media/automation-runbook-gallery/modules-blade.png)

4. На странице **Обзор коллекции** можно выполнить поиск по следующим полям:

   * Имя модуля
   * Теги
   * Автор
   * Командлет или имя ресурса DSC

5. Найдите интересующий вас модуль и выберите его, чтобы просмотреть сведения.

   Во время подробного рассмотрения конкретного модуля просмотрите дополнительную информацию. Данная информация включает ссылку на коллекцию PowerShell, все необходимые зависимости и все командлеты или ресурсы DSC, которые содержит модуль.

   ![Сведения о модуле PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Чтобы установить модуль непосредственно в службу автоматизации Azure, нажмите кнопку **Импорт** .
7. Когда на панели **Импорт** вы нажмете кнопку "Импорт", вы увидите имя импортируемого модуля. Если установлены все зависимости, кнопка **ОК** будет активна. Если какие-либо зависимости отсутствуют, их необходимо будет импортировать перед импортом данного модуля.
8. На странице **импорта** нажмите кнопку **ОК**, чтобы импортировать модуль. Когда служба автоматизации Azure импортирует модуль в вашу учетную запись, она извлекает метаданные о модуле и командлетах. Это действие может занять несколько минут, так как каждое действие необходимо извлечь.
9. Вы получите два уведомления о начале и завершении развертывания модуля соответственно.
10. Доступные действия появятся после импорта модуля. Его ресурсы можно использовать в Runbook и в настройке требуемого состояния.

> [!NOTE]
> Модули, которые поддерживают только PowerShell Core, не поддерживаются в службе автоматизации Azure. Их нельзя импортировать на портале Azure и развернуть непосредственно из коллекции PowerShell.

## <a name="python-runbooks"></a>Модули runbook Python

Модули runbook Python доступны в [ коллекции центра сценариев](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). You can contribute Python runbooks to the Script Center gallery by clicking **Upload a contribution**. При отправке материалов убедитесь, что вы добавили тег **Python**.

> [!NOTE]
> In order to upload content to [Script Center](https://gallery.technet.microsoft.com/scriptcenter) a minimum of 100 points is required.

## <a name="requesting-a-runbook-or-module"></a>Запрос на создание модуля Runbook или другого модуля

Свой запрос можно отправить на сайте [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Если вам нужна помощь в написании модуля Runbook или освоении PowerShell, опубликуйте вопрос на нашем [форуме](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="common-solutions-available-in-the-runbook-gallery"></a>Common solutions available in the runbook gallery

The list below contains a few runbooks that provide solutions to common scenarios. For a full list of runbooks created by the Azure Automation team, see [AzureAutomationTeam profile](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

* [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - Imports the latest version on PowerShell Gallery of all modules in an Automation account.
* [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - This script configures Azure Diagnostics and Log Analytics to receive Azure Automation logs containing job status and job streams.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - This runbook copies a remote file from a Windows Azure virtual machine.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - This runbook copies a local file to an Azure virtual machine.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о том, как начать работу с модулями Runbook, см. в статье [Управление модулями runbook в службе автоматизации Azure](manage-runbooks.md).
* Чтобы понять различия между PowerShell и рабочим процессом PowerShell с модулями Runbook, см. статью [Изучение рабочего процесса Windows PowerShell](automation-powershell-workflow.md).
* For more information on PowerShell, including language reference and learning modules, refer to the [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
